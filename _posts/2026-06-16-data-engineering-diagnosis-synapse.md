---
title: "[데이터 엔지니어링 진단] API로만 데이터를 나르다 만난 벽 — Synapse(Django+Ray+Agent) 시스템을 데이터 엔지니어링 관점으로 해부하기"
date: 2026-06-16T21:00:00+09:00
categories: Data-Engineering
tags:
  - DataEngineering
  - 데이터엔지니어링
  - Django
  - Ray
  - FastAPI
  - MLOps
  - ControlPlane
  - DataPlane
  - ObjectStorage
  - Streaming
  - Backpressure
  - Idempotency
  - Checkpoint
  - KeysetPagination
  - Parquet
  - Arrow
  - PostgreSQL
  - OOM
  - 학습로드맵
toc: true
toc_sticky: true
---

> 이 글은 내가 운영 중인 **Synapse**(데이터 라벨링 / MLOps 플랫폼)를 _데이터 엔지니어링_ 관점으로 꼼꼼히 진단한 기록이다.
> 시스템은 **Django 백엔드 + Nuxt 프론트 + Ray(Job/Serve) + FastAPI Agent + Python SDK(플러그인 런타임)** 으로 구성된다.
> "모든 통신을 API로만 하다 보니 느리고, 불필요한 통신이 느껴지고, 다양한 에러를 만난다"는 평소의 막연한 불편함을,
> **실제 코드 근거**와 함께 데이터 엔지니어링의 언어로 번역하는 것이 목표다.
>
> 진단 대상 코드는 `synapse-backend`(main, `808871f6` 기준), `synapse-sdk`, `synapse-agent` 세 레포의 최신 main 이다.

---

## 0. 30초 요약 (TL;DR)

1. **막연히 "API가 느리다"고 느낀 정체는 대부분 "데이터 평면(data plane)과 제어 평면(control plane)을 분리하지 않았기 때문"이다.** 대용량 바이트가 *명령을 주고받는 통로(REST API)* 를 그대로 통과하면 느리고 위험하다.
2. 실제로 **가장 잘 만든 경로는 이미 이 원칙을 지키고 있다.** 업로드의 presigned URL 직접 전송, 익스포트의 "스토리지에 번들 쓰고 URI만 돌려주기"가 그 예다.
3. 반대로 **통증이 있는 경로는 모두 같은 안티패턴을 공유한다.** ①파일/레코드 1건당 API 1번(chatty API), ②전체를 메모리에 올림(unbounded memory), ③무거운 JSON 컬럼을 정렬/DISTINCT 키로 끌고 감(최근 OOM 사건의 진짜 원인), ④체크포인트 없이 긴 작업을 돌려 죽으면 처음부터.
4. 해법의 키워드는 **경계 메모리(bounded memory) · 스트리밍 · 배칭 · 백프레셔 · 멱등성/체크포인트 · 올바른 포맷(컬럼너) · 데이터 지역성**. 전부 "큰 책 한 권"으로 수렴한다 → *Designing Data-Intensive Applications*.
5. 아래에 **7가지 통증 진단 → 해결 패턴 → 6개월 학습 로드맵 → 도서 추천 → 30/60/90일 액션**을 적었다.

---

## 1. 먼저, 시스템을 "데이터가 흐르는 길"로 다시 그리기

코드를 컴포넌트(Django/Ray/Agent)로 보면 구조가 보이지만, **데이터 엔지니어링은 "바이트가 어디서 생겨 어디로, 어떤 통로로 흐르는가"로 본다.** 같은 시스템을 데이터 흐름으로 다시 그리면 이렇게 된다.

