---
title: "[데이터 엔지니어링 실습] 파이프라인은 왜 '멱등'해야 하는가 — 재실행·증분·백필·크래시를 Postgres로 재현"
date: 2026-07-06T21:00:00+09:00
categories: Data-Engineering
tags:
  - DataEngineering
  - 데이터엔지니어링
  - Orchestration
  - 오케스트레이션
  - Airflow
  - Dagster
  - Prefect
  - Idempotency
  - 멱등성
  - Backfill
  - Watermark
  - Checkpoint
  - ExactlyOnce
  - Pipeline
  - Celery
  - 실습
toc: true
toc_sticky: true
---

> 데이터 엔지니어링 진단 시리즈의 **4편(실습)**. 1편 로드맵 4단계(파이프라인 & 오케스트레이션)를 직접 돌려본다.
> - 1편 — [\[진단\] API로만 데이터를 나르다 만난 벽]({% post_url 2026-06-16-data-engineering-diagnosis-synapse %})
> - 2편 — [\[실습\] OOM을 EXPLAIN ANALYZE로 재현]({% post_url 2026-06-16-oom-reproduction-explain-analyze %})
> - 3편 — [\[실습\] DuckDB로 Parquet vs JSON]({% post_url 2026-07-05-duckdb-parquet-vs-json %})
>
> 우리 시스템은 Celery로 여러 잡(익스포트·업로드·집계)을 돌린다. "스케줄러가 두 번 트리거하면?", "잡이 중간에 죽으면?", "과거 데이터가 틀렸으면?" — 이 질문들의 답이 **오케스트레이션**이고, 그 심장은 **멱등성(idempotency)** 이다. 말로만 알던 걸 Postgres에 실제 파이프라인으로 돌려 증명한다.

---

## 0. TL;DR

같은 파이프라인(원천 이벤트 → 일자×프로젝트 완료 집계)을 두고 실측한 5가지:

| 데모 | 무엇을 보이나 | 결과(실측) |
|---|---|---|
| **A. 비멱등 append** | 재실행이 데이터를 오염 | 2회 실행 → 총합 225가 **450으로 중복** |
| **B. 멱등 upsert** | overwrite grain | 2회 실행 → **225 유지** |
| **C. 증분(watermark)** | 신규만 처리 | 지각 5건 → 해당 셀만 25→**30**, 나머지 불변 |
| **D. 백필** | 과거 파티션 정정 | 증분은 놓침(watermark 뒤) → 백필로 07-02만 25→**35** |
| **E. 크래시 안전성** | 트랜잭션 경계가 전부 | 분리 트랜잭션 → **16(중복)**, 단일 트랜잭션 → **8(안전)** |

핵심 한 줄: **"파이프라인의 모든 작업은 몇 번을 실행하든 같은 결과여야 한다(idempotent). 그러면 재시도·중복 트리거·크래시가 더 이상 사고가 아니다."**

---

## 1. 오케스트레이션이 뭔데?

오케스트레이터(Airflow/Dagster/Prefect)가 하는 일은 셋으로 요약된다.

1. **DAG** — 작업들의 의존관계 그래프(extract → transform → load).
2. **스케줄링** — 언제(매일 새벽 2시), 어떤 구간(data interval)을 돌릴지.
3. **재시도·복구** — 실패하면 재시도, 죽으면 재개, 과거를 다시 돌리면 **백필**.

그런데 3번이 성립하려면 **한 가지 전제**가 필요하다. **"작업을 다시 돌려도 안전해야 한다"** = **멱등성**. 재시도가 데이터를 중복시키고, 백필이 과거를 오염시킨다면 오케스트레이터는 흉기가 된다.

> 이건 2편의 `SYN-7172` 익스포트와 정확히 같은 주제였다 — 워커가 죽어도 **체크포인트에서 재개**되고, 재개가 **중복/누락을 만들지 않아야** 했다. 오케스트레이션은 그 원리를 파이프라인 전체로 일반화한 것이다.

---

## 2. 미니 오케스트레이터로 감 잡기

"오케스트레이터가 내부에서 뭘 하나"를 50줄 파이썬으로 만들어 봤다 — **위상정렬 + 재시도 + 멱등 스킵**.

