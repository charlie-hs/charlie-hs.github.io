# 이미지 생성 프롬프트 — 2026-05-04-ai-system-01-mlops

> 원본 포스트: [`_posts/2026-05-04-ai-system-01-mlops.md`](../_posts/2026-05-04-ai-system-01-mlops.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `mlops_hero.png`)

> 📁 저장 경로: `/assets/images/ai-system/mlops_hero.png`

```
Minimalist isometric illustration of an end-to-end MLOps pipeline:
on the left a stack of icons representing Code, Data, and Model boxes
connected by version arrows (3-axis versioning), in the middle a cyclic
flow with seven labeled stages (collect → preprocess → train → validate
→ deploy → monitor → retrain) drawn as a closed loop with arrows, on
the right a monitoring dashboard with charts showing accuracy, latency,
and a drift alert. Soft pastel palette (sky blue, warm beige, charcoal
accents). Clean white background. Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 MLOps, CI/CD/CT/CM, 변수 P(X)는 영문 그대로 유지 가능).
라벨: 왼쪽 영역 "3축 버전 관리 — 코드·데이터·모델",
가운데 영역 "ML 라이프사이클 7단계",
오른쪽 영역 "모니터링 + 드리프트 감지",
하단 가운데 "MLOps — 모델을 *계속* 잘 돌게 하는 인프라".
```

### A2. Hidden Technical Debt 다이어그램 (`hidden_tech_debt.png`)

> 📁 저장 경로: `/assets/images/ai-system/hidden_tech_debt.png`

```
Recreation of the famous Sculley 2015 NeurIPS diagram showing how ML
code is a tiny fraction of an ML system. A large rectangular canvas is
divided into many surrounding boxes (data collection, feature
extraction, configuration, monitoring, serving infrastructure, process
management, analysis tools, machine resource management, etc.) with one
small central box labeled "ML 코드" highlighted in red. The visual
emphasizes the size disparity. Modern infographic style, light
background, the central red box clearly stands out. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 인용 출처 "Sculley 2015 NeurIPS"는 영문 그대로 유지).
라벨:
- 중앙 빨간 박스: "ML 코드"
- 주변 박스들: "설정 관리", "데이터 수집", "피처 추출", "데이터 검증",
  "서빙 인프라", "모니터링", "프로세스 관리", "분석 도구", "기계 자원 관리",
  "자동화 시스템"
- 상단 제목: "ML 시스템에서 ML 코드가 차지하는 비중"
- 하단 출처: "출처: Sculley et al. 2015 NeurIPS"
```

### A3. ML 라이프사이클 7단계 (`ml_lifecycle_7steps.png`)

> 📁 저장 경로: `/assets/images/ai-system/ml_lifecycle_7steps.png`

```
Circular flow diagram showing the 7 stages of an ML lifecycle. Stages
arranged in a clockwise circle: ① 데이터 수집, ② 전처리·피처 엔지니어링,
③ 학습, ④ 검증·평가, ⑤ 배포, ⑥ 모니터링, ⑦ 재학습. Each stage shown
as an icon-labeled node. Arrow from ④ has a dashed back-arrow to ③ for
"성능 부족 시". Arrow from ⑥ has a dashed arrow to ⑦ for "드리프트 감지".
Arrow from ⑦ loops back to ③. The cyclic nature is emphasized. Modern
infographic style, soft pastel palette, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 ML, A/B는 영문 그대로 유지 가능).
라벨:
- 7단계: "① 데이터 수집", "② 전처리·피처 엔지니어링", "③ 학습",
  "④ 검증·평가", "⑤ 배포", "⑥ 모니터링", "⑦ 재학습"
- 점선 루프 라벨: "성능 부족 → 재학습", "드리프트 감지 → 재학습"
- 가운데 부제: "지속적 학습 (CT) + 지속적 모니터링 (CM)"
- 하단 가운데: "ML 라이프사이클은 *순환* 한다"
```

### A4. 피처 스토어 구조 (`feature_store_architecture.png`)

> 📁 저장 경로: `/assets/images/ai-system/feature_store_architecture.png`