```
                         ┌──────────────────────────────────────────┐
                         │            제어 평면 (Control Plane)        │
                         │   "무엇을, 누가, 언제" — 작고 빈번한 메시지    │
                         └──────────────────────────────────────────┘
   [브라우저/CLI] ──REST──> [Django Backend] ──enqueue──> [Celery Worker]
        │                       │   ▲                          │
        │                       │   │ job 상태/진행/로그          │ (대용량 ORM)
        │                       ▼   │                          ▼
        │                  [PostgreSQL]                  [Object Storage]
        │                  Task.data(JSON, 행당 ~4.3MB!)   S3 / MinIO / GCS
        │                                                      ▲   ▲
   presigned PUT  ────────(데이터 평면, 바이트 직접)────────────┘   │
        │                                                          │
        ▼                                                          │
   [Synapse Agent(FastAPI)] ──submit──> [Ray Cluster] ── plugin ──┘
        │  health 30s / long-poll 65s        │ Serve / Job / Actor
        │  로그 스트리밍(WS·SSE)               │ (SDK가 여기서 실행되어 다시 Backend API 호출)
        └────────────────────────────────────┘
```

> **데이터 엔지니어링의 첫 번째 렌즈 — Control Plane vs Data Plane**
> - **제어 평면**: "이 작업 시작해", "지금 30% 됐어", "끝났어" 같은 **작고 빈번한 메타데이터**. REST/JSON이 적합.
> - **데이터 평면**: 실제 이미지·라벨·데이터셋 **수십 GB의 바이트**. 이건 *절대* 제어 평면(API 서버, 오케스트레이터)을 통과시키면 안 된다. 객체 스토리지로 **직접** 흘려야 한다.
>
> 당신이 "API가 느리다"고 느끼는 거의 모든 순간은, 사실 **데이터 평면 트래픽이 제어 평면 통로로 새어 들어온 순간**이다.

이 한 장의 그림과 한 개의 렌즈를 머리에 넣고, 이제 5개의 실제 경로를 따라가 보자.

---

## 2. 데이터가 흐르는 5개의 실제 경로 (코드 기준)

### 경로 ①: 업로드(수집, ingestion) — *잘 만든 길과 위험한 길이 공존*

SDK의 업로드 액션(`synapse_sdk/plugins/actions/upload/`)은 두 갈래로 갈린다.

**(A) presigned URL 직접 업로드 — 모범 사례 ✅**
스토리지가 S3/GCS/MinIO/Azure면 이 경로를 탄다.

```python
# upload/strategies/upload_strategy.py
if config.use_presigned and not has_remote_files:
    if self._storage_supports_presigned(storage):
        return self._upload_with_presigned_urls(filtered_files, config)
...
result = client.upload_files_bulk(file_paths, batch_size=200, on_progress=callback)
```

- 파일 **바이트는 Django를 통과하지 않고** 스토리지로 직행한다(데이터 평면 분리 ✅).
- 체크섬 중복 확인도 `verify_checksums_batch`로 **1000개씩 묶어** 호출한다(배칭 ✅).
- presigned 발급도 **200개 배치**로 묶는다(배칭 ✅).

**(B) 전통(traditional) 업로드 — chatty API ⚠️**
원격 경로(예: Synology) 등 presigned가 불가능하면 폴백한다.

```python
# upload/strategies/upload_strategy.py  (전통 경로)
for idx, organized_file in enumerate(files):
    ...
    uploaded = client.upload_data_file(organized_file, ...)   # 파일 1건 = API 1번, 순차
```

파일 1만 개 = **HTTP 요청 1만 번**, 그것도 직렬. 요청당 100~500ms 왕복만 쳐도 순수 오버헤드가 1,000~5,000초다.

**(C) DataUnit 생성 — 전략에 따라 1건씩 vs 50건씩**
업로드한 파일을 "데이터 단위"로 묶어 백엔드에 등록하는 단계도 두 전략이 있다.

```python
# upload/strategies/data_unit.py
class SingleDataUnitStrategy(...):
    def generate(..., batch_size: int = 1):       # 항상 1
        created = client.create_data_units([uploaded_file])   # 1건당 API 1번

class BatchDataUnitStrategy(...):
    def generate(..., batch_size: int = 50):       # 기본 50
        batches = get_batched_list(uploaded_files, batch_size)
        created = client.create_data_units(batch)             # 50건당 API 1번
```