```python
class DAG:
    def toposort(self): ...          # 의존관계대로 실행 순서 결정 (사이클이면 에러)
    def run(self, done, max_retries=3):
        for name in self.toposort():
            if name in done:                    # 멱등: 이미 성공한 태스크는 건너뜀
                print(f"  SKIP  {name}"); continue
            for attempt in range(1, max_retries+1):
                try:    self.fns[name](); done.add(name); break   # 성공 시 상태 기록
                except Exception as e:
                    if attempt==max_retries: raise                 # 재시도 소진 → 실패 전파
```

`load`가 첫 시도에 일시적 오류로 실패하도록 만들고 돌리면:

```text
위상정렬 순서: ['extract', 'transform', 'load']

[1차 실행] load가 첫 시도 실패 후 재시도로 성공:
  OK    extract (attempt 1)
  OK    transform (attempt 1)
  RETRY load attempt 1 failed: transient DB timeout
  OK    load (attempt 2)

[재실행] 이미 done → 전부 스킵(멱등):
  SKIP  extract (already done)
  SKIP  transform (already done)
  SKIP  load (already done)
```

Airflow/Dagster는 이걸 **분산·영속·스케줄** 규모로 해줄 뿐, 본질은 이 50줄이다. 재시도(2번째 성공)와 재실행 시 스킵(멱등)이 핵심이다. 하지만 진짜 어려운 건 **각 태스크 내부를 멱등하게 만드는 것** — 그게 아래 SQL 실습이다.

---

## 3. 실습 환경

2편의 격리 Postgres(포트 55432)를 재사용해 `pipeline` DB를 만들었다.

```sql
CREATE TABLE source_events  (id bigint PK, project_id int, event_time timestamptz, status text);  -- 원천(append-only)
CREATE TABLE daily_summary  (day date, project_id int, completed_count bigint,
                             PRIMARY KEY (day, project_id));         -- 파생(타깃). (day,project)가 유니크 grain
CREATE TABLE pipeline_state (pipeline text PK, last_watermark timestamptz);   -- 체크포인트
```

시드: **3일 × 3프로젝트 × 100이벤트**, 각 셀당 완료(`status='completed'`) 정확히 **25건**(총 225). 숫자가 딱 떨어져 중복/누락이 한눈에 보인다.

---

## 4. DEMO A — 멱등하지 않으면, 재실행이 데이터를 오염시킨다

가장 흔한 초보 파이프라인: 집계해서 그냥 `INSERT`(append).

```sql
INSERT INTO daily_summary_naive(day, project_id, completed_count)
SELECT event_time::date, project_id, count(*) FROM source_events WHERE status='completed' GROUP BY 1,2;
```

스케줄러 재시도, 중복 트리거, 수동 재실행… 이유는 많다. **두 번 돌면:**

```text
총 행수: 18 (기대 9),  총합: 450 (기대 225)   ← 모든 셀이 2배로 중복 집계
```

"파이프라인이 왜 숫자가 두 배죠?"의 90%가 이거다. **append는 멱등하지 않다.**

---

## 5. DEMO B — 멱등의 첫 무기: upsert(overwrite)

타깃에 **유니크 grain**(`day, project_id`)을 주고 `INSERT ... ON CONFLICT DO UPDATE`로 **덮어쓴다**.

```sql
INSERT INTO daily_summary(day, project_id, completed_count)
SELECT event_time::date, project_id, count(*) FROM source_events WHERE status='completed' GROUP BY 1,2
ON CONFLICT (day, project_id) DO UPDATE SET completed_count = EXCLUDED.completed_count;
```

**두 번, 열 번을 돌려도:**

```text
총 행수: 9,  총합: 225   ← 안정 (재실행 안전)
```

이게 멱등성의 가장 실용적인 구현이다 — **"grain을 정하고, 그 grain을 덮어써라(UPSERT / MERGE)."** 전체 재계산이라 항상 옳지만, 데이터가 커지면 비싸다. 그래서 증분이 필요하다.

---

## 6. DEMO C — 증분 처리: watermark