```
Architectural diagram of a feature store. On the left, three data sources
(database icon, Kafka stream icon, S3/data lake icon) arrow into a
central "피처 스토어" hexagonal block. From the central block, two
arrows split: one downward to "오프라인 저장소 (학습용)" depicted as a
data warehouse icon, leading to a "모델 학습" cluster icon; the other
to "온라인 저장소 (추론용)" depicted as a Redis-like fast key-value
store icon, leading to a "모델 추론" microservice icon. A small
annotation box highlights "학습-추론 일관성 (Training-Serving Skew
방지)". Modern infographic style, two accent colors per path (blue for
offline, orange for online), white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 도구 이름 Kafka, Redis, S3는 영문 그대로 유지 가능).
라벨:
- 좌측 데이터 소스: "DB", "Kafka 스트림", "데이터레이크 (S3)"
- 중앙 핵심 블록: "피처 스토어 (Feature Store)"
- 하단 분기 1: "오프라인 저장소 — 학습용"
- 하단 분기 2: "온라인 저장소 — 추론용 (저지연)"
- 도착점: "모델 학습", "모델 추론"
- 강조 박스: "학습-추론 일관성 보장"
- 하단 가운데: "한 정의식 → 두 환경 동일 결과"
```

### A5. 배치 vs 실시간 추론 비교 (`batch_vs_realtime_inference.png`)

> 📁 저장 경로: `/assets/images/ai-system/batch_vs_realtime_inference.png`

```
Two-panel comparison illustration of inference patterns.
Left panel "배치 추론": a clock icon showing daily/hourly schedule, a
large data warehouse icon, an Airflow DAG-like flow with multiple boxes
processed at once, output written to a results table. Subtitle: "대량
일괄 처리, 시간·일 단위".
Right panel "실시간 추론": a fast-response icon, a single user request
arrow, a microservice/API icon labeled "추론 서버 (KServe·Triton·vLLM)",
and a fast response arrow back to the user. Subtitle: "ms 단위 응답,
요청마다 처리".
Below both panels, a small "하이브리드 (Lambda Architecture)" box shows
both layers feeding a serving layer. Modern infographic style, two
accent colors, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 도구 이름 Airflow, Spark, KServe, Triton, vLLM은 영문 그대로 유지).
라벨:
- 좌측 패널 제목: "배치 추론 (Batch)"
- 좌측 부 라벨: "대량 일괄 처리, 시간·일 단위"
- 좌측 도구: "Spark, Airflow"
- 우측 패널 제목: "실시간 추론 (Real-time)"
- 우측 부 라벨: "ms 단위 응답, 요청마다 즉시"
- 우측 도구: "KServe, Triton, vLLM"
- 하단 박스: "하이브리드 (Lambda Architecture) — 두 레이어 결합"
- 하단 가운데: "추론 패턴 — 응답 시간으로 결정"
```

### A6. 드리프트 3종 비교 (`drift_3_types.png`)

> 📁 저장 경로: `/assets/images/ai-system/drift_3_types.png`

```
Three-panel illustration showing the three types of drift in production
ML.
Panel 1 "데이터 드리프트": two overlapping but shifted bell curves
representing P(X) at training time vs serving time, with arrows
indicating distribution shift. Caption: "입력 분포 P(X) 변화".
Panel 2 "개념 드리프트": same input but with different output mappings
shown as two different decision boundaries (curves) on the same scatter
plot. Caption: "P(Y|X) 변화 — 모델이 직접 망가짐".
Panel 3 "레이블 드리프트": two pie charts showing class proportions, one
with 99/1 split and another with 95/5, indicating P(Y) change. Caption:
"출력 분포 P(Y) 변화".
Below all three panels, a horizontal scale shows PSI (Population
Stability Index) thresholds: < 0.1 안정 / 0.1-0.25 약한 변화 / > 0.25
재학습 필요. Modern infographic style, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수식 P(X), P(Y|X), P(Y), 표준 약어 PSI는 영문 그대로 유지).
라벨:
- 패널 1 제목: "데이터 드리프트 — 입력 분포 변화"
- 패널 2 제목: "개념 드리프트 — 입력↔출력 관계 변화"
- 패널 3 제목: "레이블 드리프트 — 출력 분포 변화"
- 패널 1 캡션: "P(X) 변함 / 모델은 그대로 정확할 수 있음"
- 패널 2 캡션: "P(Y|X) 변함 / 모델 직접 망가짐 → 재학습 필수"
- 패널 3 캡션: "P(Y) 변함 / 클래스 비율 이동"
- 하단 PSI 막대: "안정 < 0.1 / 약한 변화 0.1~0.25 / 재학습 필요 > 0.25"
- 하단 가운데: "드리프트 3종 — 무엇을 감시할 것인가"
```

