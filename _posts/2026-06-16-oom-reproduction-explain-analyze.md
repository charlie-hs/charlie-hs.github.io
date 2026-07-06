---
title: "[데이터 엔지니어링 실습] OOM 사건을 EXPLAIN ANALYZE로 직접 재현하다 — DISTINCT가 4.29MB JSON을 정렬 키로 끌고 갈 때"
date: 2026-06-16T22:30:00+09:00
categories: Data-Engineering
tags:
  - DataEngineering
  - 데이터엔지니어링
  - PostgreSQL
  - EXPLAIN
  - QueryPlan
  - OOM
  - DISTINCT
  - HashAggregate
  - work_mem
  - temp_file_limit
  - Spill
  - EXISTS
  - Index
  - KeysetPagination
  - 실습
  - SQL튜닝
toc: true
toc_sticky: true
---

> 이 글은 [\[데이터 엔지니어링 진단\] API로만 데이터를 나르다 만난 벽]({% post_url 2026-06-16-data-engineering-diagnosis-synapse %})의 **후속 실습편**이다.
> 앞 글에서 진단한 7가지 통증 중 가장 교훈적이었던 **"통증 3 — wide payload를 정렬/DISTINCT 키로 끌고 감"**(실제 `SYN-7172` 익스포트 OOM 사건)을,
> 격리된 PostgreSQL에 **직접 재현**하고 `EXPLAIN (ANALYZE, BUFFERS)`로 해부한 기록이다.
> 앞 글의 30일 액션 플랜에 적었던 _"OOM 사건을 EXPLAIN ANALYZE로 직접 재현 → 블로그 후속편"_ 을 실행한 것이다.

---

## 0. 왜 "직접 재현"인가

쿼리 플랜을 글로 읽는 것과, **내 손으로 spill을 일으켜 보는 것**은 학습 깊이가 다르다. 데이터 엔지니어링에서 `EXPLAIN ANALYZE`는 청진기다. "느리다 / 메모리가 터진다"는 막연한 증상을, **어느 노드에서, 무엇 때문에, 몇 MB가 디스크로 새는지** 숫자로 보여준다.

원래 사건의 규모는 이랬다.

- 어떤 프로젝트의 `Task` **9.7만 건**, 그리고 **`Task.data`(JSONField) 한 행이 평균 ~4.29MB**.
- 검색 조건이 붙은 익스포트가 워커를 OOM으로 죽였고, `total_count` 추정 쿼리는 temp 파일 한도를 넘겨 abort.

215GB짜리 테이블을 그대로 재현할 순 없다. 대신 **같은 병리를 1/2500 스케일로 축소**해서, 노트북 한 대에서 `EXPLAIN ANALYZE`로 똑같은 증상을 만들어낸다. 핵심은 절대 크기가 아니라 **"무거운 컬럼이 정렬/그룹 키에 들어가면 메모리가 행 폭 × 행 수로 폭발한다"** 는 구조이기 때문이다.

---

## 1. 재현 환경 — 실 DB는 건드리지 않는다

운영/개발 DB(5432)는 절대 건드리지 않고, **`/tmp`에 완전히 격리된 throwaway 클러스터**를 별도 포트(55432)로 띄웠다.

```bash
export PGBIN=/usr/lib/postgresql/17/bin PGDATA=/tmp/pg-oom-demo PGPORT=55432
$PGBIN/initdb -D "$PGDATA" -U demo --no-locale -E UTF8
$PGBIN/pg_ctl -D "$PGDATA" \
  -o "-p $PGPORT -c listen_addresses=127.0.0.1 -c shared_buffers=256MB \
      -c max_parallel_workers_per_gather=0 -c log_temp_files=0" \
  -l /tmp/pg-oom-demo.log start
```

- `max_parallel_workers_per_gather=0` — 병렬 스캔을 꺼서 플랜을 단순·결정적으로 만든다(읽기 쉽다).
- `log_temp_files=0` — temp 파일이 생기면 전부 로그에 남긴다(spill 증거).

> **데이터 엔지니어링 습관 1** — 위험한 실험은 **격리된 일회용 환경**에서. 프로덕션 DB에서 `SET work_mem`, 무거운 `EXPLAIN ANALYZE`를 돌리지 않는다(ANALYZE는 쿼리를 _실제로 실행_한다).

---

## 2. 데이터 모델 — 사건을 축약 미러링