매번 전량 재계산은 낭비다. **마지막으로 처리한 지점(watermark)** 을 체크포인트에 저장하고, 그 이후만 처리한다. 단, **load와 watermark 갱신을 한 트랜잭션**에 묶는다(이유는 DEMO E).

```sql
BEGIN;
  INSERT INTO daily_summary(day, project_id, completed_count)
  SELECT event_time::date, project_id, count(*) FILTER (WHERE status='completed')
  FROM source_events
  WHERE event_time > (SELECT last_watermark FROM pipeline_state WHERE pipeline='daily')  -- 신규만
  GROUP BY 1,2 HAVING count(*) FILTER (WHERE status='completed') > 0
  ON CONFLICT (day, project_id) DO UPDATE
    SET completed_count = daily_summary.completed_count + EXCLUDED.completed_count;       -- 델타 누적
  UPDATE pipeline_state SET last_watermark = COALESCE(
    (SELECT max(event_time) FROM source_events WHERE event_time > last_watermark), last_watermark);
COMMIT;
```

실측:

```text
1st run  : 전체 900건 신규(watermark=epoch) → 총합 225, watermark → 2026-07-03 01:40
2nd run  : 신규 없음 → INSERT 0 0, 총합 225 (멱등)
지각 5건 : (07-03, proj1)에 완료 5건 추가 후 → 신규 5건만 처리
           (07-03,proj1)=30, 나머지 25 유지, watermark → 2026-07-03 03:04
```

**전량 재계산의 1/N 비용**으로 최신 상태를 유지한다. 단, 증분엔 함정이 둘 있다 — **워터마크 뒤에 도착한 데이터(DEMO D)** 와 **크래시 타이밍(DEMO E)**.

---

## 7. DEMO D — 백필: 과거 파티션을 정정

라벨 재검수로 **07-02의 완료 10건이 뒤늦게** 잡혔다고 하자. 문제는 그 이벤트의 `event_time`이 07-02(=이미 watermark 뒤)라는 것.

```text
(1) 증분 잡 재실행 → watermark(07-03 03:04) 뒤라 '못 봄' → INSERT 0 0, (07-02,proj2) 여전히 25
```

증분은 **워터마크 뒤 과거는 손대지 않는다**(그게 증분의 정의다). 이럴 때 필요한 게 **백필** — 대상 파티션만 source에서 **전량 재계산해 overwrite**.

```sql
-- 파라미터: 대상 일자 = 2026-07-02 (이 파티션만)
INSERT INTO daily_summary(day, project_id, completed_count)
SELECT event_time::date, project_id, count(*) FILTER (WHERE status='completed') FROM source_events
WHERE event_time::date = DATE '2026-07-02'          -- 파티션 스코프
GROUP BY 1,2
ON CONFLICT (day, project_id) DO UPDATE SET completed_count = EXCLUDED.completed_count;  -- 누적 아닌 overwrite!
```

```text
(2) 백필 후 → (07-02, proj2) 25 → 35 로 정정, proj1·proj3 및 다른 날짜는 무영향
```

포인트 둘:
- **백필은 overwrite**(누적 아님). 그래서 몇 번을 돌려도 그 파티션은 항상 정답으로 수렴 — **멱등**.
- **파티션 스코프**라 다른 날짜를 건드리지 않는다. → Airflow의 `backfill --start/--end`, Dagster의 **partition** 개념이 이걸 일급시민으로 만든 것.

> **습관** — 데이터를 **파티션(보통 날짜)** 으로 나눠라. 재처리·백필·삭제의 단위가 되고, 각 파티션 overwrite가 멱등이 된다.

---

## 8. DEMO E — 크래시 안전성 (가장 중요)

분산 시스템에선 "정확히 한 번(exactly-once)" 전달이 사실상 불가능하다. 현실의 정답은:

> **at-least-once(최소 한 번) 전달 + 멱등한 처리 = effectively-once(사실상 한 번)**

증분 파이프라인의 급소는 **"load는 됐는데 watermark 갱신 전에 죽는" 순간**이다. 두 패턴을 대비했다.

