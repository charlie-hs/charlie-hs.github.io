# 이미지 생성 프롬프트 — 2026-04-27-ai-introduction-modern-ai

> 원본 포스트: [`_posts/2026-04-27-ai-introduction-modern-ai.md`](../_posts/2026-04-27-ai-introduction-modern-ai.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `modern_ai_hero.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/modern_ai_hero.png`

```
Minimalist isometric illustration of modern AI:
on the left a glowing neural network, in the middle a Transformer block
shown as stacked layers with arrows representing self-attention, on the
right a generated landscape image emerging from noise (representing
diffusion). A faint timeline at the bottom marks key years.
Soft pastel palette (sky blue, warm beige, charcoal accents). Clean white
background. Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 기술명 Transformer, 연도 숫자 2012/2017/2022는 그대로 유지 가능).
라벨: 왼쪽 영역 "신경망", 가운데 영역 "Transformer", 오른쪽 영역 "디퓨전 생성",
타임라인 위 "2012 (AlexNet)", "2017 (Transformer)", "2022 (ChatGPT)".
```

### A2. Transformer 블록 시각화 (`transformer_block.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/transformer_block.png`

```
Clean technical illustration of a single Transformer encoder block:
input tokens with positional embeddings flowing into a multi-head
self-attention module, then add-norm, then feed-forward, then another
add-norm. Each component drawn as a distinct labeled box. Arrows show
data flow. Subtle matrices floating beside the attention block.
Modern infographic style, two accent colors. White background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 Q, K, V, softmax(QKᵀ/√d)V는 그대로 유지).
라벨 (위에서 아래로):
- "입력 토큰 + 위치 임베딩"
- "멀티헤드 셀프 어텐션"
- "Add & LayerNorm" (또는 한글로 "잔차 + 정규화")
- "피드 포워드 (MLP)"
- "Add & LayerNorm"
- "다음 블록으로"
- 어텐션 옆 행렬 라벨: "Q (질의)", "K (키)", "V (값)"
```

### A3. 생성 모델 3대장 비교 (`generative_models.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/generative_models.png`

```
Three-panel comparison illustration. Left panel: an encoder-decoder shape
with a probability distribution in the latent space. Middle panel: two
stylized neural networks facing each other with a tug-of-war rope. Right
panel: a sequence of images going from pure noise to a clear picture
across multiple steps. Clean infographic style, consistent color palette. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 모델명 약어 VAE, GAN, Diffusion은 영문 그대로 유지 가능).
라벨:
- 왼쪽 패널 제목: "VAE (변분 오토인코더)"
- 왼쪽 패널 내부: "인코더", "잠재 공간", "디코더"
- 가운데 패널 제목: "GAN (적대적 생성 신경망)"
- 가운데 패널 내부: "생성자 (G)", "판별자 (D)"
- 오른쪽 패널 제목: "Diffusion (디퓨전)"
- 오른쪽 패널 양 끝: "노이즈" → "생성 이미지"
```

### A4. AI 시리즈 4부작 매핑 (`series_quadrant.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/series_quadrant.png`

```
A four-quadrant diagram representing the four-part AI introduction series.
Top-left: an icon of a brain in a frame. Top-right: a tree/graph being
explored. Bottom-left: logical formulas and a Bayes net side by side.
Bottom-right: a stylized Transformer + diffusion noise sequence. Subtle
connecting arrows between quadrants showing the conceptual flow. Clean
educational poster style. 1:1 square aspect.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지.
라벨:
- 좌상단: "① AI 정의"
- 우상단: "② 탐색"
- 좌하단: "③ 지식·추론"
- 우하단: "④ 현대 AI"
- 가운데 시리즈 제목: "AI개론 4부작"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
