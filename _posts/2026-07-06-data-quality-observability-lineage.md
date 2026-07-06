---
title: "[데이터 엔지니어링 실습] 이 숫자를 믿어도 되나 — 데이터 품질·관측성·계보를 Postgres/sqlglot로 재현"
date: 2026-07-06T22:30:00+09:00
categories: Data-Engineering
tags:
  - DataEngineering
  - 데이터엔지니어링
  - DataQuality
  - 데이터품질
  - Observability
  - 관측성
  - Lineage
  - 계보
  - Reconciliation
  - AnomalyDetection
  - Freshness
  - sqlglot
  - dbt
  - OpenLineage
  - WriteAuditPublish
  - 실습
toc: true
toc_sticky: true
---

> 데이터 엔지니어링 진단 시리즈 **5편(완결)**. 1편 로드맵 6단계다.
> - 1편 [\[진단\]]({% post_url 2026-06-16-data-engineering-diagnosis-synapse %}) · 2편 [\[OOM\]]({% post_url 2026-06-16-oom-reproduction-explain-analyze %}) · 3편 [\[Parquet\]]({% post_url 2026-07-05-duckdb-parquet-vs-json %}) · 4편 [\[오케스트레이션\]]({% post_url 2026-07-06-pipeline-orchestration-idempotency-backfill %})
>
> 4편까지 파이프라인은 **빠르게, 안전하게** 돈다. 그런데 남은 질문 — **"그 결과의 숫자를 믿어도 되나?"** 파이프라인이 성공(exit 0)했다고 데이터가 맞는 건 아니다. 이 마지막 편은 **믿음을 검증**하는 세 축을 Postgres와 sqlglot로 직접 돌려본다.

---

## 0. TL;DR

4편에서 쓰던 파이프라인 데이터(`source_events → daily_summary`)에 버그를 심고, 세 축으로 잡아냈다.

| 축 | 무엇 | 결과(실측) |
|---|---|---|
| **데이터 품질** | 6종 어서션 배터리 | 정상 **6/6 PASS**, 오염 **5개 FAIL**(중복·음수·source초과·고아·합계불일치 27) |
| **관측성** | 볼륨 이상탐지 / 신선도 | 처리량 급락 z-score **−44.03 → ANOMALY**, watermark 30h → **STALE** |
| **계보** | sqlglot 컬럼 계보 + 그래프 | `status` 변경 → **dashboard·ml까지 영향**, 체크 실패 → **원천 컬럼 역추적** |

핵심 한 줄: **"파이프라인이 '성공'한 것과 데이터가 '맞는' 것은 다르다. 품질은 맞는지, 관측성은 지금 상태가 어떤지, 계보는 어디서 와서 어디로 가는지를 답한다."**

---

## 1. "돌긴 하는데 믿어도 되나?"

운영에서 가장 무서운 건 에러가 아니라 **조용히 틀린 데이터(silent data error)** 다. 잡은 초록불(성공)인데 대시보드 숫자가 틀리고, 그 위에서 의사결정과 모델 학습이 굴러간다. 이걸 막는 세 축:

1. **데이터 품질(Quality)** — *데이터가 맞나?* 규칙(제약)을 명시하고 위반을 잡는다.
2. **관측성(Observability)** — *지금 파이프라인/데이터 상태가 어떤가?* 지표·로그로 이상을 조기 감지.
3. **계보(Lineage)** — *이 값은 어디서 왔고 어디에 쓰이나?* 영향분석·근본원인 추적.

2편의 OOM, 4편의 중복 집계는 전부 **"틀린 결과가 통과할 수 있었다"** 는 공통점이 있었다. 이 세 축이 그 안전망이다.

---

## 2. 실습 환경

4편의 격리 Postgres(포트 55432) `pipeline` DB를 재사용한다. 먼저 **정답(truth)** 을 source에서 full refresh로 만들고(멱등), 버그 4종을 심은 **오염 사본**을 만들었다.