`Single` 전략을 쓰면 1만 파일 = `create_data_units` 1만 번, `Batch(50)`이면 200번. **40~50배 차이**다.

**(D) 체크섬 — 파일을 통째로 메모리에 올려 MD5 ⚠️**

```python
# upload/utils/checksum.py — calculate_checksums_parallel()
# ThreadPoolExecutor(max_workers=10) 로 각 워커가 파일 "전체"를 읽어 MD5
```

500MB 파일 × 워커 10개 = 순간 **5GB** 메모리 스파이크. 스트리밍(8KB씩 읽어 갱신)하지 않는다.

> 50MB 이상 단일 파일은 backend의 chunked-upload(`CHUNKED_UPLOAD_THRESHOLD = 50MB`)로 넘겨 SDK가 통째로 버퍼링하지 않는 점은 좋다.

---

### 경로 ②: 익스포트(반출, egress) — *최근 터진 OOM 사건의 전말*

이 경로가 가장 교훈적이다. 바로 며칠 전(`SYN-7172`, 커밋 `808871f6`) "**plugin_export search export OOM**"을 고친 흔적이 main에 그대로 있다. 이 사건 하나에 데이터 엔지니어링 핵심 개념 4개가 압축돼 있다.

**무대 설정**: 어떤 프로젝트는 `Task` 9.7만 건, 그리고 **`Task.data`(JSONField) 한 행이 평균 약 4.29MB**다. 검색 조건이 붙은 익스포트가 워커를 OOM으로 죽였다.

**진짜 원인 1 — 무거운 JSON을 DISTINCT/GROUP BY 키로 끌고 갔다.**
검색을 이렇게 걸고 있었다(개념):

```python
# (구) to-many 필드 검색 → 중복 행 방지 위해 .distinct()
Task.objects.filter(assignments__user__email__icontains=term).distinct()
```

`assignments` 같은 *to-many* 조인은 행을 뻥튀기(fan-out)하므로 `.distinct()`가 필요한데, PostgreSQL의 DISTINCT/GROUP BY는 **정렬 키에 SELECT된 모든 컬럼**을 끌고 들어간다. 그 안에 4.29MB짜리 `data` 텍스트가 섞이면:

- `work_mem`(예: 64MB)를 가볍게 초과 → **디스크로 temp 파일 spill**
- 그게 `temp_file_limit`마저 초과 → `total_count` 추정 쿼리가 **abort**

커밋 메시지 원문 그대로:
> *"DISTINCT/GROUP BY 키로 wide JSON 컬럼을 끌어들여 work_mem 을 초과, temp 파일 spill 로 total_count 추정 쿼리를 abort 시킨다."*

**진짜 원인 2 — chunk 크기 × 행 크기 = 메모리 폭탄.**
검색 동반 시 청크 기본값을 `_SEARCH_CHUNK_SIZE = 2000`으로 올려 두었는데:

> 2000 × 4.29MB ≈ **8.6GB** → 6Gi cgroup 워커가 **OOMKilled** → 첫 체크포인트 flush 전에 죽음 → `checkpoint`가 비어 **resumable 불가** → reaper가 그냥 FAILED 처리.

**고친 방법 (= 그대로 학습 교재):**

1. **ID만 뽑는 경량 매칭 쿼리로 분리(프로젝션 푸시다운)** — wide JSON을 검색 쿼리에서 빼버린다.
   ```python
   search_ids = match_qs.values('id')          # id만! data 컬럼 안 끌고 옴
   return qs.filter(id__in=search_ids)         # 본 쿼리엔 distinct 불필요
   ```
2. **JOIN+DISTINCT 대신 EXISTS 서브플랜** — fan-out 자체를 안 만들어 DISTINCT가 필요 없다.
   ```python
   Exists(model._default_manager.filter(Q(**{lookup: term}), pk=OuterRef('pk')))
   ```
