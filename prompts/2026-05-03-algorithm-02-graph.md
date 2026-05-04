# 이미지 생성 프롬프트 — 2026-05-03-algorithm-02-graph

> 원본 포스트: [`_posts/2026-05-03-algorithm-02-graph.md`](../_posts/2026-05-03-algorithm-02-graph.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> _모든 텍스트 라벨은 한글_, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `graph_algo_hero.png`)

> 📁 저장 경로: `/assets/images/algorithm/graph_algo_hero.png`

```
Minimalist isometric illustration of graph algorithm fundamentals: on
the left a small graph (5 nodes, weighted edges) with a queue of nodes
showing BFS expansion order, in the middle the same graph with one
shortest-path tree highlighted in a different color (Dijkstra), on the
right a minimum spanning tree drawn as bold green edges with non-MST
edges grayed out (Kruskal/Prim). Soft pastel palette (sky blue, warm
beige, charcoal accents). Clean white background. Vector flat design.
16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 BFS, DFS, MST, 변수 V, E는 영문 그대로 유지 가능).
라벨: 왼쪽 영역 "그래프 순회 — BFS·DFS",
가운데 영역 "최단 경로 — 다익스트라",
오른쪽 영역 "최소 신장 트리 (MST)",
하단 가운데 "그래프 알고리즘 — 순회·최단·MST".
```

### A2. BFS vs DFS 자료구조 비교 (`bfs_vs_dfs_algo.png`)

> 📁 저장 경로: `/assets/images/algorithm/bfs_vs_dfs_algo.png`

```
Two-panel comparison illustration of graph traversal.
Left panel "BFS": a small graph with arrows showing level-order traversal
from a start node, accompanied by a queue (FIFO) data structure beside
it. Nodes colored by visit order with a horizontal sequence underneath.
Right panel "DFS": same graph with arrows showing depth-first traversal,
accompanied by a stack (LIFO) data structure. Nodes colored by visit
order showing the recursive descent pattern.
Modern infographic style, two accent colors (blue for BFS, orange for
DFS), white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 BFS, DFS, FIFO, LIFO는 영문 그대로 유지 가능).
라벨:
- 좌측 패널 제목: "BFS (너비 우선 탐색)"
- 좌측 자료구조: "큐 (Queue) — FIFO"
- 좌측 부 라벨: "가까운 순서대로 방문"
- 우측 패널 제목: "DFS (깊이 우선 탐색)"
- 우측 자료구조: "스택 (Stack) — LIFO"
- 우측 부 라벨: "한 방향 끝까지 → 백트랙"
- 하단 가운데: "두 순회 모두 시간복잡도 O(V+E)"
```

### A3. 다익스트라 손풀이 트레이싱 (`dijkstra_trace.png`)

> 📁 저장 경로: `/assets/images/algorithm/dijkstra_trace.png`

```
Step-by-step trace of Dijkstra's algorithm on a small weighted graph
with 5 nodes (A, B, C, D, E). Each step shown as a separate snapshot
with: (1) the graph drawn at the top with the *just confirmed* node
highlighted in green, edges being relaxed in dashed orange, (2) a
distance table at the bottom listing current best distances from A.
Five snapshots arranged horizontally: initial → confirm A(0) → confirm
B(1) → confirm C(3) → confirm D(4) → confirm E(7). Modern infographic
style, consistent color palette, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 노드 이름 A, B, C, D, E와 숫자, 수식 표기는 그대로 유지).
라벨:
- 상단 제목: "다익스트라 알고리즘 — 한 단계씩 손풀이"
- 단계 라벨: "0: 초기화", "1: A 확정", "2: B 확정", "3: C 확정", "4: D 확정", "5: E 확정"
- 거리 표 헤더: "정점", "현재 최단거리"
- 색 범례: "녹색 = 방금 확정", "주황 점선 = 간선 완화 (relaxation)"
- 하단 결과: "최단 거리: A→E = 7 (경로 A→B→C→D→E)"
```

### A4. MST — 프림 vs 크루스칼 비교 (`mst_prim_kruskal.png`)

> 📁 저장 경로: `/assets/images/algorithm/mst_prim_kruskal.png`