```sql
-- 정답: source_events 로부터 재계산 (day×project 완료 집계)
TRUNCATE daily_summary;
INSERT INTO daily_summary(day, project_id, completed_count)
SELECT event_time::date, project_id, count(*) FILTER (WHERE status='completed')
FROM source_events GROUP BY 1,2 HAVING count(*) FILTER (WHERE status='completed') > 0;

-- 오염 사본(PK 없음) + 버그 주입
CREATE TABLE daily_summary_bad AS SELECT * FROM daily_summary;
UPDATE daily_summary_bad SET completed_count=50 WHERE day='2026-07-01' AND project_id=1;  -- ① 부풀림
UPDATE daily_summary_bad SET completed_count=-3 WHERE day='2026-07-01' AND project_id=2;  -- ② 음수
INSERT INTO daily_summary_bad VALUES ('2099-01-01', 9, 5, now());                          -- ③ 고아
INSERT INTO daily_summary_bad SELECT * FROM daily_summary_bad WHERE day='2026-07-03' AND project_id=3;  -- ④ 중복
```

---

## 3. DEMO 1 — 데이터 품질: 규칙을 명시하고 위반을 잡는다

품질 체크는 결국 **"참이어야 하는 명제(assertion)"** 를 SQL로 쓰고 위반 수를 세는 것이다. 6종 배터리를 만들었다(대상은 뷰 `dq_target`으로 스왑).

```sql
WITH truth AS (   -- source 로부터의 정답 재계산 (reconciliation 기준)
  SELECT event_time::date AS day, project_id, count(*) FILTER (WHERE status='completed') AS c
  FROM source_events GROUP BY 1,2
)
SELECT 'C1 grain_uniqueness'      , count(*) FROM (SELECT day,project_id FROM dq_target GROUP BY 1,2 HAVING count(*)>1) d
UNION ALL SELECT 'C2 not_null'    , count(*) FROM dq_target WHERE day IS NULL OR project_id IS NULL OR completed_count IS NULL
UNION ALL SELECT 'C3 non_negative', count(*) FROM dq_target WHERE completed_count < 0
UNION ALL SELECT 'C4 not_exceed_source'    , count(*) FROM dq_target t LEFT JOIN truth s USING(day,project_id) WHERE t.completed_count > COALESCE(s.c,0)
UNION ALL SELECT 'C5 referential_to_source', count(*) FROM dq_target t LEFT JOIN truth s USING(day,project_id) WHERE s.day IS NULL
UNION ALL SELECT 'C6 reconciliation_sum'   , abs(COALESCE((SELECT sum(completed_count) FROM dq_target),0) - (SELECT sum(c) FROM truth));
```

**정상 테이블 vs 오염 테이블 실측:**

```text
        check_name        | 정상 | 오염
--------------------------+------+------
 C1 grain_uniqueness      | PASS | FAIL (violations=1)   ← 중복 grain
 C2 not_null              | PASS | PASS
 C3 non_negative          | PASS | FAIL (1)              ← 음수
 C4 not_exceed_source     | PASS | FAIL (2)              ← 부풀림 + 고아
 C5 referential_to_source | PASS | FAIL (1)              ← 고아(source에 없음)
 C6 reconciliation_sum    | PASS | FAIL (diff=27)        ← 합계 불일치
```

체크의 유형학(이게 곧 dbt test / Great Expectations의 카테고리다):

| 체크 | 유형 | 무엇을 지키나 |
|---|---|---|
| C1 | **uniqueness** | grain 무결성(4편 멱등성의 사후 검증) |
| C2 | **not-null** | 필수값 |
| C3 | **range/domain** | 값의 범위(음수 불가) |
| C4 | **logical** | 도메인 규칙(완료≤전체) |
| C5 | **referential** | 상류 참조 무결성(고아 없음) |
| C6 | **reconciliation** | 원천과 합계 대사 — **가장 강력** |

