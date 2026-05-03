# 이미지 생성 프롬프트 — 2026-05-04-ai-advanced-data-mining

> 원본 포스트: [`_posts/2026-05-04-ai-advanced-data-mining.md`](../_posts/2026-05-04-ai-advanced-data-mining.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `data_mining_hero.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/data_mining_hero.png`

```
Minimalist isometric illustration combining data mining and dimension
reduction concepts: on the left a transaction database with arrows
showing item co-occurrence patterns (suggesting Apriori), in the
middle a 3D scatter plot being projected to 2D with arrows showing
PCA dimensionality reduction, on the right a hierarchical dendrogram
tree splitting into clusters. Soft pastel palette (sky blue, warm
beige, charcoal accents). Clean white background. Vector flat design.
16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 PCA, Apriori는 영문 그대로 유지 가능).
라벨: 왼쪽 영역 "연관 규칙 (Apriori)", 가운데 영역 "차원 축소 (PCA)",
오른쪽 영역 "계층적 군집화", 하단 가운데
"데이터마이닝 · 차원 축소 · 진화 알고리즘".
```

### A2. Apriori 알고리즘 단계 (`apriori_algorithm.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/apriori_algorithm.png`

```
Step-by-step illustration of the Apriori algorithm pipeline. From left
to right: (1) initial candidate 1-itemsets shown as circles labeled
{A}, {B}, {C}, ... , (2) support threshold filter (some circles
removed/grayed out), (3) generated 2-itemsets shown as paired
combinations, (4) again filtered by support, (5) progressively larger
itemsets until termination. Below each step, a small support count is
shown. Arrows connect steps. Modern infographic style, two accent
colors, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 Apriori, K, 항목집합 라벨 {A}, {B}, {C} 등은 영문 그대로 유지).
라벨:
- 단계 1: "1-항목집합 후보 생성"
- 단계 2: "지지도 계산 → 가지치기"
- 단계 3: "K-항목집합으로 확장"
- 단계 4: "다시 지지도 계산"
- 단계 5: "반복 → 종료"
- 하단 가운데: "Apriori 알고리즘 — 반단조성으로 가지치기"
```

### A3. PCA 차원 축소 시각화 (`pca_dimensionality.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/pca_dimensionality.png`

```
3D-to-2D projection illustration of PCA. On the left, a 3D scatter
plot of data points with two dashed lines indicating the first two
principal components (longest variance directions). An arrow points
from the 3D space to a 2D plane on the right showing the data
projected onto the plane spanned by these two principal components.
The variance explained by each principal component is shown as a small
bar chart at the bottom. Modern infographic style, soft gradient
background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 PC1, PC2, λ₁, λ₂는 그대로 유지).
라벨:
- 좌측 3D 영역: "원 데이터 (고차원)"
- 좌측 첫 번째 점선: "제1 주성분 (PC1) — 분산 가장 큰 방향"
- 좌측 두 번째 점선: "제2 주성분 (PC2)"
- 화살표: "투영"
- 우측 2D 평면: "저차원 표현"
- 하단 막대 그래프: "주성분별 설명 분산 (eigenvalue λ)"
- 하단 가운데: "PCA — 공분산 행렬 고유벡터로 분산 보존"
```

### A4. 추천 시스템 비교 (`recommender_systems.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/recommender_systems.png`

```
Two-panel comparison illustration of recommender system paradigms.
Left panel "Collaborative Filtering": a user-item rating matrix shown
as a grid with stars, with an empty cell highlighted as "?". Two
similar users (highlighted rows) point to a predicted rating. Below,
labels for "User-based" and "Item-based" approaches.
Right panel "Content-Based": a movie poster icon with metadata tags
(genre, director, actor), arrow pointing to similar movies based on
matching tags. Below, a label "Cold Start = 협업 기반의 문제" is
highlighted in red across both panels. Modern infographic style,
consistent color palette, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지.
라벨:
- 좌측 패널 제목: "협업 기반 필터링 (Collaborative Filtering)"
- 좌측 행렬 헤더: "사용자 × 아이템 평점 행렬"
- 좌측 부 라벨: "사용자 기반 / 아이템 기반"
- 우측 패널 제목: "내용 기반 필터링 (Content-Based)"
- 우측 메타데이터 태그: "장르 · 감독 · 배우 · 키워드"
- 가운데 강조 박스: "콜드 스타트 = 협업 기반의 문제 (내용 기반은 영향 적음)"
- 하단 가운데: "추천 시스템 두 갈래"
```

### A5. 유전 알고리즘 교차 연산 7종 분류 (`crossover_7_types.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/crossover_7_types.png`

```
Seven-panel grid illustration showing different crossover operators in
genetic algorithms. Each panel displays two parent chromosomes (top, two
rows of colored gene boxes) and two offspring chromosomes (bottom),
with the crossover operation visualized between them.
Panel 1 (Single-Point): one vertical cut line, swap right halves.
Panel 2 (Multi-Point / Two-Point): two cut lines, swap middle segment.
Panel 3 (Uniform): random per-gene swap based on a mask.
Panel 4 (Arithmetic): real-valued chromosomes blended with α·P1 + (1-α)·P2.
Panel 5 (OX, Order Crossover): permutation-preserving crossover for TSP-style problems.
Panel 6 (PMX, Partially Matched Crossover): permutation crossover with
mapping table.
Panel 7 (CX, Cycle Crossover): cycle structure preserved between parents.
Below the grid, a hierarchical tree summarizes the classification:
이산형 (1-3) / 실수형 (4) / 순열형 (5-7). Modern infographic style,
consistent color palette per panel, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 OX, PMX, CX, 수식 α는 영문 그대로 유지).
라벨:
- 상단 제목: "유전 알고리즘 교차 연산 7종"
- 패널 제목:
  ① "단일점 교차 (Single-Point)"
  ② "다점 교차 (Multi-Point)"
  ③ "균등 교차 (Uniform)"
  ④ "산술 교차 (Arithmetic) — α·P1 + (1-α)·P2"
  ⑤ "순서 교차 (OX, Order Crossover)"
  ⑥ "부분 매칭 교차 (PMX)"
  ⑦ "사이클 교차 (CX)"
- 부모/자식 라벨: "부모 1, 부모 2", "자식 1, 자식 2"
- 하단 분류 트리: "이산형 비트 (①②③) / 실수형 (④) / 순열형 (⑤⑥⑦)"
- 하단 가운데: "교차 연산 — 염색체 표현에 따라 선택"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