3. **청크 크기를 250으로 통일** — 250 × 4.29MB ≈ **1.07GB**. 예산 안. 게다가 첫 청크가 빨리 끝나 **첫 체크포인트가 flush**되므로 죽어도 이어서 재개 가능.
4. **JSONField(`*__meta`) 검색은 익스포트에서 제외** — `icontains`가 `upper((meta)::text) LIKE '%term%'` 선행 와일드카드 풀스캔이라 인덱스가 무용. (list와의 parity를 일부 포기하는 합의된 결정.)

부수적으로 익스포트는 **keyset 페이지네이션**(`WHERE id > cursor ORDER BY id LIMIT N`)으로 순회한다. `.iterator()`(서버 사이드 커서)는 **PgBouncer transaction pooling 때문에 금지**라, 명시적 keyset 루프를 쓴다. 그리고 part 파일 단위로 체크포인트를 남겨 **resumable**하게 만들었고, Celery 큐도 `cpu_bound`(concurrency=1, 10Gi, max-tasks-per-child=1) 전용으로 격리해 동시 OOM 폭주를 막았다.

> 여기 한 사건에 **predicate/projection pushdown, EXISTS vs JOIN+DISTINCT, work_mem/temp spill, bounded memory(청크×행), 체크포인트/멱등 재개, keyset vs offset, 커넥션 풀링 제약**이 전부 들어 있다. 이게 데이터 엔지니어링이다.

---

### 경로 ③: 리스트 / 페이지네이션 — *읽기는 비교적 건강하다 ✅*

- 백엔드 list는 **cursor 페이지네이션 v2**(기본 50, 최대 200)로 offset의 깊은 페이지 문제를 피한다.
- list용 직렬화기는 무거운 JSON(`data`, `annotation_data` 등)을 `defer`하고 `select_related`/`prefetch_related`로 **N+1을 잘 피한다.**
- SDK 클라이언트의 `list(list_all=True)`는 **리스트가 아니라 제너레이터**를 반환해 페이지를 받는 즉시 흘려보낸다(전체 누적 안 함, bounded memory ✅).

다만 SDK 기본 `page_size=100`이라 10만 건이면 1,000번 호출이다. 페이지 크기는 익스포트 핸들러처럼 상황에 맞게 올릴 여지가 있다(트레이드오프: 페이지당 페이로드↑).

---

### 경로 ④: 플러그인 실행 — *데이터가 Agent를 "통과"한다 ⚠️*

Agent는 액션 `method`에 따라 3가지로 실행한다.

| 모드 | 통로 | 데이터 흐름 |
|---|---|---|
| **serve** | HTTP → Ray Serve | params를 HTTP 바디로 프록시(스트리밍 친화 ✅) |
| **task** | Ray Actor(동기) | params dict를 agent가 들고 있다가 Ray로 pickle 직렬화 |
| **job** | Ray Job(비동기) | params를 SQLite `QueuedJob.params`에 저장 후 dispatch |

`task`/`job` 모드에서 **큰 params(예: 수백 MB)** 는 ①FastAPI가 JSON 역직렬화 → ②dict로 메모리 보유 → ③Ray SDK가 다시 pickle 직렬화, 이렇게 **agent 프로세스를 거쳐 두세 번 버퍼링**된다. 스트리밍/청킹이 없어 agent가 의도치 않은 **데이터 평면 병목**이 될 수 있다.

> 교훈: **오케스트레이터에는 "데이터의 주소(URI/ObjectRef)"만 흘리고, 바이트는 흘리지 마라.**

---

### 경로 ⑤: 로그/진행상황 스트리밍 — *백프레셔가 없다 ⚠️*

```python
# agent app/websocket/logs.py
async for logs in client.tail_job_logs(job_id):
    store.append(job_id, logs)                  # 디스크 쓰기(블로킹)
    await websocket.send_text(json.dumps(...))  # 클라이언트로 전송
```