> **핵심** — C6 대사(reconciliation)는 4편 DEMO A/E1의 **중복 집계 버그를 정확히 잡는 체크**다. "파생 테이블 합계 == 원천에서 재계산한 합계"는 조용한 오류를 막는 최후의 방어선이다.

**어떻게 파이프라인에 붙이나 — Write-Audit-Publish(WAP)**: 결과를 ①스테이징에 쓰고(write) → ②품질 배터리로 검사(audit) → ③통과할 때만 프로덕션으로 승격(publish). 실패하면 **회로 차단(circuit breaker)** 으로 나쁜 데이터의 하류 전파를 막는다. 실무 도구: **dbt test / Great Expectations / Soda / Deequ**.

---

## 4. DEMO 2 — 관측성: 지금 상태를 지표로 본다

애플리케이션에 로그·메트릭·트레이스가 있듯, 데이터에도 관측성 신호가 있다. Monte Carlo가 정리한 **신선도·볼륨·스키마·분포** 4축이 대표적. 여기선 **실행 로그 → 볼륨 이상 → 신선도**를 실측했다.

**(1) 실행 로그(run log)** — 매 실행의 처리량·소요·체크 실패 수를 남긴다. 14일 정상 + 1일 이상:

```text
 run_id |    day     | rows_out | duration_ms | checks_failed
--------+------------+----------+-------------+---------------
     15 | 2026-07-05 |       95 |         640 |             5   ← 급락 + DQ 5건 실패
     14 | 2026-07-04 |      300 |        1195 |             0
     ...
```

**(2) 볼륨 이상탐지(z-score)** — 직전 14개 대비 표준화 점수, `|z|>3`이면 이상:

```sql
WITH stats AS (
  SELECT run_ts::date AS day, rows_out,
         avg(rows_out) OVER w AS mean, stddev_samp(rows_out) OVER w AS sd
  FROM pipeline_run_log
  WINDOW w AS (ORDER BY run_id ROWS BETWEEN 14 PRECEDING AND 1 PRECEDING))
SELECT day, rows_out, round(mean,1) mean, round(sd,1) sd,
       round((rows_out-mean)/NULLIF(sd,0),2) AS zscore,
       CASE WHEN abs((rows_out-mean)/NULLIF(sd,0))>3 THEN 'ANOMALY' ELSE 'ok' END FROM stats ...;
```

```text
    day     | rows_out | mean  | sd  | zscore  |  flag
------------+----------+-------+-----+---------+---------
 2026-07-05 |       95 | 302.1 | 4.7 | -44.03  | ANOMALY ❗
 2026-07-04 |      300 | 302.3 | 4.9 |  -0.48  | ok
```

처리량이 302±4.7에서 95로 급락 → **z=−44**. "성공했지만 데이터의 2/3가 사라진" 상황을 상태값(exit code)이 아니라 **분포**로 잡는다.

**(3) 신선도 SLA** — `now() - watermark`가 임계를 넘으면 STALE:

```text
        last_watermark         |         lag          | freshness
-------------------------------+----------------------+-----------
 2026-07-04 23:21:06+00        | 1 day 06:00:00       | STALE ❗   (임계 24h)
```

실무 도구: **dbt artifacts + Elementary**, **Monte Carlo / Metaplane**(자동 이상탐지), 지표 자체는 **Prometheus/Grafana**로 대시보드화.

---

## 5. DEMO 3 — 계보: 어디서 왔고, 어디로 가나

계보는 두 질문에 답한다 — **영향분석**("이 원천을 바꾸면 뭐가 깨지나?")과 **근본원인**("이 값이 틀렸는데 어디를 봐야 하나?").

**(1) sqlglot로 SQL에서 컬럼 계보 자동 추출** — 우리 집계 SQL을 파싱:

