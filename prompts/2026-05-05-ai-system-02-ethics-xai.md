# 이미지 생성 프롬프트 — 2026-05-05-ai-system-02-ethics-xai

> 원본 포스트: [`_posts/2026-05-05-ai-system-02-ethics-xai.md`](../_posts/2026-05-05-ai-system-02-ethics-xai.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `ai_ethics_hero.png`)

> 📁 저장 경로: `/assets/images/ai-system/ai_ethics_hero.png`

```
Minimalist isometric illustration of AI governance: on the left a
4-tier pyramid representing the EU AI Act risk levels (red top for
"금지", orange for "고위험", blue for "제한적", green base for "최소"),
in the middle three side-by-side panels showing a fairness scale
balancing two groups, an XAI magnifying glass over a model, and a
shield blocking adversarial noise, on the right a stack of legal
document icons labeled "데이터 3법", "금융 AI 가이드라인", "AI 기본법".
Soft pastel palette (sky blue, warm beige, charcoal accents). Clean
white background. Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 EU, AI, XAI는 영문 그대로 유지 가능).
라벨: 왼쪽 영역 "EU AI Act 위험 4등급",
가운데 영역 "공정성 · XAI · 적대적 방어",
오른쪽 영역 "국내 규제 — 데이터 3법 · AI 기본법",
하단 가운데 "AI 윤리 · 규제 · 설명가능성".
```

### A2. EU AI Act 위험 4등급 피라미드 (`eu_ai_act_4_tiers.png`)

> 📁 저장 경로: `/assets/images/ai-system/eu_ai_act_4_tiers.png`

```
Pyramid diagram showing EU AI Act 4 risk tiers, top to bottom:
Tier 1 (red, smallest): "금지 (Unacceptable)" with examples
"사회적 점수 / 잠재의식 조작 / 실시간 생체인식".
Tier 2 (orange): "고위험 (High Risk)" with examples
"신용평가 · 채용 · 의료 · 법 집행".
Tier 3 (light blue): "제한적 위험 (Limited Risk)" with examples
"챗봇 · 딥페이크 · 감정인식".
Tier 4 (green, largest base): "최소 위험 (Minimal Risk)" with examples
"스팸필터 · 게임 AI · 추천 시스템".
On the right side, a vertical scale shows obligations from "전면 금지"
at the top, "8대 의무" for high-risk, "투명성 의무" for limited, and
"자율" at the bottom. Modern infographic style, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 EU, AI Act는 영문 그대로 유지 가능).
라벨:
- 상단 제목: "EU AI Act — 위험 4등급"
- 등급 라벨: "① 금지 / ② 고위험 / ③ 제한적 / ④ 최소"
- 우측 의무 라벨: "전면 금지", "8대 의무 (위험관리·문서화·인간감독 등)",
  "AI 사용 고지", "자율 (행동강령 권장)"
- 강조 박스: "신용평가·채용 = 고위험"
- 하단 부제: "위반 시 최대 매출 7% 또는 3,500만 유로 과징금"
```

### A3. 공정성 3정의 + 불가능성 (`fairness_three_definitions.png`)

> 📁 저장 경로: `/assets/images/ai-system/fairness_three_definitions.png`

```
Triangle diagram showing the impossibility theorem of fairness. Three
vertices each represent one fairness definition:
- Top: "통계적 동등성 (Demographic Parity)" with formula
  "P(Ŷ=1|A=0) = P(Ŷ=1|A=1)"
- Bottom-left: "동등 기회 (Equal Opportunity)" with formula
  "TPR_A=0 = TPR_A=1"
- Bottom-right: "동등 정확성 (Equalized Odds)" with formula
  "TPR + FPR 모두 동일"
A red dashed circle in the middle of the triangle is labeled
"세 정의 동시 만족 ✗ (기저율 다르면)" with a warning icon.
Below the triangle, two small bar charts show "집단 A 부도율 5%" vs
"집단 B 부도율 10%" to illustrate base rate difference. Modern
infographic style, soft pastel palette, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수식 P(Ŷ=1|A), TPR, FPR, A=0, A=1은 그대로 유지).
라벨:
- 삼각형 꼭짓점: "통계적 동등성", "동등 기회", "동등 정확성"
- 중앙 경고: "세 정의 동시 만족 불가능 (Kleinberg 2016)"
- 하단 막대: "집단별 기저율 차이 — 부도율 5% vs 10%"
- 하단 가운데: "어느 정의를 우선할지는 사회적 합의"
```