- Ray의 `tail_job_logs` 제너레이터엔 흐름 제어가 없다. WebSocket 소비자가 느리면 청크가 **메모리에 쌓인다**.
- `JOB_LOG_TTL_DAYS`, `JOB_LOG_MAX_BYTES`가 기본 **0(비활성)** 이라 디스크 로그가 자동 정리되지 않는다.
- health check 30초, long-poll 65초 폴링에 **jitter/지수 백오프가 없어**, 백엔드 복구 시 모든 agent가 동시에 몰리는 **thundering herd** 위험이 있다.
- SDK 진행상황/메트릭은 ~1초 throttle로 잘 눌러두었지만, 업로드 시 **파일/유닛 1건당 로그 이벤트**를 남기는 경로가 있어 1만 파일이면 1만 이벤트가 생긴다(부분 완화됨).

---

## 3. 꼼꼼한 진단 — 7가지 데이터 엔지니어링 통증

각 통증은 **증상 → 코드 근거 → 데이터 엔지니어링 개념 → 처방**으로 정리한다.

### 통증 1. Chatty API (네트워크판 N+1)
- **증상**: 업로드/등록이 데이터가 많을수록 비선형으로 느려진다.
- **근거**: 전통 업로드 `upload_data_file()` 루프, `SingleDataUnitStrategy`의 1건당 `create_data_units`.
- **개념**: 원격 호출의 비용은 **payload가 아니라 왕복 횟수(round trip)** 가 지배한다. *Little's Law*: 처리량 ≈ 동시성 / 지연. 직렬 + 1건당 = 동시성 1, 지연 누적.
- **처방**: ①벌크 엔드포인트로 묶기(이미 일부 존재), ②`BatchDataUnitStrategy`를 기본으로, ③폴백 경로도 thread pool/async로 동시성 부여, ④가능하면 presigned 직접 전송 우선.

### 통증 2. Unbounded Memory (전체를 메모리에 올림)
- **증상**: 큰 작업에서 OOMKilled, 워커 재시작.
- **근거**: 체크섬 full-file read × 10 워커, 익스포트 청크 2000 × 4.29MB.
- **개념**: **peak memory ≈ 동시성 × 배치 크기 × 행 크기**. 이 식을 항상 암산하라. 스트리밍/제너레이터/청킹으로 상수 메모리를 유지.
- **처방**: 스트리밍 MD5(8KB씩), 청크 크기를 메모리 예산에서 역산, 워커 수 동적 조정.

### 통증 3. Wide payload를 정렬/DISTINCT/GROUP BY 키로 끌고 감
- **증상**: 특정 검색+익스포트에서만 work_mem 초과·temp spill·쿼리 abort.
- **근거**: 경로 ②의 OOM 사건(수정 완료).
- **개념**: **Projection Pushdown**(필요한 컬럼만 SELECT) + **Predicate Pushdown**(필터를 데이터 가까이) + **EXISTS vs JOIN+DISTINCT**. 무거운 컬럼은 매칭 단계에서 배제하고 id로만 join.
- **처방**: 이미 적용됨. 일반 원칙으로 **"검색/정렬/집계 쿼리에 큰 JSON/BLOB 컬럼을 끌고 오지 않는다"** 를 코드 리뷰 체크리스트에 추가.

### 통증 4. 체크포인트/멱등성 부재 → 죽으면 처음부터
- **증상**: 긴 익스포트가 중간에 죽으면 non-resumable FAILED.
- **근거**: 첫 청크 전에 OOM → `checkpoint` 비어 재개 불가(수정 전).
- **개념**: 긴 작업은 **at-least-once + 멱등 쓰기 + 체크포인트**로 설계한다. part 파일 = 커밋 경계 = 재개 지점.
- **처방**: 이미 part 단위 체크포인트로 개선. 업로드/학습 등 **다른 긴 작업에도 동일 패턴 일반화**.