### A7. 분산 학습 — 데이터 vs 모델 병렬 (`distributed_training_parallels.png`)

> 📁 저장 경로: `/assets/images/ai-system/distributed_training_parallels.png`

```
Two-panel comparison illustration of distributed training paradigms.
Left panel "데이터 병렬 (Data Parallel)": three identical model replicas
shown on three GPU icons, each receiving a different mini-batch. Arrows
labeled "그래디언트" converge on a central "All-Reduce" operation,
which then sends updated weights back to each GPU. Subtitle: "모델 복사
+ 데이터 분할 / 표준 패턴".
Right panel "모델 병렬 (Model Parallel)": a large model split into three
horizontal slices (Layer 1-3, Layer 4-6, Layer 7-9) each placed on a
different GPU icon, with sequential arrows showing forward pass through
the pipeline, then backward pass returning. Subtitle: "모델 분할 / LLM
필수".
Below, a small badge showing "FSDP / ZeRO — 하이브리드 (옵티마이저까지
분할)". Modern infographic style, two accent colors per panel, white
background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 GPU, FSDP, ZeRO, LLM, All-Reduce는 영문 그대로 유지).
라벨:
- 좌측 패널 제목: "데이터 병렬 (Data Parallel)"
- 좌측 부 라벨: "모델 복사 + 데이터 분할"
- 좌측 도구: "PyTorch DDP, Horovod, Ray Train"
- 우측 패널 제목: "모델 병렬 (Model Parallel)"
- 우측 부 라벨: "모델 분할 — LLM 필수"
- 우측 분류: "텐서 병렬 / 파이프라인 병렬"
- 하단 배지: "FSDP / ZeRO — 옵티마이저 상태까지 분할"
- 하단 가운데: "모델이 GPU 한 장에 들어가면 데이터 병렬, 아니면 모델 병렬"
```

### A8. A/B vs 섀도우 vs 카나리 배포 (`deployment_strategies.png`)

> 📁 저장 경로: `/assets/images/ai-system/deployment_strategies.png`

```
Three-panel comparison illustration of model deployment strategies.
Panel 1 "A/B 테스트": traffic flow split 90/10 between old model (large
arrow) and new model (small arrow), with users seeing actual responses
from both. Statistical comparison icon (p-value) at the bottom.
Panel 2 "섀도우 배포 (Shadow)": 100% traffic flows to old model and
returns to user, while a duplicate of every request also flows to new
model but its output is logged only (not returned). Subtitle: "사용자
영향 0".
Panel 3 "카나리 배포 (Canary)": progressive rollout shown as a timeline
1% → 5% → 25% → 100%, with a checkpoint icon between each stage. Old
model shrinks, new model grows.
Below all three, a comparison table showing risk vs speed vs resource
cost for each strategy. Modern infographic style, three accent colors,
white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 A/B, p-value는 영문 그대로 유지).
라벨:
- 패널 1 제목: "A/B 테스트"
- 패널 1 부 라벨: "트래픽 분할 — 통계 유의성 확인"
- 패널 2 제목: "섀도우 배포 (Shadow)"
- 패널 2 부 라벨: "사용자 영향 0 / 자원 2배"
- 패널 3 제목: "카나리 배포 (Canary)"
- 패널 3 부 라벨: "1% → 5% → 25% → 100% 점진 확대"
- 비교표 헤더: "전략 / 위험 / 속도 / 자원"
- 하단 가운데: "배포 전략 — 위험과 자원의 균형"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
