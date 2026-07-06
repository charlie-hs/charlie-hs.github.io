---
title: "[데이터 엔지니어링 실습] 같은 데이터, 480배의 차이 — DuckDB로 Parquet vs JSON 직접 재보기"
date: 2026-07-05T21:00:00+09:00
categories: Data-Engineering
tags:
  - DataEngineering
  - 데이터엔지니어링
  - DuckDB
  - Parquet
  - JSON
  - JSONL
  - Columnar
  - 컬럼너
  - ProjectionPushdown
  - Compression
  - Arrow
  - Lakehouse
  - Export
  - MLOps
  - 실습
  - SQL튜닝
toc: true
toc_sticky: true
---

> 이 글은 데이터 엔지니어링 진단 시리즈의 **3편(실습)** 이다.
> - 1편 — [\[진단\] API로만 데이터를 나르다 만난 벽]({% post_url 2026-06-16-data-engineering-diagnosis-synapse %})
> - 2편 — [\[실습\] OOM 사건을 EXPLAIN ANALYZE로 재현]({% post_url 2026-06-16-oom-reproduction-explain-analyze %})
>
> 1편에서 진단한 **"통증 7 — 잘못된 전송 포맷(행 기반 JSON으로 대량 전송)"** 을, DuckDB로 **직접 실측**해 확인한다.
> 우리 익스포트 번들은 지금 **행 기반 JSON**(`raw_bundle_v1`)이다. 이게 분석·반출에서 얼마나 손해인지, 그리고 **컬럼너(Parquet)** 로 바꾸면 얼마나 달라지는지를 같은 데이터로 재본다.

---

## 0. TL;DR

같은 10만 건 라벨링 익스포트 데이터(원본 324MB JSONL)를 두고:

| 항목 | JSON(행) | Parquet(컬럼너) | 차이 |
|---|---|---|---|
| **파일 크기** | 324 MB | **18.6 MB** (zstd) | **17.4배 작음** (gzip 36.9MB보다도 2배 작음) |
| **스칼라 집계** (status별 건수) | 826 ms | **1.7 ms** | **482배 빠름** |
| **필터+집계** (프로젝트별 처리량) | 829 ms | **1.9 ms** | **438배 빠름** |
| **중첩 데이터 집계** (라벨 분포) | 3,376 ms | 1,708 ms(native) / **7.2 ms**(셔레딩) | 2배 / **468배** |

핵심 한 줄: **"행 기반 JSON은 한 글자를 읽으려 해도 전부를 읽어 파싱해야 하고, 컬럼너는 필요한 컬럼만 읽는다."** 그래서 스칼라 분석에서 수백 배가 갈린다.

---

## 1. 왜 이걸 재보는가 — 행(row) vs 컬럼너(columnar)

우리 익스포트는 레코드 하나를 이런 **행 단위 JSON**으로 쓴다(개념).

```json
{"id": 1, "project_id": 2, "status": "rejected", "num_annotations": 12,
 "data": {"image": {...}, "annotations": [ {bbox, label, confidence, ...} × 12 ]}}
```

분석가가 실제로 던지는 질문은 대개 **스칼라 몇 개**에 대한 것이다 — "status별 몇 건?", "프로젝트별 완료 처리량은?", "이미지당 평균 annotation 수는?". 그런데 **행 포맷은 이 질문에 답하려고 매번 4.29MB짜리 `data` 블롭까지 통째로 읽어 파싱**한다. 필요한 건 `status` 한 글자인데.

```
행 포맷(JSONL)                     컬럼너 포맷(Parquet)
┌───────────────────────────┐     id     │ 1 │ 2 │ 3 │ ...   ← 이 컬럼만 읽음
│ {id,status,...,data:{...}}│     status │ R │ C │ P │ ...   ← 이 컬럼만 읽음
│ {id,status,...,data:{...}}│     ...
│ {id,status,...,data:{...}}│     data   │ (거대) (거대) ... ← 안 건드림
└───────────────────────────┘
한 행이 통째로 붙어 있음            컬럼이 따로 저장됨 → 필요한 것만 스캔
```