```
Two-panel comparison illustration of MST algorithms on the same weighted
graph (5 nodes, 6 weighted edges).
Left panel "Prim": shows a sequence of snapshots where the MST grows
outward from a start node, with the priority queue of candidate edges
drawn beside it. Each step adds the cheapest edge crossing the current
tree boundary.
Right panel "Kruskal": shows edges sorted by weight in a list at the top,
then snapshots where edges are added one by one if they don't form a
cycle, with a small union-find forest structure shown beside it.
Both panels end with the same final MST highlighted in green. Modern
infographic style, two accent colors per panel, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 노드 이름과 가중치 숫자, 표준 약어 MST, Union-Find는 영문 그대로 유지).
라벨:
- 좌측 패널 제목: "프림 (Prim) — 정점 중심 확장"
- 좌측 자료구조: "우선순위 큐 (min-heap)"
- 좌측 시간복잡도: "O(E log V)"
- 우측 패널 제목: "크루스칼 (Kruskal) — 간선 중심"
- 우측 자료구조: "유니온-파인드 (Union-Find)"
- 우측 시간복잡도: "O(E log E)"
- 우측 정렬 라벨: "간선 가중치 오름차순 정렬"
- 사이클 표시: "✗ 사이클 발생 — 추가 안 함"
- 하단 가운데: "최종 MST 가중치 합: 7 (두 알고리즘 동일)"
```

### A5. 최단경로 알고리즘 4종 의사결정 트리 (`shortest_path_decision.png`)

> 📁 저장 경로: `/assets/images/algorithm/shortest_path_decision.png`

```
Decision tree diagram for choosing among shortest-path algorithms.
Root question: "어떤 최단경로 문제인가?". Splits into:
- "비가중 그래프" → BFS (O(V+E))
- "가중 + 단일 출발점" → next question:
  - "음의 가중치 ✗" → 다익스트라 (O((V+E) log V))
  - "음의 가중치 ✓ / 음수 사이클 검출" → 벨만-포드 (O(VE))
- "모든 쌍 최단거리" → 플로이드-워샬 (O(V³))
Each leaf node shows algorithm name, time complexity, and a tiny icon
(queue, heap, array, 2D matrix). Clean infographic style, soft pastel
palette per branch, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 알고리즘 이름 BFS와 시간복잡도 표기, 변수 V, E는 영문 그대로 유지).
라벨:
- 루트 질문: "어떤 최단경로 문제인가?"
- 1차 분기: "비가중", "가중 + 단일 출발점", "모든 쌍"
- 2차 분기 (가중 아래): "음의 가중치 ✗", "음의 가중치 ✓"
- 리프 라벨:
  ① "BFS — O(V+E)"
  ② "다익스트라 — O((V+E) log V)"
  ③ "벨만-포드 — O(VE)"
  ④ "플로이드-워샬 — O(V³)"
- 하단 가운데: "최단경로 알고리즘 — 문제 조건으로 선택"
```

### A6. 위상정렬 두 가지 방법 (`topological_sort_two_ways.png`)

> 📁 저장 경로: `/assets/images/algorithm/topological_sort_two_ways.png`

```
Two-panel comparison illustration of topological sort methods on the
same DAG (5 nodes A→B, A→C, B→D, C→D, D→E).
Left panel "Kahn (BFS 기반)": shows a row of in-degree counts beneath
each node, with arrows tracing how nodes with in-degree 0 enter a queue
one by one and produce the order. Each step shows in-degree decrement
on neighbors.
Right panel "DFS 기반": shows DFS recursion descending into the graph,
with a stack on the side accumulating nodes in postorder. The final
order is the stack reversed.
Both panels arrive at the order A → B → C → D → E. Modern infographic
style, two accent colors, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 노드 이름 A, B, C, D, E와 표준 약어 BFS, DFS, DAG는 영문 그대로 유지).
라벨:
- 좌측 패널 제목: "Kahn 알고리즘 (BFS 기반)"
- 좌측 자료구조: "큐 + 진입차수 배열"
- 좌측 단계 라벨: "진입차수 0 → 큐에 투입 → 빼면서 인접 정점 차수 -1"
- 우측 패널 제목: "DFS 기반"
- 우측 자료구조: "재귀 + 스택 (후위 순회)"
- 우측 단계 라벨: "후위 순회 후 결과 역순"
- 공통 결과: "위상정렬 결과: A → B → C → D → E"
- 하단 가운데: "둘 다 O(V+E), DAG 전제"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