```python
from sqlglot.lineage import lineage
node = lineage("day", SQL, dialect="postgres")   # day 컬럼의 뿌리 추적
```

```text
  day             ← ['source_events.event_time']
  completed_count ← ['completed_count']      # COUNT(*) 는 단일 컬럼이 아니라 집계 → 뿌리가 자기 자신
  참조 테이블: ['source_events']
  참조 컬럼  : ['event_time', 'project_id', 'status']  # status → WHERE, event_time/project_id → GROUP
```

> **정직한 한계** — `completed_count = COUNT(*)`는 특정 컬럼이 아니라 **행 수 집계**라 자동 계보가 단일 원천 컬럼으로 안 떨어진다. 그래서 실무에선 **파싱 기반 참조 추출 + 선언적 그래프**를 함께 쓴다(자동화가 만능이 아님을 아는 게 중요).

**(2) 의존 그래프로 영향분석·근본원인 추적:**

```python
EDGES = {
  "source_events.status":         ["daily_summary.completed_count"],
  "daily_summary.completed_count":["dashboard.throughput", "ml.training_set"], ...}
```

```text
  영향분석  source_events.status 스키마 변경 시 깨지는 하류:
     → daily_summary.completed_count → dashboard.throughput → ml.training_set

  근본원인  daily_summary.completed_count 체크 실패(C6) 시 의심 상류:
     ← source_events.status
```

`status` 컬럼 하나를 바꾸면 대시보드와 **모델 학습셋까지** 영향을 받는다는 걸 배포 전에 안다. 반대로 C6가 터지면 어디를 파야 할지 즉시 좁혀진다. 실무 도구: **OpenLineage + Marquez**(표준·수집), **dbt docs**(DAG 시각화), **DataHub / Amundsen**(카탈로그).

---

## 6. 세 축을 하나의 루프로

셋은 따로가 아니라 하나의 신뢰 루프다.

```
   계보(Lineage)         품질(Quality)          관측성(Observability)
   무엇이 영향받나?  →   규칙 위반 잡기    →   언제·얼마나 벗어났나
        ↑                                            │
        └──────────── 근본원인으로 되짚기 ←────────────┘
```

- **배포 전**: 계보로 변경의 영향 범위를 본다(스키마 변경이 뭘 깨나).
- **실행 중**: WAP로 품질 게이트 통과 시에만 publish(회로 차단).
- **실행 후**: 관측성으로 신선도·볼륨·분포를 감시, 이상 시 계보로 근본원인 역추적.
- **원천에서**: **데이터 계약(data contract)** — 생산자가 스키마·의미·SLA를 약속해 애초에 깨짐을 막는다.

---

## 7. Synapse에 매핑

1. **익스포트 대사(reconciliation).** 익스포트한 레코드 수/합계가 원천 쿼리 결과와 일치하는지 매 잡 끝에 검증(2·4편 버그의 사후 안전망).
2. **잡 관측성.** Celery 잡의 처리량·소요·실패율을 run-log로 남기고 z-score 이상탐지. "성공했지만 절반만 처리"를 잡는다.
3. **신선도 SLA.** agent health, 마지막 성공 잡, 익스포트 최신성에 freshness 임계.
4. **플러그인 파라미터 계약.** 플러그인 params/결과에 스키마 계약(pydantic)으로 not-null·domain·range 강제 → 1편의 "다양한 에러" 상당수는 계약 부재.
5. **계보 카탈로그.** 플러그인 → 데이터셋 → 모델의 의존을 OpenLineage로 수집해, 데이터셋 변경이 어느 모델 학습에 영향 주는지 추적.

---

## 8. 원칙 정리