### 통증 5. 데이터 평면이 제어 평면/오케스트레이터를 통과
- **증상**: 큰 params/result가 agent·API를 거치며 느리고 메모리를 먹음.
- **근거**: agent task/job 모드의 params 이중 버퍼링, 플러그인 결과가 HTTP 응답 바디로 통째 반환.
- **개념**: **Data Locality / "move code to data"**. 바이트는 스토리지·Ray object store에 두고 **참조(URI/ObjectRef)** 만 주고받는다.
- **처방**: 큰 입출력은 presigned URI / Ray `ObjectRef` / manifest로 전달. API 응답엔 메타데이터만.

### 통증 6. 백프레셔 없는 스트리밍 + 폴링 thundering herd
- **증상**: 로그 폭주 시 메모리 증가, 백엔드 복구 시 트래픽 스파이크.
- **근거**: `tail_job_logs` 무제한 소비, 폴링에 jitter/백오프 없음.
- **개념**: **Backpressure**(소비자 속도에 맞춰 생산자 제어), **지수 백오프 + jitter**.
- **처방**: 크기 제한 큐(초과 시 오래된 청크 드롭/요약), 로그 TTL/상한 활성화, 폴링에 jitter·백오프 도입.

### 통증 7. 잘못된 전송 포맷 (행 기반 JSON으로 대량 전송)
- **증상**: 대량 데이터 전송/익스포트 시 직렬화·전송·메모리 비용이 큼.
- **근거**: `Task.data`가 행당 ~4.29MB JSON, 익스포트 번들도 행 기반.
- **개념**: **행(row) 포맷 vs 컬럼너(columnar)**. 분석/대량 반출은 **Parquet/ORC + Arrow(제로카피)** 가 압도적으로 효율적(압축·프로젝션·벡터화).
- **처방**: 익스포트 번들 포맷에 Parquet 옵션 추가 검토, 대량 분석 읽기는 DuckDB/Arrow로.

---

## 4. 그래서, 데이터 엔지니어링으로 어떻게 풀 것인가

### 4.1 머릿속에 박아야 할 7개의 멘탈 모델

1. **Control Plane / Data Plane 분리** — 명령은 API로, 바이트는 스토리지로.
2. **Bounded Memory** — `peak ≈ 동시성 × 배치 × 행크기`. 항상 상수 메모리를 노린다.
3. **Streaming over Materialization** — 제너레이터·keyset·청크. "전부 모으기" 금지.
4. **Batching over Chattiness** — 왕복 횟수를 줄여라(N+1을 네트워크에서도 의심).
5. **Backpressure** — 생산자는 소비자 속도를 존중.
6. **Idempotency & Checkpointing** — 재시도/재개가 안전한 설계.
7. **Right Format & Pushdown** — 컬럼너 + 필요한 것만(projection) + 가까이서 거르기(predicate).

### 4.2 Synapse에 바로 매핑한 구체 처방 (우선순위)

| 우선 | 처방 | 어느 통증 | 난이도 |
|---|---|---|---|
| 🔴 1 | `BatchDataUnitStrategy`를 기본값으로, 폴백 업로드에 동시성 부여 | 1 | 낮음 |
| 🔴 2 | 로그 스트리밍 크기제한 큐 + 로그 TTL/상한 활성화 | 6 | 낮음 |
| 🟠 3 | 스트리밍 체크섬(8KB 청크) + 워커 수 동적화 | 2 | 낮음 |
| 🟠 4 | "wide 컬럼을 정렬/DISTINCT에 넣지 않기"를 리뷰 체크리스트화 | 3 | 낮음 |
| 🟠 5 | 체크포인트/재개 패턴을 업로드·학습 등 긴 작업 전반에 일반화 | 4 | 중간 |
| 🟡 6 | 큰 params/result를 URI·ObjectRef·manifest 참조로 전달 | 5 | 중간 |
| 🟡 7 | 익스포트 번들에 Parquet 포맷 옵션, 분석 읽기에 DuckDB/Arrow | 7 | 중간 |
| 🟢 8 | 폴링에 지수 백오프 + jitter | 6 | 낮음 |

### 4.3 한 단계 위 — 아키텍처 진화 방향(중장기)