이것이 **projection pushdown**(필요한 컬럼만 읽기)의 물리적 토대다. 말로만 알던 걸 숫자로 확인해보자.

> **습관** — DuckDB는 "설치 없는 로컬 분석 엔진"이다. `pip`/바이너리 하나로 JSON·Parquet·CSV를 SQL로 바로 질의한다. 포맷 실험엔 최적의 도구.

---

## 2. 실습 환경

```bash
mkdir -p /tmp/duckdb-demo && cd /tmp/duckdb-demo
# DuckDB는 uv로 격리 실행 (설치 흔적 안 남김)
uv run --with duckdb python3 <script>.py
```

**데이터**: 라벨링 익스포트를 모사한 10만 건. 각 레코드 = **스칼라 메타데이터**(id, project_id, created, status, assignee, num_annotations) + **큰 중첩 `data`**(image 정보 + annotations 리스트: bbox·label·confidence·attributes). annotation 수는 이미지당 5~40개(총 225만 개).

```python
# gen.py 핵심 (seed 고정)
rec = {"id": i, "project_id": (i%12)+1, "created": ..., "status": random.choice(STATUS),
       "assignee": f"user{n}@example.com", "num_annotations": k,
       "data": {"image": {...}, "annotations": [ {bbox, label, confidence, attributes} × k ]}}
```

→ `records.jsonl` = **324 MB** (10만 행, 행당 ~3.3KB).

---

## 3. 실험 ① — 파일 크기

DuckDB로 JSONL을 읽어 Parquet로 저장하고(중첩 struct 그대로 컬럼너로), 공정 비교를 위해 gzip도 만든다.

```python
con.execute("CREATE TABLE t AS SELECT * FROM read_json_auto('records.jsonl')")
con.execute("COPY t TO 'records.zstd.parquet'  (FORMAT parquet, COMPRESSION zstd)")
con.execute("COPY t TO 'records.snappy.parquet'(FORMAT parquet, COMPRESSION snappy)")
```

| 파일 | 크기 | 비고 |
|---|---|---|
| `records.jsonl` | **324.0 MB** | 원본 행 포맷 |
| `records.jsonl.gz` (gzip -9) | 36.9 MB | 압축된 행 포맷 |
| `records.snappy.parquet` | 20.4 MB | 컬럼너 (빠른 압축) |
| **`records.zstd.parquet`** | **18.6 MB** | 컬럼너 (고압축) |
| `annotations.zstd.parquet` | 6.3 MB | 셔레딩(225만 행, 4절) |

**왜 이렇게 작나?** 컬럼너는 **같은 컬럼의 비슷한 값이 붙어 있어** 압축이 잘 먹는다. 게다가 **dictionary encoding**(`status` 5종 → 정수 코드), **RLE**(반복 제거), 컬럼별 최적 인코딩이 자동 적용된다. 그 결과 **gzip으로 압축한 JSON(36.9MB)보다도 2배 작고**, 심지어 **압축을 풀지 않고도 질의**할 수 있다(gzip은 통째로 풀어야 함).

---

## 4. 실험 ② — 쿼리 성능

각 쿼리를 warmup 후 best-of-3로 측정(threads=4, 동일 머신, OS 캐시 warm 상태로 공정 비교).

### Q1 — 스칼라 집계: `status`별 건수 (큰 `data` 불필요)

```sql
SELECT status, count(*) FROM <source> GROUP BY status;
```

| 소스 | 시간 |
|---|---|
| `read_json_auto('records.jsonl')` | 825.9 ms |
| `'records.zstd.parquet'` | **1.7 ms** |

→ **Parquet가 482배 빠름.** JSON은 324MB 전체를 읽어 매 행을 파싱해야 status를 꺼낼 수 있다. Parquet는 `status` 컬럼만 읽는다(5절에서 그게 몇 KB인지 확인).

### Q2 — 필터 + 집계: 완료 태스크의 프로젝트별 처리량 / 평균 annotation 수

```sql
SELECT project_id, count(*), avg(num_annotations)
FROM <source> WHERE status='completed' GROUP BY project_id;
```

| 소스 | 시간 |
|---|---|
| JSON | 829.3 ms |
| Parquet | **1.9 ms** |