실제 모델(`Task` / `Assignment` / `User`)을 핵심만 남겨 옮겼다. 포인트는 두 가지: **`task.data`는 wide JSON**, **`assignment`는 to-many(fan-out)**.

```sql
CREATE TABLE app_user   (id bigint PRIMARY KEY, email text NOT NULL);
CREATE TABLE task       (id bigint PRIMARY KEY, project_id int NOT NULL, data jsonb);
CREATE TABLE assignment (id bigint PRIMARY KEY, task_id bigint NOT NULL, user_id bigint NOT NULL);

-- 압축 비활성화: 행 폭을 실제처럼 유지(아래 함정 참고)
ALTER TABLE task ALTER COLUMN data SET STORAGE EXTERNAL;

INSERT INTO app_user
SELECT g, 'user'||g||'@example.com' FROM generate_series(1,2000) g;

-- 행당 ≈ 8KB 고엔트로피 JSON (서로 다른 md5 250개를 이어붙임)
INSERT INTO task
SELECT g, 18, jsonb_build_object(
  'blob', (SELECT string_agg(md5(random()::text),'') FROM generate_series(1,250)), 'i', g)
FROM generate_series(1,20000) g;

-- task당 assignment 3건 → to-many fan-out 60,000행
INSERT INTO assignment
SELECT row_number() OVER (), t.g, ((t.g*3+k)%2000)+1
FROM generate_series(1,20000) t(g), generate_series(0,2) k;

CREATE INDEX ON assignment(task_id);
CREATE INDEX ON assignment(user_id);
ANALYZE;
```

> **재현 중 만난 함정 — TOAST 압축** 🪤
> 처음엔 `repeat('x', 8000)`으로 8KB를 채웠는데, 테이블 크기가 **행당 215바이트**로 찍혔다. PostgreSQL의 **TOAST가 반복 바이트를 거의 0으로 압축**해버린 것. 프로덕션의 실제 라벨 JSON(좌표·텍스트)은 그렇게 압축되지 않으므로, ①`md5(random())`로 **고엔트로피** 콘텐츠를 만들고 ②`STORAGE EXTERNAL`로 **컬럼 압축을 끄니** 비로소 행당 ~8KB가 유지됐다.
>
> 이건 그 자체로 데이터 엔지니어링 교훈이다 — **"디스크 크기 ≠ 메모리 크기"**. 압축된 8KB도 정렬할 땐 메모리에서 풀려 8KB를 차지한다.

적재 결과:

```
 rows  | total_size | avg_data_col | avg_text_len
-------+------------+--------------+--------------
 20000 | 162 MB     | 8037 bytes   | 8023 bytes
```

| | 내 재현 | 프로덕션 |
|---|---|---|
| 행 수 | 20,000 | ~97,000 |
| 행당 `data` | ~8 KB | ~4.29 MB |
| to-many | task당 3 assignment | 동일 구조 |
| **배율** | **기준** | **약 2,500× / 537× (행폭)** |

---

## 3. 먼저, EXPLAIN ANALYZE 읽는 법 (30초 속성)

플랜을 읽을 때 내가 보는 **5개 신호**:

| 신호 | 어디서 | 의미 |
|---|---|---|
| **Sort Method / HashAggregate Disk Usage** | 노드 줄 | `external merge Disk: …` 또는 `Disk Usage: …` 가 보이면 **메모리 부족 → 디스크 spill**(느림의 주범) |
| **Memory Usage / Batches** | 노드 줄 | `Batches: 1`이면 메모리 내 처리, `Batches: 37`처럼 늘면 spill 분할 |
| **Group Key / Sort Key** | 노드 줄 | **여기에 무거운 컬럼이 끼었는지** 가 이 사건의 핵심 |
| **temp read/written** | `Buffers:` | spill로 읽고 쓴 임시 블록 수(×8KB) |
| **actual time / rows / loops** | 모든 노드 | 추정이 아니라 **실측**. `rows`가 예상과 크게 다르면 통계/플랜 문제 |

> **습관 2** — `EXPLAIN`(추정)이 아니라 `EXPLAIN (ANALYZE, BUFFERS)`(실측 + I/O)을 본다. 단, ANALYZE는 **쿼리를 실제 실행**하므로 쓰기 쿼리엔 트랜잭션+ROLLBACK으로 감싼다.

---

## 4. BAD ① — `total_count` 쿼리가 디스크로 spill

