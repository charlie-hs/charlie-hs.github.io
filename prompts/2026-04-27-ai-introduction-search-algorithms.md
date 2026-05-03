# 이미지 생성 프롬프트 — 2026-04-27-ai-introduction-search-algorithms

> 원본 포스트: [`_posts/2026-04-27-ai-introduction-search-algorithms.md`](../_posts/2026-04-27-ai-introduction-search-algorithms.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트 (다이어그램으로 표현 어려운 경우 참고)

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `search_hero.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/search_hero.png`

```
Minimalist isometric illustration of search algorithms in AI:
a stylized graph/tree on the left half with nodes connected by edges in
varying weights, and a robotic agent on the right exploring the graph with
glowing path traces in different colors. Two figures facing each other in
a small inset representing minimax. Soft pastel palette (sky blue, warm beige,
charcoal accents). Clean white background. Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 알고리즘 약어 BFS, DFS, A*는 영문 그대로 유지 가능).
라벨: 색깔별 경로에 "BFS (파랑)", "DFS (초록)", "A* (빨강)",
미니맥스 인셋 두 인물에 "MAX (나)"와 "MIN (상대)".
```

### A2. BFS vs DFS 시각 비교 (`bfs_vs_dfs.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/bfs_vs_dfs.png`

```
Side-by-side educational illustration. Left panel shows a tree being explored
level by level with concentric ripples expanding from the root. Right panel
shows the same tree with a single deep arrow piercing down one branch then
backtracking. Below each, a small box indicates memory complexity. Clean
infographic style, two accent colors (blue, orange). 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, BFS, DFS 약어와 수학 표기 O(b^d), O(bm)는 그대로 유지).
라벨:
- 왼쪽 패널 제목: "너비 우선 탐색 (BFS)"
- 오른쪽 패널 제목: "깊이 우선 탐색 (DFS)"
- 메모리 박스: "메모리: O(b^d)" / "메모리: O(bm)"
```

### A3. A* 휴리스틱 직관 (`astar_heuristic.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/astar_heuristic.png`

```
Diagram-style illustration of A* heuristic search on a 2D grid map.
A start node on the left, a goal on the right. Concentric contour lines
emanating from the goal represent decreasing h(n) values. A glowing path
is drawn from start to goal, hugging the contour gradient — suggesting the
algorithm feels its way toward the goal. Obstacles drawn as gray blocks.
Clean technical illustration, one accent color, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 알고리즘명 A*와 수식 h(n)은 그대로 유지).
라벨: 시작점 "출발 (S)", 도착점 "목표 (G)", 장애물 영역 "장애물",
등고선 옆 "휴리스틱 h(n)", 경로 위 "탐색 경로".
```

### A4. 알파-베타 가지치기 (`alpha_beta_pruning.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/alpha_beta_pruning.png`

```
Stylized minimax game tree with three levels. Some leaves and their parent
edges shown bright with values; others are crossed out with a red tag and
a dashed border, indicating branches not explored. Triangular icons
distinguish two types of nodes (▲ and ▼). Clean infographic style, 16:9,
white background.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 그리스 문자 α, β와 노드 유형 약어 MAX, MIN은 그대로 유지 가능).
라벨: 노드 라벨 "MAX (▲)", "MIN (▼)", 잘려나간 가지에 "가지치기" 태그,
α/β 값 표기 옆에 한글 설명 "α (최선 하한)", "β (최악 상한)".
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기-완결형 이미지를 만들도록 작성됐다.

---