### E1 — 잘못된 패턴: load와 watermark가 분리된 트랜잭션

```text
[txn1] 신규 8건 additive load → COMMIT   (여기까지 디스크 반영)
  💥 CRASH — watermark 갱신(txn2) 실행 전 워커 죽음
  상태: day=8, watermark 그대로(07-10)
[재실행] watermark가 안 움직였으니 같은 8건을 '신규'로 또 처리(additive)
  ❌ day = 16   ← 중복 집계
```

load는 커밋됐는데 watermark는 안 올라갔다. 재개하면 같은 데이터를 또 먹는다.

### E2 — 올바른 패턴: load + watermark를 한 트랜잭션에

```text
[txn] BEGIN → load → watermark 갱신 → (COMMIT 직전 💥 CRASH = ROLLBACK)
  상태: day rows=0, watermark 그대로   ← 둘 다 원자적으로 롤백
[정상 재실행] BEGIN → load → watermark → COMMIT
  ✅ day = 8, watermark → 2026-07-11 00:00:08
[한 번 더 재실행] watermark 전진 → INSERT 0 0
  ✅ day = 8 (불변, 멱등)
```

load와 watermark가 **원자적으로 함께 커밋되거나 함께 취소**되므로, 크래시가 어디서 나든 재개가 안전하다. **트랜잭션 경계가 곧 멱등성의 경계**다.

> 2편 `SYN-7172`가 "첫 체크포인트 flush 전에 죽으면 재개 불가"였던 것과 같은 이야기 — 상태(watermark/checkpoint)와 결과(part 파일/집계)의 커밋을 **한 경계**로 묶어야 재개가 정확해진다.

---

## 9. 그래서 오케스트레이터가 이걸 어떻게 도와주나

위 원리를 실무 도구로 매핑하면:

**Apache Airflow**
- **DAG / 스케줄** + `data_interval`(logical date) — 각 실행이 "어느 구간"인지 명확 → 구간 단위 멱등 처리의 토대.
- `retries` / `retry_delay` — DEMO 2번 재시도가 이것.
- `airflow dags backfill --start-date --end-date` + `catchup` — DEMO D의 파티션 백필.
- **주의(통증 5·1 연결)**: **XCom으로 큰 데이터를 넘기지 마라.** XCom은 메타데이터(경로/파라미터)용. 바이트는 스토리지에 두고 주소만 넘긴다 — 1편의 "제어 평면 vs 데이터 평면" 그대로.

**Dagster**
- **Software-Defined Assets** — "테이블/파일 자체"를 선언하고 의존관계를 그림 → `daily_summary`가 `source_events`에서 파생됨을 명시.
- **Partitions** — 날짜 파티션이 일급시민. 파티션별 materialize/백필이 DEMO C·D 그대로.
- **Freshness / SLA** — "이 자산은 N시간 내 최신이어야" 선언.

**Prefect** — 파이썬 함수 데코레이터 중심의 가벼운 흐름. 소규모/동적 워크플로에 적합.

**언제 쓰고, 언제 안 쓰나 (정직하게)**
- 잡이 몇 개고 의존이 단순하면 **cron + 멱등 스크립트**로 충분하다. 오케스트레이터는 공짜가 아니다(운영 부담).
- **DAG 의존, 백필, 재시도, 관측이 필요해지는 순간** 도입한다. 도구가 멱등성을 대신 만들어주지는 않는다 — **멱등은 당신이 SQL/코드로 설계**하고, 도구는 그걸 스케줄·재시도·백필해줄 뿐.

---

## 10. Synapse에 매핑 — 우리 Celery 잡을 어떻게 바꿀까

1. **모든 잡을 멱등하게.** 결과 테이블에 유니크 grain을 두고 append 대신 UPSERT/overwrite. "재시도하면 중복" 버그의 근절.
2. **상태와 결과를 한 트랜잭션에.** 2편 익스포트가 part 파일 + checkpoint를 함께 커밋하듯, 집계/증분 잡도 load + watermark를 원자적으로.
3. **watermark 테이블 도입.** 증분 가능한 잡(집계, 동기화)은 전량 재처리 대신 워터마크 기반으로 — 비용 급감.
4. **파티션 + 백필 경로.** 날짜/프로젝트로 파티션하고, "이 파티션만 재계산" 백필 커맨드를 표준화(과거 정정·재처리의 안전한 도구).
5. **필요해지면 오케스트레이터.** 잡 간 의존과 백필 요구가 커지면 Dagster(자산·파티션 모델이 라벨링 파이프라인과 잘 맞음)를 우선 검토.

