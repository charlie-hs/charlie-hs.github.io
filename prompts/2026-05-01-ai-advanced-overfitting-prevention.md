# 이미지 생성 프롬프트 — 2026-05-01-ai-advanced-overfitting-prevention

> 원본 포스트: [`_posts/2026-05-01-ai-advanced-overfitting-prevention.md`](../_posts/2026-05-01-ai-advanced-overfitting-prevention.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `overfitting_prevention_hero.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/overfitting_prevention_hero.png`

```
Minimalist isometric illustration of overfitting prevention techniques:
on the far left a learning curve graph showing training error decreasing
while validation error eventually rises (overfitting point marked with
a red arrow), in the middle four small icons representing dropout
(crossed-out neurons), batch normalization (data distribution being
flattened), data augmentation (image being rotated/flipped), and
regularization (a balance scale weighing weights). On the right an
"early stopping" pause icon. Soft pastel palette (sky blue, warm beige,
charcoal accents). Clean white background. Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 L1, L2는 영문 그대로 유지 가능).
라벨: 좌측 영역 "과적합 — 검증 오차 증가",
가운데 영역 "드롭아웃 / 배치 정규화 / 데이터 증강 / 규제화 (L1·L2)",
우측 영역 "Early Stopping",
하단 가운데 "과적합 완화 — 4대 기법 완전 정리".
```

### A2. 24-11번 함정 분석 (`batchnorm_trap.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/batchnorm_trap.png`

```
Two-column comparison illustration analyzing 24-11 question option ②.
Left column "거짓 진술": shows a weight matrix with arrows pointing
at it labeled "가중치를 정규화", and a histogram with arrows indicating
"표준편차 증대".
Right column "진실": shows an activation output (node value) with
arrows pointing at it labeled "노드값(활성화 출력)을 정규화", and a
histogram with the spread compressed and labeled "표준편차 = 1로 일정".
Below, a red X mark with the false statement and a green check with
the true statement. Modern infographic style, two contrasting colors
(red for false, green for true). White background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 γ, β, μ, σ는 그대로 유지).
라벨:
- 좌측 컬럼 제목: "거짓 — 24-11번 ②번 보기"
- 좌측 항목 1: "가중치 W를 정규화"
- 좌측 항목 2: "표준편차 증대"
- 우측 컬럼 제목: "진실"
- 우측 항목 1: "노드값(활성화 출력) x를 정규화"
- 우측 항목 2: "표준편차 = 1로 일정"
- 하단 가운데: "배치 정규화 함정 분석 — 24-11번 직출"
```

### A3. L1 vs L2 시각적 비교 (`l1_vs_l2_regularization.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/l1_vs_l2_regularization.png`

```
Two-panel comparison illustration of L1 vs L2 regularization.
Left panel "L1 (Lasso)": a 2D contour plot of a loss function with
diamond-shaped (square rotated 45°) contour of the L1 constraint
overlaid. The optimal point is at a corner (where one weight = 0).
Below, a bar chart showing many weights with several being exactly
zero (sparse).
Right panel "L2 (Ridge)": same loss contour with a circular L2
constraint overlaid. The optimal point is in a smooth interior
position. Below, a bar chart showing all weights with reduced
magnitudes but none exactly zero.
Modern infographic style, distinct colors for each panel. White
background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 L1, L2, w₁, w₂, λ, Σ|w|, Σw²는 그대로 유지).
라벨:
- 좌측 패널 제목: "L1 정규화 (Lasso) — 희소성 유도"
- 좌측 패널 식: "λ Σ|wᵢ|"
- 좌측 부 라벨: "일부 가중치 = 0"
- 우측 패널 제목: "L2 정규화 (Ridge) — 가중치 축소"
- 우측 패널 식: "λ Σwᵢ²"
- 우측 부 라벨: "모든 가중치 작아짐"
- 하단 막대 그래프 라벨: "가중치 값 분포"
- 하단 가운데: "L1 vs L2 정규화 — 희소성 vs 축소"
```

### A4. 4대 기법 비교 (`overfitting_4_methods_compare.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/overfitting_4_methods_compare.png`

```
Four-quadrant infographic comparing the four overfitting prevention
methods. Each quadrant has an icon and a brief description:
- Top-left: Dropout — neural network with grayed-out nodes
- Top-right: Batch Normalization — data distribution being flattened
  to a normal curve
- Bottom-left: Data Augmentation — original image plus rotated/flipped/
  color-shifted variants
- Bottom-right: Regularization — loss curve with a penalty term added,
  showing L1 (diamond) and L2 (circle) constraint shapes
Clean infographic style, consistent color palette, white background.
16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 L1, L2와 수학 기호 γ, β, λ는 영문 그대로 유지).
라벨:
- 좌상단 사분면: "① 드롭아웃 (Dropout) — rate=0.5"
- 우상단 사분면: "② 배치 정규화 — 노드값 평균 0, 분산 1"
- 좌하단 사분면: "③ 데이터 증강 — 회전·반전·Mixup"
- 우하단 사분면: "④ 규제화 (L1·L2) — 가중치 크기 제한"
- 가운데 부제: "과적합 완화 4대 기법"
- 하단 가운데: "각 기법은 상호 보완적 — 함께 사용 가능"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
