# 이미지 생성 프롬프트 — 2026-05-01-ai-advanced-activation-derivatives

> 원본 포스트: [`_posts/2026-05-01-ai-advanced-activation-derivatives.md`](../_posts/2026-05-01-ai-advanced-activation-derivatives.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `activation_derivatives_hero.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/activation_derivatives_hero.png`

```
Minimalist isometric illustration of activation function derivatives:
on the left a coordinate plane showing both σ(z) and σ'(z) curves
overlaid (with the derivative peaking at 0.25 marked), in the middle a
chain rule formula visualization with three boxes (∂L/∂a × ∂a/∂z ×
∂z/∂w), on the right a deep network with gradient flow arrows
diminishing through layers (representing vanishing gradient). Soft
pastel palette (sky blue, warm beige, charcoal accents). Clean white
background. Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 σ, σ'(z), z, ∂L/∂a, ∂a/∂z, ∂z/∂w, 0.25는 그대로 유지).
라벨: 좌측 영역 "활성화 함수 + 그 미분", 가운데 영역 "역전파 연쇄법칙",
우측 영역 "기울기 소멸", 하단 가운데 "활성화 함수 미분 완전 정리".
```

### A2. 미분 곡선 6종 비교 (`activation_derivative_curves.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/activation_derivative_curves.png`

```
Six-panel grid showing each activation function paired with its
derivative on the same coordinate plane (function as solid line,
derivative as dashed line):
- Sigmoid (S-shape) + σ' (bell peaking at 0.25)
- Tanh + tanh' (bell peaking at 1)
- ReLU (hockey stick) + ReLU' (step at z=0)
- Leaky ReLU + Leaky ReLU' (two-level step)
- Softplus + Softplus' (= sigmoid curve)
- Softmax (single output curve, with note "Jacobian when vector")
Each panel labeled with formula and max derivative value. Clean
educational style, light grid lines, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호와 함수명 σ, σ', tanh, tanh', ReLU, Leaky ReLU, Softplus,
Softmax는 그대로 유지).
라벨:
- 패널 1 제목: "시그모이드 — 미분 최댓값 0.25"
- 패널 2 제목: "Tanh — 미분 최댓값 1"
- 패널 3 제목: "ReLU — 미분 양수 1, 음수 0"
- 패널 4 제목: "Leaky ReLU — 음수 영역 α"
- 패널 5 제목: "Softplus — 미분 = 시그모이드"
- 패널 6 제목: "Softmax — Jacobian 행렬"
- 각 패널 범례: "원함수 (실선)", "미분 (점선)"
- 하단 가운데: "활성화 함수 6종과 그 미분"
```

### A3. 시그모이드 미분 유도 (`sigmoid_derivative_proof.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/sigmoid_derivative_proof.png`

```
Step-by-step mathematical derivation illustration showing the chain of
manipulations from σ(z) = 1/(1+e^(-z)) to σ'(z) = σ(1-σ). Each step
shown as a labeled rectangular box with the equation, connected by
downward arrows. Final box highlighted in green showing σ'_max = 0.25
at z=0, σ=0.5. To the right of the derivation, a small visualization
of the function f(σ) = σ(1-σ) as an upside-down parabola peaking at
σ=0.5, value 0.25. Clean infographic style, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수식 σ, σ(z), σ', e^(-z), 0.25, 0.5는 그대로 유지).
라벨:
- 단계 박스: "Step 1: u = 1 + e^(-z), σ = 1/u",
  "Step 2: dσ/dz = e^(-z)/(1+e^(-z))²",
  "Step 3: 분리 → σ × [1 − σ]",
  "Step 4: σ'(z) = σ(z)·(1−σ(z))"
- 우측 그래프 라벨: "f(σ) = σ(1−σ)", "최댓값 0.25 (σ=0.5)"
- 하단 가운데: "시그모이드 미분 유도 — 0.25의 비밀"
```

### A4. 기울기 소멸 시각화 (`vanishing_gradient_layers.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/vanishing_gradient_layers.png`

```
Two-row comparison illustration of gradient propagation through deep
networks. Top row "Sigmoid Network": 8 stacked layer boxes from right
(output) to left (input), with arrows showing gradient values
multiplied by 0.25 at each step (0.25 → 0.0625 → 0.0039 → ... →
0.000015), arrow thickness shrinking dramatically. Bottom row "ReLU
Network": same 8 layers, but gradient stays approximately 1 (constant
arrow thickness). Each row labeled with its activation. Below, a bar
chart shows final gradient magnitudes side by side. Modern infographic
style, two contrasting colors. White background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 활성화 함수명 Sigmoid, ReLU와 수치 0.25, 0.0625, 1, 0.000015는 그대로 유지).
라벨:
- 상단 행 제목: "시그모이드 네트워크 — 8층"
- 상단 행 화살표 라벨: "× 0.25 (시그모이드 미분 최대)"
- 하단 행 제목: "ReLU 네트워크 — 8층"
- 하단 행 화살표 라벨: "× 1 (양수 영역)"
- 좌측 라벨: "입력층"
- 우측 라벨: "출력층"
- 하단 막대 그래프: "최종 그래디언트 크기"
- 하단 가운데: "기울기 소멸 — 시그모이드 0.25^n vs ReLU 1^n"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