수정 전 익스포트는 검색을 이렇게 적용했다(개념). base queryset이 `CAST(data AS text)`를 달고 다니고, to-many 검색이라 `.distinct()`가 붙는다.

```sql
SET work_mem = '4MB';        -- 프로덕션 64MB이나 행이 537배 좁으니 축소
SET temp_file_limit = '2GB'; -- 일단 완주시켜 spill 크기를 측정

EXPLAIN (ANALYZE, BUFFERS, COSTS OFF)
SELECT COUNT(*) FROM (
  SELECT DISTINCT t.id, CAST(t.data AS text) AS _export_data_text
  FROM task t
  JOIN assignment a ON a.task_id = t.id
  JOIN app_user  u ON u.id = a.user_id
  WHERE u.email ILIKE '%example%'
) sub;
```

실측 플랜(핵심만):

```text
 Aggregate (actual time=3355.996..3356.013 rows=1 loops=1)
   Buffers: shared hit=240574, temp read=102909 written=144959
   ->  HashAggregate (actual time=2933.512..3354.893 rows=20000 loops=1)
         Group Key: t.id, (t.data)::text          ← ★ wide JSON이 그룹 키에!
         Batches: 37  Memory Usage: 8289kB  Disk Usage: 453456kB   ← ★ 453MB 디스크 spill
         Buffers: shared hit=240574, temp read=102909 written=144959
         ->  Hash Join (actual time=8.859..1592.607 rows=60000 loops=1)   ← fan-out 6만 행
               ...
 Execution Time: 3458.101 ms
```

**무슨 일이 벌어졌나:**

1. `task ⋈ assignment ⋈ user`로 **6만 행**이 만들어진다(task당 3 assignment = fan-out).
2. `DISTINCT (id, (data)::text)`를 위해 PostgreSQL이 **HashAggregate**를 쓰는데, **그룹 키에 8KB짜리 `(data)::text`가 통째로 들어간다.**
3. 해시 테이블이 `work_mem`(4MB)을 넘자 **37개 배치로 쪼개 디스크로 쏟았다 → `Disk Usage: 453456kB`(약 453MB).**
4. 결과: 3.46초. 이 느림과 메모리는 **순전히 "무거운 컬럼을 그룹 키에 넣었기 때문"** 이다. id만으로 distinct했다면 키는 8바이트면 됐다.

> 커밋 메시지 원문과 1:1로 맞는다:
> *"DISTINCT/GROUP BY 키로 wide JSON 컬럼을 끌어들여 work_mem 을 초과, temp 파일 spill 로 total_count 추정 쿼리를 abort 시킨다."*

---

## 5. BAD ② — 한도를 낮추니 프로덕션처럼 **abort**

프로덕션에서 실제로 본 최종 증상은 spill을 넘어 **쿼리 자체가 죽는 것**이었다. `temp_file_limit`을 spill 크기(453MB)보다 낮게 잡으면 그대로 재현된다.

```sql
SET work_mem = '4MB';
SET temp_file_limit = '100MB';   -- spill 453MB > 100MB
SELECT COUNT(*) FROM ( SELECT DISTINCT t.id, CAST(t.data AS text) ... ) sub;
```

```text
ERROR:  temporary file size exceeds temp_file_limit (102400kB)
```

바로 이거다. `total_count` 추정 단계에서 임시 파일이 한도를 넘겨 **쿼리가 abort**되고, 그 결과 익스포트 잡은 첫 청크조차 끝내지 못한 채 실패했다.

> **습관 3** — `temp_file_limit`은 "DB를 지키는 안전판"이지 해결책이 아니다. 한도를 올리면 abort는 안 나지만 **느린 디스크 spill은 그대로**다. 진짜 해결은 **spill을 안 만드는 쿼리**다(다음 절).

---

## 6. GOOD ① — id만 매칭하는 EXISTS (wide 컬럼 추방)

수정의 핵심은 단순하다. **검색 매칭은 id로만, 무거운 컬럼은 근처에도 못 오게.** to-many는 JOIN+DISTINCT 대신 **EXISTS**로 바꿔 fan-out 자체를 없앤다.

```sql
EXPLAIN (ANALYZE, BUFFERS, COSTS OFF)
SELECT COUNT(*) FROM (
  SELECT t.id FROM task t
  WHERE EXISTS (
    SELECT 1 FROM assignment a JOIN app_user u ON u.id = a.user_id
    WHERE a.task_id = t.id AND u.email ILIKE '%example%'
  )
) sub;
```

