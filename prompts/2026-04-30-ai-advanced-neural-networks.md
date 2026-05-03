# 이미지 생성 프롬프트 — 2026-04-30-ai-advanced-neural-networks

> 원본 포스트: [`_posts/2026-04-30-ai-advanced-neural-networks.md`](../_posts/2026-04-30-ai-advanced-neural-networks.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `neural_networks_hero.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/neural_networks_hero.png`

```
Minimalist isometric illustration of neural network mathematics:
on the left a stylized perceptron node with weighted inputs converging
into a sigmoid curve, in the middle a multi-layer network with arrows
showing forward propagation and backward gradient flow, on the right a
loss function bowl with a small ball rolling down toward the minimum
(gradient descent). Soft pastel palette (sky blue, warm beige, charcoal
accents). Clean white background. Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 σ, w, ∂L/∂w 등은 그대로 유지).
라벨: 왼쪽 영역 "퍼셉트론", 가운데 "다층 신경망 (역전파)", 오른쪽 "경사하강법",
하단 가운데 "신경망 핵심 수식".
```

### A2. 활성화 함수 4종 비교 (`activation_functions.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/activation_functions.png`

```
Four-panel grid comparison of activation functions, each panel showing
the function curve on a coordinate plane.
Top-left: sigmoid curve in (0,1) range with smooth S-shape.
Top-right: tanh curve in (-1,1) range, steeper S-shape, centered at 0.
Bottom-left: ReLU function — zero for negative input, linear for positive.
Bottom-right: Leaky ReLU — slight slope in negative region.
Each panel labeled with formula. Clean educational style, white background,
light grid lines. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수식 σ(z), Tanh, ReLU, Leaky ReLU, max, e, z는 그대로 유지).
패널 제목:
- 좌상단: "시그모이드 σ(z) = 1/(1+e^-z), (0,1)"
- 우상단: "Tanh, (-1,1)"
- 좌하단: "ReLU = max(0, z), [0,∞)"
- 우하단: "Leaky ReLU = max(αz, z)"
가운데 부제: "활성화 함수 4종 비교"
```

### A3. 역전파 흐름 (`backpropagation_flow.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/backpropagation_flow.png`

```
Clean technical illustration of a 3-layer neural network (input, hidden,
output) with two-color arrows showing data flow. Forward arrows in blue
go left to right. Backward arrows in red go right to left, representing
gradient flow. Small annotations near the output show the loss function.
Each connection has a weight label. Modern infographic style, white
background, two accent colors. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 w, ∂L/∂w, η는 그대로 유지).
라벨:
- 입력층: "입력층"
- 은닉층: "은닉층"
- 출력층: "출력층"
- 파란 화살표: "순전파 (Forward Pass)"
- 빨간 화살표: "역전파 (Backward Pass) — 그래디언트"
- 손실 함수 박스: "손실 함수 L"
- 가중치 갱신: "w ← w - η · ∂L/∂w"
```

### A4. 과적합 완화 4대 기법 (`overfitting_prevention.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/overfitting_prevention.png`

```
Four-panel comparison illustration of overfitting prevention techniques.
Top-left: dropout — neural network with some nodes grayed out and connections
cut.
Top-right: batch normalization — a normal distribution curve aligning input
distributions across layers.
Bottom-left: data augmentation — original image transformed into rotated,
flipped, color-shifted versions.
Bottom-right: regularization — loss curve with a penalty term added on top.
Clean infographic style, consistent color palette, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 L1, L2, BatchNorm, λ는 영문 그대로 유지 가능).
패널 제목:
- 좌상단: "드롭아웃 (Dropout) — rate=0.5"
- 우상단: "배치 정규화 (Batch Normalization)"
- 좌하단: "데이터 증강 (Data Augmentation)"
- 우하단: "규제화 (Regularization, L1·L2)"
가운데 부제: "과적합 완화 4대 기법"
```

### A5. 1-1-1 신경망 순전파·역전파 통합 trace (`forward_backward_trace.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/forward_backward_trace.png`

```
Step-by-step educational illustration of a tiny 1-input, 1-hidden,
1-output neural network with sigmoid activations, showing both forward
and backward passes side by side. Top half: forward pass — input value
x=1.0 enters, multiplied by weight w1=0.5, passed through sigmoid to
produce hidden value h, multiplied by weight w2=0.5, passed through
sigmoid to produce output y, compared against target t=0.0 to compute
loss L. Each intermediate value is annotated. Bottom half: backward pass
— gradients ∂L/∂y, ∂L/∂w2, ∂L/∂h, ∂L/∂w1 flow right to left, with
chain-rule equations shown next to each arrow. Modern infographic style,
two accent colors (forward = blue, backward = red), white background.
16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 σ, w1, w2, x, h, y, t, L, ∂L/∂w, η는 그대로 유지).
라벨:
- 상단 제목: "순전파 (Forward Pass)"
- 입력 노드: "입력 x = 1.0"
- 은닉 노드: "은닉 h = σ(w1·x)"
- 출력 노드: "출력 y = σ(w2·h)"
- 손실 박스: "손실 L = ½(y - t)²"
- 하단 제목: "역전파 (Backward Pass) — 연쇄 법칙"
- 그래디언트 라벨: "∂L/∂y", "∂L/∂w2 = ∂L/∂y · ∂y/∂w2",
  "∂L/∂h = ∂L/∂y · ∂y/∂h", "∂L/∂w1 = ∂L/∂h · ∂h/∂w1"
- 갱신 식: "w ← w - η · ∂L/∂w"
- 하단 가운데: "1-1-1 신경망 — 한 번의 전·후방 흐름"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
