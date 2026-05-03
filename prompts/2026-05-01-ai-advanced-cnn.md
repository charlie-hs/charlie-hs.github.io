# 이미지 생성 프롬프트 — 2026-05-01-ai-advanced-cnn

> 원본 포스트: [`_posts/2026-05-01-ai-advanced-cnn.md`](../_posts/2026-05-01-ai-advanced-cnn.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `cnn_hero.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/cnn_hero.png`

```
Minimalist isometric illustration of a Convolutional Neural Network:
on the left a colorful input image of a cat, in the middle several
stacked feature map layers shrinking in size with arrows showing the
flow through convolution and pooling operations, on the right a final
classification output with class probability bars. A small filter
kernel slides across one of the feature maps. Soft pastel palette
(sky blue, warm beige, charcoal accents). Clean white background.
Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 CNN, ReLU, Softmax는 영문 그대로 유지 가능).
라벨: 왼쪽 영역 "입력 이미지", 가운데 영역 "합성곱 + 풀링 (특성 추출)",
오른쪽 영역 "분류 출력 (Softmax)", 슬라이딩 필터 옆 "필터 (3×3)",
하단 가운데 "CNN 합성곱 신경망".
```

### A2. 합성곱 연산 시각화 (`convolution_operation.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/convolution_operation.png`

```
Clean technical illustration of a 2D convolution operation:
on the left a 4×4 input matrix with numbers, in the middle a 3×3 filter
matrix overlaid on the top-left 3×3 region of the input (highlighted),
arrows showing element-wise multiplication, on the right a 2×2 output
feature map with the first cell value computed. Below, the formula for
output size is shown. Modern infographic style, two accent colors,
white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수식 (H-F+2P)/S+1, H, W, F, P, S는 그대로 유지).
라벨: 왼쪽 매트릭스 "입력 (4×4)", 가운데 작은 매트릭스 "필터 (3×3)",
오른쪽 매트릭스 "출력 특성 맵 (2×2)", 화살표에 "요소별 곱 → 합산",
하단 공식 박스 "출력 크기: H' = (H − F + 2P) / S + 1".
```

### A3. ResNet 잔차 연결 (`resnet_residual.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/resnet_residual.png`

```
Clean technical illustration of a ResNet residual block:
input x flows through two convolutional layers (Conv → ReLU → Conv) to
produce F(x), then a skip connection (curved arrow) bypasses these
layers and adds x directly to F(x), resulting in F(x) + x. The skip
connection is highlighted in a different color. Comparison shown:
"plain network without skip" struggling with deep layers vs "ResNet
with skip" enabling 152-layer training. Modern infographic style,
two accent colors. White background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수식 H(x) = F(x) + x, ReLU, Conv는 그대로 유지).
라벨: 입력 "입력 x", 두 합성곱 박스 "합성곱 + ReLU",
점프하는 화살표 "잔차 연결 (Skip Connection)",
출력 박스 "출력 H(x) = F(x) + x",
하단 비교 박스 "잔차 연결 → 152층 안정 학습".
```

### A4. 객체 탐지 모델 비교 (`object_detection.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/object_detection.png`

```
Two-panel comparison illustration of object detection paradigms:
Top panel "2-stage" shows: input image → region proposal (multiple
candidate boxes) → CNN classification → final output. R-CNN, Fast R-CNN,
Faster R-CNN labeled below.
Bottom panel "1-stage" shows: input image divided into a grid → single
forward pass produces all boxes and classes simultaneously → final
output. YOLO, SSD labeled below.
A speed-vs-accuracy axis chart on the right comparing all five models
positioning. Clean infographic style, consistent color palette,
white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 모델명 R-CNN, Fast R-CNN, Faster R-CNN, YOLO, SSD는 영문 유지).
라벨:
- 상단 패널 제목: "2-Stage (정확도 우선)"
- 상단 단계 라벨: "입력 → 영역 제안 → 분류 → 출력"
- 상단 모델: "R-CNN (2014), Fast R-CNN (2015), Faster R-CNN (2015)"
- 하단 패널 제목: "1-Stage (속도 우선)"
- 하단 단계 라벨: "입력 → 그리드 분할 → 박스+클래스 동시 예측 → 출력"
- 하단 모델: "YOLO (2016), SSD (2016)"
- 우측 차트 축: "속도", "정확도"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