### A4. XAI 3대장 비교 (`xai_three_methods.png`)

> 📁 저장 경로: `/assets/images/ai-system/xai_three_methods.png`

```
Three-panel comparison illustration of XAI methods.
Panel 1 "LIME": shows a single prediction point with surrounding sample
points (perturbations), then a small linear approximation line passing
through them locally. Caption: "지역 선형 근사 / 모델 무관".
Panel 2 "SHAP": shows a waterfall chart breaking down a single
prediction into contributions from each feature, with positive
contributions in green and negative in red, summing to the final
prediction. Caption: "Shapley value / 게임이론 4공리".
Panel 3 "Grad-CAM": shows an example medical X-ray image with a
heatmap overlay highlighting which regions the CNN focused on for its
diagnosis. Caption: "CNN 시각화 / 마지막 conv 그래디언트".
Below all three panels, a comparison table shows each method's
applicability (model type, data type, computational cost). Modern
infographic style, three accent colors per panel, white background.
16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 도구명 LIME, SHAP, Grad-CAM, CNN, 표기 Shapley value는 영문 그대로
유지).
라벨:
- 패널 1 제목: "LIME (2016)"
- 패널 1 부 라벨: "지역 선형 근사 — 모델 무관"
- 패널 2 제목: "SHAP (2017)"
- 패널 2 부 라벨: "Shapley value — 4공리 만족"
- 패널 3 제목: "Grad-CAM (2017)"
- 패널 3 부 라벨: "CNN 활성화 시각화"
- 비교표 헤더: "방법 / 모델 종류 / 데이터 / 비용"
- 하단 가운데: "XAI 3대장 — 어느 도구를 언제 쓸까"
```

### A5. 적대적 공격 — 판다→긴팔원숭이 (`adversarial_panda.png`)

> 📁 저장 경로: `/assets/images/ai-system/adversarial_panda.png`

```
Recreation of the classic Goodfellow 2014 FGSM example. Three images
arranged horizontally:
Left: a clean panda photo with a label "원본: 판다 (99%)" beneath.
Middle: a noise pattern image (looks like static) with label
"+ 적대적 잡음 ε · sign(∇L) (사람 눈에 안 보임)".
Right: the resulting adversarial image (looks identical to the panda
to human eyes) with label "결과: 긴팔원숭이 (99%)" in red.
A "+" symbol between left and middle, "=" between middle and right.
Below all three, the FGSM formula is shown:
"x' = x + ε · sign(∇ₓ L(θ, x, y))"
A small annotation reads: "사람 눈에는 같지만 모델은 완전히 다른 답".
Modern educational style, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 FGSM, 수식 x, x', ε, sign, ∇ₓ L, θ, y는 그대로 유지).
라벨:
- 좌측: "원본 — 판다 (99% 신뢰)"
- 가운데: "적대적 잡음 (사람 눈에 안 보임)"
- 우측: "적대적 예제 — 긴팔원숭이 (99% 신뢰)"
- 하단 식: "FGSM: x' = x + ε · sign(∇ₓ L)"
- 강조 캡션: "두 이미지가 사람 눈엔 동일"
- 하단 가운데: "Goodfellow 2014 — 적대적 공격의 출발점"
```

### A6. 편향 5종 파이프라인 (`bias_5_types_pipeline.png`)

> 📁 저장 경로: `/assets/images/ai-system/bias_5_types_pipeline.png`