```text
 Aggregate (actual time=42.946..42.953 rows=1 loops=1)
   ->  Hash Join (rows=20000)
         ->  HashAggregate
               Group Key: a.task_id              ← 좁은 키(8바이트)
               Batches: 1  Memory Usage: 1809kB  ← ★ spill 없음
 Execution Time: 44.221 ms                       ← ★ 3458ms → 44ms (약 78배)
```

- 그룹 키가 `a.task_id`(좁음) → 해시 테이블이 1.8MB로 `work_mem` 안에 들어가 **`Batches: 1`, spill 0**.
- **44ms.** 같은 결과를 78배 빠르게.
- `EXISTS`는 매칭되는 assignment를 **하나만 찾으면 즉시 멈춰서**(semi-join) fan-out 6만 행을 만들지 않는다 → `DISTINCT`가 아예 필요 없다.

> 이게 **predicate/projection pushdown**의 실체다. "필요한 컬럼(id)만, 필터는 데이터 가까이서, 중복은 애초에 안 만들기."

---

## 7. GOOD ② — chunk-fetch는 인덱스로 흐른다

익스포트는 keyset 페이지네이션으로 청크를 읽는다. 수정본은 `id__in(EXISTS)` + **DISTINCT 제거** + PK 인덱스 정렬이라, `LIMIT 250`이 곧바로 효력을 낸다.

```sql
EXPLAIN (ANALYZE, BUFFERS, COSTS OFF)
SELECT t.id, t.project_id, CAST(t.data AS text) AS _export_data_text
FROM task t
WHERE t.id IN (
    SELECT t2.id FROM task t2
    WHERE EXISTS (SELECT 1 FROM assignment a JOIN app_user u ON u.id=a.user_id
                  WHERE a.task_id=t2.id AND u.email ILIKE '%example%'))
  AND t.id > 0          -- keyset cursor
ORDER BY t.id LIMIT 250;
```

```text
 Limit (actual time=0.175..7.167 rows=250 loops=1)
   ->  Nested Loop Semi Join (rows=250)
         ->  Index Scan using task_pkey on task t (Index Cond: id > 0, rows=250)
         ->  ... 전부 Index Scan ...
 Execution Time: 7.244 ms                         ← temp 없음, 250행만 읽음
```

PK 인덱스로 `id` 순서대로 흐르며 250행을 채우면 즉시 멈춘다. **temp spill 0, 7.2ms.**

> ⚠️ 흥미로운 반전: 수정 _전_ chunk-fetch도(`DISTINCT + ORDER BY id LIMIT 250`) 내 재현에선 `Incremental Sort`로 22ms에 끝났다. **chunk-fetch SQL 자체는 인덱스 덕에 안 죽었다.** 프로덕션의 chunk OOM은 SQL이 아니라 **다른 층**에 있었다 — 다음 절.

---

## 8. 두 번째 OOM — 이건 Postgres가 아니라 "워커 메모리"였다

앞 글에서 분리해 진단한 그 통증이다. chunk 쿼리는 멀쩡한데도 워커가 죽은 이유는, **Django가 청크의 모든 행을 모델 인스턴스로 메모리에 올렸기 때문**이다. 메모리는 단순하다:

```
청크 메모리 ≈ chunk_size × 행당 payload
```

실측으로 못 박았다.

```sql
SELECT 'chunk=250'  , pg_size_pretty(sum(pg_column_size(data))) FROM (SELECT data FROM task ORDER BY id LIMIT 250)  x
UNION ALL
SELECT 'chunk=2000' , pg_size_pretty(sum(pg_column_size(data))) FROM (SELECT data FROM task ORDER BY id LIMIT 2000) x;
```

| chunk_size | 내 스케일 (8KB/행) | 프로덕션 환산 (4.29MB/행) | 6Gi 워커 |
|---|---|---|---|
| 250 | 1.96 MB | **~1.07 GB** | ✅ 예산 내 |
| 2000 | 15 MB | **~8.58 GB** | 💥 OOMKilled |

수정 전엔 검색 동반 시 `_SEARCH_CHUNK_SIZE=2000`으로 청크를 키웠는데, `2000 × 4.29MB ≈ 8.6GB`가 6Gi cgroup을 넘겨 워커가 **첫 체크포인트 flush 전에 죽었다** → 재개 불가 FAILED. `_DEFAULT_CHUNK_SIZE=250`으로 통일하니 `~1.07GB`로 예산 안에 들고, 첫 청크가 빨리 끝나 체크포인트가 남아 **재개 가능**해졌다.