→ **438배.** `status`, `project_id`, `num_annotations` 세 개의 작은 컬럼만 스캔. `data`는 손도 안 댄다.

### Q3 — 중첩 데이터 접근: 라벨 분포 (annotations `unnest`)

```sql
-- JSON / Parquet(native): 중첩 리스트를 unnest
SELECT a.label, count(*) c FROM <source>, UNNEST(data.annotations) t(a) GROUP BY a.label ORDER BY c DESC;
-- 셔레딩 테이블: 이미 평탄
SELECT label, count(*) c, avg(confidence) FROM 'annotations.zstd.parquet' GROUP BY label ORDER BY c DESC;
```

| 소스 | 시간 |
|---|---|
| JSON (unnest) | 3,376 ms |
| Parquet native (unnest) | 1,707 ms |
| **Parquet 셔레딩(평탄 테이블)** | **7.2 ms** |

→ 이번엔 **큰 데이터를 반드시 읽어야** 하므로 격차가 줄어든다(native 2배). 하지만 미리 annotation을 **평탄한 컬럼너 테이블로 셔레딩**해두면 `label`·`confidence` 두 컬럼만 읽어 **468배**가 된다.

> **교훈** — 컬럼너는 "안 읽어도 되는 걸 안 읽는" 최적화다. 그러니 **자주 분석하는 축(annotation 단위 통계)** 은 미리 셔레딩해 컬럼으로 펼쳐두면 위력이 극대화된다.

---

## 5. 왜 480배인가 — projection pushdown을 눈으로

Parquet은 컬럼마다 따로 압축·저장하므로, **컬럼별 크기**를 볼 수 있다.

```sql
SELECT path_in_schema AS column, sum(total_compressed_size) AS bytes
FROM parquet_metadata('records.zstd.parquet') GROUP BY 1 ORDER BY 2 DESC;
```

```text
  data...annotations...bbox          12336.3 KB  (65.0%)   ← 좌표가 파일의 2/3
  data...annotations...confidence     3692.5 KB  (19.4%)
  data...annotations...label          1026.2 KB  ( 5.4%)
  ...
  num_annotations                       70.3 KB  ( 0.4%)
  status                                33.1 KB  ( 0.2%)   ← Q1이 읽는 전부
  project_id                             0.1 KB  ( 0.0%)
  TOTAL                              18984.9 KB
```

- **Q1은 `status` 컬럼(33.1 KB)만 스캔한다 → 전체의 0.17%.** JSON은 매번 324MB를 파싱한다. I/O 기준 ~1만 배 차이, 실측 시간 482배 차이(파싱 비용이 지배).
- `bbox`가 파일의 **65%**다. 그래서 Q3에서 `data`를 통째로 훑는 native unnest가 느렸고, `bbox`를 아예 안 읽는 셔레딩(label/confidence만)이 압도적이었다.

> 이게 1편에서 말한 predicate/projection pushdown의 물리다 — **"필요한 컬럼만, 필요한 행만."** 2편의 OOM도 결국 같은 뿌리였다(무거운 컬럼을 안 읽어도 될 자리에서 읽었다).

---

## 6. 정직하게 — Parquet이 항상 답은 아니다

컬럼너가 만능은 아니다. 멘토라면 트레이드오프를 말해야 한다.

**Parquet(컬럼너)가 이기는 곳**
- 대량 **분석/집계**, 일부 컬럼만 반복 조회(BI, 리포트, 학습셋 통계).
- **한 번 쓰고 여러 번 읽는** 데이터(익스포트 산출물, 웨어하우스/레이크).
- 저장·전송 비용이 중요한 대용량.

**행 포맷(JSON/JSONL)이 여전히 나은 곳**
- **레코드 단위 전체 조회/전송**(한 태스크의 모든 필드를 그대로 주고받는 API 응답).
- **스키마가 유동적**이거나 반정형(중첩이 매 레코드마다 다름).
- **스트리밍 append/한 건씩 쓰기**(로그, 이벤트 수집). Parquet은 배치로 row group을 만들 때 진가.
- 사람이 눈으로 읽어야 할 때(디버깅).