---

## 11. 원칙 정리

1. **Idempotency(멱등성)** — 몇 번 실행하든 같은 결과. 재시도·중복·크래시를 무력화하는 단 하나의 성질.
2. **at-least-once + 멱등 = effectively-once** — 정확히 한 번을 보장하려 하지 말고, 여러 번 와도 안전하게 만들어라.
3. **Grain & UPSERT** — 유니크 grain을 정하고 덮어써라(MERGE). append는 최후의 수단.
4. **Watermark & 증분** — 전량 재계산은 비싸다. 체크포인트로 신규만.
5. **Partition & Backfill** — 파티션은 재처리/백필/삭제의 단위. 파티션 overwrite는 멱등.
6. **트랜잭션 경계 = 멱등 경계** — 상태와 결과를 함께 커밋하라.
7. **오케스트레이터로 데이터를 나르지 마라** — DAG는 제어, 바이트는 스토리지(1편 재확인).

---

## 12. 직접 해보기

```bash
# (2편 클러스터가 떠 있어야 함: /tmp/pg-oom-demo, port 55432)
PSQL="/usr/lib/postgresql/17/bin/psql -h 127.0.0.1 -p 55432 -U demo -d pipeline"
python3 mini_dag.py                 # DAG 위상정렬 + 재시도 + 멱등 스킵
# DEMO A~E SQL은 본문 스니펫 그대로 psql에 실행
```

**스스로 해볼 실험 3개:**
1. DEMO C의 `ON CONFLICT`를 `+ EXCLUDED`(누적) 대신 `= EXCLUDED`(overwrite)로 바꾸면 증분이 왜 틀리는가? (증분은 델타를 누적해야 하고, 백필은 전량을 overwrite — grain별로 의미가 다름을 체감)
2. E2 트랜잭션 안에 일부러 `1/0` 에러를 넣어 rollback을 유발하고, 상태가 정말 원자적으로 되돌아오는지 확인.
3. `daily_summary`를 `day`로 파티션 테이블(`PARTITION BY RANGE`)로 만들고, 백필이 파티션 단위로 얼마나 깔끔해지는지.

정리:
```bash
/usr/lib/postgresql/17/bin/psql -h 127.0.0.1 -p 55432 -U demo -d postgres -c "DROP DATABASE pipeline;"
# 데모 클러스터 자체를 내리려면:
/usr/lib/postgresql/17/bin/pg_ctl -D /tmp/pg-oom-demo stop && rm -rf /tmp/pg-oom-demo
```

---

## 13. 마무리

4부작을 한 줄로 잇는다.

- **1편**: 느림·에러의 뿌리 = 데이터 평면이 제어 평면을 통과 / 필요 없는 걸 읽음.
- **2편**: 무거운 컬럼을 정렬 키에 넣어 453MB spill(EXPLAIN ANALYZE로 실토).
- **3편**: 컬럼너로 "안 읽을 것을 안 읽어" 480배.
- **4편**: 재실행·크래시·백필을 **멱등성**으로 무력화 — 파이프라인이 사고를 견딘다.

세 편이 "**read less(덜 읽어라)**"였다면, 이번 편은 "**re-run safe(다시 돌려도 안전하게)**"다. 이 둘이 데이터 엔지니어링 실무의 절반이다.

다음은 1편 로드맵 6단계 — **데이터 품질·관측성·계보(lineage)**. "파이프라인이 돌긴 하는데, 그 결과를 믿어도 되나?"를 다룬다.

---

*모든 실습은 PostgreSQL 17.4 격리 클러스터에서 수행했고, 인용한 수치(A: 450, B: 225, C: 30/230, D: 35, E1: 16, E2: 8)는 실제 실행 출력이다.*