1. **성공 ≠ 정확** — exit 0은 데이터 정확성을 보장하지 않는다.
2. **규칙을 코드로(assertions)** — uniqueness·not-null·range·logical·referential·**reconciliation**.
3. **Reconciliation이 왕** — 원천과의 합계 대사가 조용한 오류의 최후 방어선.
4. **Write-Audit-Publish** — 통과할 때만 승격, 실패 시 회로 차단.
5. **데이터에도 관측성** — 신선도·볼륨·스키마·분포 4신호.
6. **계보로 앞뒤를 본다** — 영향분석(forward) + 근본원인(backward).
7. **자동화의 한계를 알라** — 자동 계보/이상탐지는 보조. 도메인 규칙은 사람이 명시한다.

---

## 9. 직접 해보기

```bash
PSQL="/usr/lib/postgresql/17/bin/psql -h 127.0.0.1 -p 55432 -U demo -d pipeline"
# DQ: 뷰를 스왑하며 배터리 실행
$PSQL -c "CREATE OR REPLACE VIEW dq_target AS SELECT * FROM daily_summary;"     && $PSQL -f dq_battery.sql
$PSQL -c "CREATE OR REPLACE VIEW dq_target AS SELECT * FROM daily_summary_bad;" && $PSQL -f dq_battery.sql
# 계보(sqlglot)
uv run --with sqlglot python3 lineage.py
```

**스스로 해볼 실험:**
1. C6 대사를 "합계"가 아니라 "셀별 diff"로 바꿔, 어느 (day,project)가 틀렸는지 정확히 짚기.
2. z-score 대신 **IQR**(사분위) 기반 이상탐지로 바꿔 이상치 민감도 비교.
3. `daily_summary`에 두 번째 파생(예: `weekly_summary`)을 추가하고 계보 그래프/영향분석을 확장.

정리:
```bash
$PSQL -d postgres -c "DROP DATABASE pipeline;"
/usr/lib/postgresql/17/bin/pg_ctl -D /tmp/pg-oom-demo stop && rm -rf /tmp/pg-oom-demo /tmp/duckdb-demo /tmp/pipeline-demo
```

---

## 10. 마무리 — 5부작을 닫으며

막연했던 한 문장, *"API만 쓰니 느리고, 불필요한 통신 같고, 에러가 잦다"* 에서 출발해 여기까지 왔다.

| 편 | 질문 | 핵심 |
|---|---|---|
| 1 진단 | 뭐가 문제지? | 데이터 평면 ≠ 제어 평면 / 7가지 통증 |
| 2 OOM | 왜 터지지? | wide 컬럼을 정렬 키에 → 453MB spill |
| 3 Parquet | 왜 느리지? | 컬럼너로 안 읽을 걸 안 읽어 480배 |
| 4 오케스트레이션 | 다시 돌려도 되나? | 멱등성 = at-least-once + idempotent |
| 5 품질·관측·계보 | 믿어도 되나? | reconciliation·이상탐지·계보 |

관통하는 세 문장:
- **"데이터를 옮기지 말고 주소를 옮겨라"** (1·3편)
- **"필요한 것만, 필요한 만큼 읽어라(read less)"** (2·3편)
- **"다시 돌려도 안전하게, 그리고 결과를 의심하라(re-run safe & verify)"** (4·5편)

나는 데이터 엔지니어링을 "잘 몰랐"지만, 다섯 번의 실습으로 이제 안다 — **그것은 결국 "데이터를 덜 옮기고, 덜 읽고, 안전하게 다시 돌리고, 그 결과를 증명하는" 기술**이다. 다음은 이 조각들을 실제 Synapse에 하나씩 반영하는 실전이다. 1편의 도서 목록(*DDIA*, *Fundamentals of Data Engineering*, *The Art of PostgreSQL*)과 함께, 계속 파나가려 한다.

*— 시리즈 끝. 읽어주셔서 감사합니다.*

---

*모든 실습은 PostgreSQL 17.4 격리 클러스터 + sqlglot(uv 격리 실행)으로 수행했고, 인용 수치(DQ 5 FAIL/diff 27, z=−44.03, freshness 30h STALE, 계보 추적)는 실제 실행 출력이다.*