> **습관 4** — `peak_memory ≈ 동시성 × 배치 × 행 폭`을 **항상 암산**하라. 청크 크기는 "왕복을 줄이려고" 키우는 게 아니라 **메모리 예산에서 역산**해 정하는 값이다.

---

## 9. 한 사건, 다섯 개념

이 작은 재현 하나에 데이터 엔지니어링 핵심이 다 들어있다.

1. **Projection Pushdown** — 검색엔 `id`만. 무거운 컬럼을 정렬/그룹 키에 넣지 마라.
2. **EXISTS vs JOIN+DISTINCT** — to-many는 semi-join으로. fan-out을 안 만들면 DISTINCT가 필요 없다.
3. **work_mem / spill / temp_file_limit** — 메모리를 넘으면 디스크로 새고(느림), 한도를 넘으면 죽는다(abort).
4. **디스크 크기 ≠ 메모리 크기** — TOAST로 압축돼도 정렬 땐 풀린다.
5. **bounded memory** — `chunk × 행폭`. 청크는 메모리 예산에서 역산한다. + 죽어도 이어가게 **체크포인트**.

처방을 한 줄로: **"무거운 건 옮기지 말고 id로 가리켜라. 정렬·중복은 애초에 만들지 마라. 한 번에 들고 있는 양은 예산에서 역산하라."**

---

## 10. 직접 해보기

전체 재현 스크립트를 `/tmp/oom-repro.sql`에 담아 뒀다(스키마·데이터·BAD/GOOD/abort/메모리 산수 일괄).

```bash
# 격리 클러스터 (실 dev DB 5432와 무관)
export PGBIN=/usr/lib/postgresql/17/bin
$PGBIN/initdb -D /tmp/pg-oom-demo -U demo --no-locale -E UTF8
$PGBIN/pg_ctl -D /tmp/pg-oom-demo \
  -o "-p 55432 -c listen_addresses=127.0.0.1 -c max_parallel_workers_per_gather=0 -c log_temp_files=0" \
  -l /tmp/pg-oom-demo.log start

# 재현 실행
psql -h 127.0.0.1 -p 55432 -U demo -d oom -f /tmp/oom-repro.sql

# 직접 탐색 (work_mem를 바꿔가며 spill이 어떻게 변하는지 보라)
psql -h 127.0.0.1 -p 55432 -U demo -d oom

# 정리
$PGBIN/pg_ctl -D /tmp/pg-oom-demo stop && rm -rf /tmp/pg-oom-demo
```

**스스로 해볼 실험 3개:**

1. `SET work_mem = '512MB'` 후 BAD ①을 다시 — spill이 사라지나? (메모리로 가린 것일 뿐 근본 해결이 아님을 체감)
2. `task` 행 폭을 8KB → 64KB로 키우고 GOOD/BAD 격차가 어떻게 벌어지는지.
3. `assignment`를 task당 3 → 10건으로 늘려 fan-out이 BAD HashAggregate에 주는 영향 관찰.

---

## 11. 마무리

앞 글이 "**무엇이** 통증인가"였다면, 이 글은 "**왜** 그렇게 되는가를 내 손으로 확인"한 기록이다. `EXPLAIN ANALYZE` 한 줄 — `Group Key: t.id, (t.data)::text` — 이 모든 것을 말해줬다. 무거운 컬럼이 그 자리에 있어선 안 됐다.

다음 실습편 후보는 앞 글 30/60/90 플랜에서 이어진다 — **DuckDB로 익스포트 산출물을 Parquet로 읽어 행 기반 JSON과 비교(통증 7)**. 컬럼너 포맷이 왜 분석/반출에서 압도적인지, 같은 데이터로 직접 재보려 한다.

---

*재현은 PostgreSQL 17.4 격리 클러스터에서 수행했고, 모든 플랜 수치(spill 453MB, 3458ms→44ms, abort, chunk 1.07GB/8.58GB)는 실제 `EXPLAIN (ANALYZE, BUFFERS)` 출력이다. 쿼리 형태는 `synapse-backend`의 수정 전(`main 7c4a0866`) / 수정 후(`origin/main 808871f6`, SYN-7172) 코드를 미러링했다.*