- **객체 스토리지를 "범용 데이터 평면"으로 승격**: 업로드뿐 아니라 익스포트·중간 산출물·대량 입력까지 전부 presigned + manifest. API는 "주소록"만 관리.
- **이벤트/메시지 브로커 도입 검토**: job 상태·로그를 폴링이 아니라 Kafka/Redis Streams 같은 브로커로(백프레셔·재처리·팬아웃이 공짜). CDC(Debezium)로 DB 변경을 파이프라인에 흘리는 것도 같은 계열.
- **분석 읽기 경로 분리**: 운영 DB(PostgreSQL)에서 무거운 집계를 떼어내 **레이크하우스(Parquet on S3) + DuckDB/Trino**로. OLTP와 OLAP를 섞지 않는다.
- **Ray Data 활용**: 플러그인 내 대량 변환을 SDK의 단일 프로세스 루프 대신 Ray Data 파이프라인으로(분산·스트리밍·백프레셔 내장).

---

## 5. 6개월 학습 로드맵 — "무엇을, 어떤 순서로"

> 원칙: **위에서 아래로**. 저장/메모리/쿼리의 1차 원리를 모르면, 상위 도구(Spark/Airflow/Kafka)는 "버튼 누르기"가 된다. 너의 통증은 전부 1~2단계에 뿌리가 있다.

**1단계 — 기초 원리 (3~4주) · 가장 중요**
- 운영체제/DB가 메모리·I/O를 다루는 법: 페이지 캐시, buffer pool, spill-to-disk.
- 관계형 내부: 인덱스(B-Tree), 조인 알고리즘, 쿼리 플래너, `work_mem`, **`EXPLAIN (ANALYZE, BUFFERS)`** 읽기.
- → 너의 OOM 사건을 `EXPLAIN ANALYZE`로 직접 재현/해부해 볼 것. (이게 최고의 실습)

**2단계 — 스토리지 & 파일 포맷 (2~3주)**
- 객체 스토리지(S3) 모델, presigned URL, multipart upload.
- 파일 포맷: **Parquet/ORC/Avro/Arrow**, 컬럼너의 원리, 압축, 파티셔닝.

**3단계 — 처리 엔진 (3~4주)**
- 배치: Spark의 실행 모델(셔플, 파티션, lazy DAG), DataFrame.
- SQL 엔진: **DuckDB**(로컬 분석의 끝판왕), Trino.
- **Ray / Ray Data**(이미 쓰는 도구이니 깊게).

**4단계 — 파이프라인 & 오케스트레이션 (2~3주)**
- Airflow/Dagster/Prefect의 DAG·멱등성·백필.
- 너의 Celery job을 "재개 가능한 멱등 파이프라인"으로 재설계해 보기.

**5단계 — 스트리밍 & CDC (2~3주)**
- Kafka, 파티션/오프셋, **exactly-once vs at-least-once**, 백프레셔, Debezium(CDC).

**6단계 — 데이터 모델링 & 신뢰성 (2~3주)**
- 차원 모델링(Kimball), 레이크하우스, dbt.
- 데이터 품질/관측성/계보(lineage), Great Expectations.

---

## 6. 기술도서 추천 — 통증별 처방전

> ⭐ 표시는 "지금 너에게 가장 먼저" 추천.

