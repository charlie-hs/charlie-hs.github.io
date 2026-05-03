# 이미지 생성 프롬프트 — 2026-04-27-ai-introduction-knowledge-representation

> 원본 포스트: [`_posts/2026-04-27-ai-introduction-knowledge-representation.md`](../_posts/2026-04-27-ai-introduction-knowledge-representation.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `kr_hero.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/kr_hero.png`

```
Minimalist isometric illustration of knowledge representation in AI:
on the left half a glowing logic-formula floating in space (with symbols
∀ ∃ → ∧), in the middle a network of interconnected nodes with generic
concept icons, on the right a directed acyclic graph with probability bars
over each node. A faint robotic silhouette observes the three structures
from below. Soft pastel palette (sky blue, warm beige, charcoal accents).
Clean white background. Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 논리 기호 ∀, ∃, →, ∧는 그대로 유지).
라벨: 세 영역에 각각 "논리", "시맨틱 네트워크", "베이즈 네트워크",
하단 로봇 위 "지식 표현".
```

### A2. 명제 논리 vs 술어 논리 비교 (`propositional_vs_fol.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/propositional_vs_fol.png`

```
Two side-by-side blackboard panels in chalkboard style. Left panel shows
simple propositions P, Q, R connected by ∧ and →. Right panel shows
∀x. Human(x) → Mortal(x), with little stick figures representing multiple
objects in a row beneath. Subtle hand-drawn educational style. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 논리 수식 P, Q, R, ∀, ∃, →, ∧, Human(x), Mortal(x)은 수식이므로 그대로 유지).
라벨: 왼쪽 패널 제목 "명제 논리", 오른쪽 패널 제목 "술어 논리 (FOL)",
막대 인물들 아래 한글 라벨 "객체들".
```

### A3. 시맨틱 네트워크의 상속 (`semantic_network.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/semantic_network.png`

```
Educational diagram showing a hierarchy of concept nodes connected by
labeled relation edges. Top node connects down through a middle node to
a bottom node. A small bird silhouette is placed below as an instance.
A wings icon is attached to the middle node. A red strikethrough arrow
from the bottom node represents an exception. Clean infographic style,
white background, two accent colors. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 관계명 IS-A, HAS-A, INSTANCE-OF는 영문 그대로 유지 가능).
라벨:
- 노드: 위에서부터 "동물", "새", "펭귄"
- 인스턴스 실루엣: "Tweety"
- 관계 라벨: "IS-A", "HAS-A", "INSTANCE-OF"
- 날개 아이콘 옆: "날개"
- 빨간 X 표시 옆: "예외: 못 난다"
```

### A4. 베이즈 네트워크 — 스프링클러 예제 (`bayesian_network.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/bayesian_network.png`

```
Clean directed acyclic graph diagram with four nodes: one at the top, two
in the middle (both children of the top), and one at the bottom (child of
both middle nodes). Each node accompanied by a small mini-table showing
conditional probability values. Subtle cloud, sprinkler, rain, and grass
icons next to each node. Modern infographic style, light gradient
background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 변수 약어 C, S, R, W와 확률 표기 P(...|...), 숫자 값은 그대로 유지).
라벨:
- 위 노드: "흐림 (C)"
- 가운데 왼쪽: "스프링클러 (S)"
- 가운데 오른쪽: "비 (R)"
- 아래 노드: "잔디 젖음 (W)"
- 각 미니 표 헤더: "조건부 확률표 (CPT)"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