> 그래서 현실 해법은 "둘 중 하나"가 아니라 **역할 분리**다 — 제어/전송은 JSON, **분석/반출은 Parquet**.

---

## 7. Synapse에 매핑 — 무엇을 바꿀까

1. **익스포트 번들에 Parquet 옵션 추가.** SDK가 소비하는 `raw_bundle_v1`(행 JSON)은 유지하되, "분석용 export"는 Parquet로. 스칼라 메타 + 셔레딩된 annotation 테이블을 함께 내보내면, 다운스트림 통계가 수백 배 빨라지고 전송량은 1/17로 준다.
2. **분석 읽기 경로를 운영 DB에서 분리(레이크하우스 맛보기).** 무거운 집계(프로젝트별 진척, 라벨 분포, 검수 통계)를 PostgreSQL에서 돌리지 말고, **주기적으로 S3에 Parquet로 떨궈 DuckDB/Trino로** 질의. OLTP(운영)와 OLAP(분석)를 섞지 않는다 — 2편 OOM의 근본 예방이기도 하다.
3. **Arrow로 무복사(zero-copy) 전달.** 플러그인↔백엔드 대량 전달을 JSON 직렬화 대신 **Arrow IPC**로 하면 파싱 비용 자체가 사라진다(통증 5·7 동시 완화).
4. **파티셔닝.** `s3://exports/project_id=18/date=2026-07-05/*.parquet` 처럼 자주 거르는 축으로 파티션하면 **파일 단위로 건너뛰기**(partition pruning)가 공짜로 붙는다.

---

## 8. 직접 해보기

스크립트 4개(`gen.py`, `convert.py`, `bench.py`, `meta.py`)는 `/tmp/duckdb-demo`에 있다.

```bash
cd /tmp/duckdb-demo
python3 gen.py                                   # 324MB JSONL 생성
uv run --with duckdb python3 convert.py          # Parquet/셔레딩 변환 + 크기
uv run --with duckdb python3 bench.py            # JSON vs Parquet 성능
uv run --with duckdb python3 meta.py             # 컬럼별 크기(projection 근거)
```

**스스로 해볼 실험 3개:**
1. `num_annotations`를 5~40 → 50~200으로 키워 `data`를 무겁게 하면, 스칼라 쿼리(Q1) 격차가 어떻게 벌어지나? (JSON은 더 느려지고 Parquet은 그대로 — 격차가 커진다)
2. `records.snappy.parquet` vs `records.zstd.parquet`의 크기/속도 트레이드오프 비교(snappy는 크지만 CPU 저렴).
3. Parquet을 `project_id`로 파티션해 저장한 뒤 `WHERE project_id=7` 쿼리가 얼마나 더 빨라지는지(partition pruning).

정리:
```bash
rm -rf /tmp/duckdb-demo
```

---

## 9. 마무리 — 시리즈를 닫으며

3부작을 한 문장으로 잇는다.

- **1편(진단)**: 느림·에러의 뿌리는 대개 **데이터 평면이 제어 평면을 통과**하거나 **필요 없는 데이터를 읽어서**다.
- **2편(OOM)**: 무거운 컬럼을 정렬/DISTINCT 키에 넣자 `EXPLAIN ANALYZE`가 453MB spill을 실토했다.
- **3편(포맷)**: 같은 데이터를 컬럼너로 두자 **읽지 않아도 될 것을 읽지 않아** 480배가 갈렸다.

세 편의 교훈이 결국 하나로 모인다 — **"필요한 것만, 필요한 만큼 읽어라(read less)."** 무엇을 읽지 않을지를 설계하는 것, 그게 데이터 엔지니어링의 절반이다.

다음엔 이 조각들을 실제 파이프라인으로 엮는 이야기(오케스트레이션·멱등성·백필)로 넘어가려 한다. 1편의 학습 로드맵 4단계다.

---

*실측은 DuckDB(uv 격리 실행), 동일 머신, 10만 건/225만 annotation 데이터로 수행했다. 모든 수치(크기 324MB→18.6MB, 시간 482×/438×/468×, status 컬럼 0.17%)는 실제 실행 출력이다. warmup 후 best-of-3, threads=4로 공정 비교했다.*