| 책 | 왜 너에게 (어느 통증) |
|---|---|
| ⭐ **Designing Data-Intensive Applications** — Martin Kleppmann | 이 글의 7개 멘탈 모델 전부의 출처. 분리/일관성/스트리밍/배칭의 바이블. **1순위.** |
| ⭐ **Fundamentals of Data Engineering** — Reis & Housley | 데이터 엔지니어링 "생애주기"의 전체 지도. 로드맵 잡기에 최적. |
| ⭐ **The Art of PostgreSQL** — Dimitri Fontaine | 통증 3의 정확한 처방. SQL·인덱스·플래너로 사고하는 법. |
| **Database Internals** — Alex Petrov | 통증 2·3의 1차 원리(저장 엔진, B-Tree, spill). |
| **High Performance Python** — Gorelick & Ozsvald | 통증 1·2(메모리 프로파일링, 제너레이터, 동시성)를 파이썬으로. |
| **Streaming Systems** — Akidau, Chernyak, Lax | 통증 6(백프레셔·워터마크·exactly-once)의 정본. |
| **Spark: The Definitive Guide** — Chambers & Zaharia | 분산 처리/셔플/DataFrame을 제대로. |
| **The Data Warehouse Toolkit** — Ralph Kimball | 4.3의 OLAP 분리·차원 모델링. |
| **Python for Data Analysis** — Wes McKinney | pandas/Arrow, 컬럼너 사고(통증 7). |
| **Designing Machine Learning Systems** — Chip Huyen | MLOps 맥락(플러그인=학습/배포)에서 데이터 흐름 설계. |

추가로 무료/온라인: **DuckDB 공식 docs**, **Apache Arrow docs**, **Ray Data docs**, PostgreSQL 공식 매뉴얼의 "Performance Tips" 장.

---

## 7. 30 / 60 / 90일 액션 플랜

**~30일 (이해 + 빠른 승리)**
- [ ] *DDIA* 1~4장, *The Art of PostgreSQL* 앞부분 읽기.
- [ ] OOM 사건을 `EXPLAIN (ANALYZE, BUFFERS)`로 직접 재현·해부 → 블로그 후속편.
- [ ] 빠른 승리 2개 적용: `BatchDataUnitStrategy` 기본화 / 로그 TTL·상한 활성화 + 크기제한 큐.

**~60일 (패턴 내재화)**
- [ ] 스트리밍 체크섬 + 워커 동적화(통증 2) 적용.
- [ ] "wide 컬럼 ↛ 정렬/DISTINCT" 리뷰 체크리스트 + 익스포트 외 긴 작업에 체크포인트 일반화.
- [ ] DuckDB로 익스포트 산출물을 Parquet로 읽어보는 PoC(통증 7).

**~90일 (아키텍처 진화)**
- [ ] 큰 params/result를 참조(URI/ObjectRef) 전달로 전환하는 설계서(통증 5).
- [ ] 분석 읽기 경로를 운영 DB에서 분리(레이크하우스 PoC)하는 ADR 작성.
- [ ] 폴링 → 이벤트/브로커 전환 타당성 검토(통증 6).

---

## 8. 마무리

처음의 막연한 불편함 — "API만 쓰니 느리고, 불필요한 통신 같고, 에러가 잦다" — 은 사실 **데이터 엔지니어링이 100년 가까이 다듬어 온 몇 개의 원칙**으로 거의 다 설명된다.

- 느린 건 대개 **데이터 평면이 제어 평면을 통과**하거나 **왕복이 너무 잦아서(chatty)** 다.
- 에러(특히 OOM)는 대개 **전체를 메모리에 올리거나, 무거운 컬럼을 정렬/DISTINCT에 끌고 가거나, 체크포인트가 없어서** 다.

좋은 소식은, **너의 시스템은 이미 모범 사례(presigned 직접 전송, keyset 순회, 체크포인트 재개)를 일부 갖췄고**, 최근 OOM 수정 커밋 자체가 훌륭한 교재라는 점이다. 남은 일은 그 원칙들을 **모든 경로에 일관되게 일반화**하는 것이다.

> *"데이터를 옮기지 말고, 데이터의 주소를 옮겨라. 전부 모으지 말고, 흘려보내라. 한 번에 하나씩 부르지 말고, 묶어서 불러라."* — 이 세 문장이 위 7개 통증의 9할을 막는다.

---

*본 진단은 `synapse-backend`(main `808871f6`) · `synapse-sdk`(main) · `synapse-agent`(main) 코드를 직접 읽고 작성했다. 각 주장은 해당 파일·커밋으로 검증되었으며, 추정에는 그렇다고 표시했다.*