```
Linear pipeline diagram showing where 5 types of bias enter the ML
process. From left to right:
Stage 1 "데이터 수집" — labeled with "역사적 편향" (icon: clock with
biased records) and "대표성 편향" (icon: skewed pie chart showing
imbalanced groups).
Stage 2 "라벨링" — labeled with "측정 편향" (icon: warped measuring
tape suggesting proxy variable distortion).
Stage 3 "학습" — labeled with "집계 편향" (icon: average line missing
subgroup peaks).
Stage 4 "평가" — labeled with "평가 편향" (icon: biased benchmark with
warning).
Each bias type connected to its stage with a downward arrow into the
horizontal pipeline. At the end, a "편향된 출력" box. Below the
diagram, a small annotation lists mitigation techniques per stage:
"사전 처리 / 학습 중 / 사후 처리". Modern infographic style, white
background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지.
라벨:
- 단계: "데이터 수집 → 라벨링 → 학습 → 평가 → 출력"
- 편향 5종:
  ① "역사적 편향 (Historical Bias)"
  ② "대표성 편향 (Representation Bias)"
  ③ "측정 편향 (Measurement Bias)"
  ④ "집계 편향 (Aggregation Bias)"
  ⑤ "평가 편향 (Evaluation Bias)"
- 완화 기법 막대: "사전 처리 (재가중·재표집) / 학습 중 (공정성 제약) / 사후 처리 (임계값 조정)"
- 하단 가운데: "편향은 파이프라인 어디서나 들어온다"
```

### A7. 화이트박스 vs 블랙박스 트레이드오프 (`whitebox_vs_blackbox.png`)

> 📁 저장 경로: `/assets/images/ai-system/whitebox_vs_blackbox.png`

```
Two-axis scatter plot illustrating the interpretability-performance
tradeoff. X-axis: "성능 (Performance)" from low to high. Y-axis:
"해석 가능성 (Interpretability)" from low to high. Several model
families plotted as labeled points:
- Top-left (high interpretability, lower performance): 선형 회귀,
  로지스틱 회귀, 얕은 결정트리
- Bottom-right (low interpretability, high performance): 딥러닝,
  앙상블 (XGBoost), 트랜스포머
- Middle: 랜덤 포레스트, 얕은 신경망
A diagonal arrow with a "?" labeled "이상적 위치 — 둘 다 높음" but
empty (suggesting the gap). On the right side of the plot, an arrow
pointing to the bottom-right region with label "+ XAI (LIME · SHAP)
→ 사후 설명으로 보강". Modern infographic style, soft palette, white
background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 모델 이름 XGBoost, LIME, SHAP, Transformer는 영문 그대로 유지).
라벨:
- 축: "성능 (낮음 → 높음)", "해석 가능성 (낮음 → 높음)"
- 모델 점:
  좌상단: "선형 회귀", "로지스틱 회귀", "얕은 결정트리"
  중앙: "랜덤 포레스트", "얕은 신경망"
  우하단: "딥러닝", "XGBoost", "Transformer"
- 화살표 라벨: "이상적 위치 (둘 다 높음) — 빈 공간"
- 보강 화살표: "+ XAI 적용 → 사후 설명"
- 하단 가운데: "해석 가능성 vs 성능 — 절충 또는 XAI 보강"
```

### A8. 국내 규제 타임라인 (`korea_ai_regulation_timeline.png`)

> 📁 저장 경로: `/assets/images/ai-system/korea_ai_regulation_timeline.png`

```
Horizontal timeline diagram showing key Korean AI/data regulations from
2020 to 2026. Five major events marked on the line:
2020: "데이터 3법 시행 (개보법·정통망법·신정법)"
2020: "마이데이터 (신용정보법 개정)"
2021: "신뢰할 수 있는 AI 가이드라인 (과기정통부)"
2021: "금융분야 AI 활용 가이드라인 (금융위·금감원)"
2024: "AI 기본법 국회 통과"
2026: "AI 기본법 시행"
Each event shown as a marker on the timeline with a brief description.
Above the timeline, a parallel bar shows EU AI Act milestones for
comparison: "2024 발효", "2026 시행". Modern infographic style, two
color bands (Korea and EU), white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 EU, AI, GDPR은 영문 그대로 유지).
라벨:
- 상단 제목: "한국 AI · 데이터 규제 타임라인 (2020~2026)"
- 한국 트랙 라벨: "한국"
- EU 비교 트랙: "EU 비교 (AI Act)"
- 이벤트:
  2020: "데이터 3법 + 마이데이터"
  2021: "AI 가이드라인 2종"
  2024: "AI 기본법 통과"
  2026: "AI 기본법 시행"
- 강조 박스: "KODIT 등 금융 AI = 금융위 가이드라인 + AI 기본법 동시 적용"
- 하단 가운데: "국내 규제는 *가이드라인 → 법률* 로 강화 중"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
