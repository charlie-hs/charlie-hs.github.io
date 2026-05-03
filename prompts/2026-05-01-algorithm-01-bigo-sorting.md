# 이미지 생성 프롬프트 — 2026-05-01-algorithm-01-bigo-sorting

> 원본 포스트: [`_posts/2026-05-01-algorithm-01-bigo-sorting.md`](../_posts/2026-05-01-algorithm-01-bigo-sorting.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `algorithm_hero.png`)

> 📁 저장 경로: `/assets/images/algorithms/algorithm_hero.png`

```
Minimalist isometric illustration of algorithm complexity and sorting:
on the left a coordinate plane showing growth curves for O(1), O(log
n), O(n), O(n log n), O(n²), O(2ⁿ) labeled accordingly, in the middle
a stylized array being divided and merged (suggesting merge sort), on
the right a max-heap binary tree structure. Soft pastel palette (sky
blue, warm beige, charcoal accents). Clean white background. Vector
flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 시간복잡도 표기 O(1), O(log n), O(n), O(n log n), O(n²), O(2ⁿ),
O(n!)는 그대로 유지).
라벨: 좌측 영역 "시간복잡도 성장률", 가운데 영역 "분할정복 정렬",
우측 영역 "힙 자료구조", 하단 가운데 "알고리즘 분석 — Big-O · 정렬 · 자료구조".
```

### A2. 정렬 6종 비교표 시각화 (`sorting_comparison.png`)

> 📁 저장 경로: `/assets/images/algorithms/sorting_comparison.png`

```
Wide infographic table comparing six sorting algorithms (Bubble,
Selection, Insertion, Quick, Merge, Heap) across columns: best/average/
worst time complexity, space complexity, stability. Each row colored
in a distinct accent color, with stable sorts highlighted with a green
checkmark and unstable with a red X. Below the table, three key
takeaway boxes: "O(n²) 그룹", "O(n log n) 그룹", "안정 vs 불안정".
Modern infographic style, clean educational layout. White background.
16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 시간복잡도 표기 O(n), O(n log n), O(n²), O(log n), O(1)는 그대로 유지).
라벨:
- 표 행: "버블 정렬", "선택 정렬", "삽입 정렬", "퀵 정렬", "병합 정렬", "힙 정렬"
- 표 열: "최선", "평균", "최악", "공간", "안정성"
- 안정성 라벨: "안정", "불안정"
- 하단 박스: "O(n²) 그룹: 버블·선택·삽입", "O(n log n) 그룹: 퀵·병합·힙",
  "안정: 버블·삽입·병합 / 불안정: 선택·퀵·힙"
- 상단 부제: "정렬 알고리즘 6종 비교"
```

### A3. 자료구조 시간복잡도 (`data_structure_complexity.png`)

> 📁 저장 경로: `/assets/images/algorithms/data_structure_complexity.png`

```
Two-column infographic comparing data structure operations. Left column
"선형 자료구조": rows for Array, Linked List, Stack, Queue, with
columns for Access, Search, Insert, Delete time complexities. Right
column "트리·해시": rows for BST, AVL, Hash Table, Heap, with
columns for Search, Insert, Delete (and average vs worst for BST and
Hash). Color coding: O(1) green, O(log n) yellow, O(n) red. Modern
educational layout. White background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 시간복잡도 표기와 자료구조 약어 BST, AVL은 그대로 유지).
라벨:
- 좌측 열 제목: "선형 자료구조"
- 우측 열 제목: "트리·해시"
- 좌측 행: "배열", "연결 리스트", "스택", "큐"
- 우측 행: "이진탐색트리(BST)", "AVL 트리", "해시 테이블", "힙"
- 컬럼 헤더: "접근", "검색", "삽입", "삭제"
- 색상 범례: "O(1) 빠름", "O(log n) 보통", "O(n) 느림"
- 하단 부제: "자료구조 기본 연산 시간복잡도"
```

### A4. Big-O 성장률 그래프 (`bigo_growth_rates.png`)

> 📁 저장 경로: `/assets/images/algorithms/bigo_growth_rates.png`

```
Coordinate plane graph showing growth curves for various time
complexities. X-axis labeled with input size, Y-axis labeled with
operation count, with curves for O(1) (flat horizontal), O(log n)
(slow growing), O(n) (linear), O(n log n) (slightly steeper), O(n²)
(quadratic, sharply rising), O(2ⁿ) (exponential, very steep), O(n!)
(factorial, fastest rising). Each curve in a different color, labeled
inline. Annotations: "효율적", "비효율적" arrows along the Y-axis.
Modern educational illustration, soft gradient background, clean
gridlines. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 시간복잡도 표기 O(1), O(log n), O(n), O(n log n), O(n²), O(2ⁿ),
O(n!)는 그대로 유지).
라벨:
- X축: "입력 크기 (n)"
- Y축: "연산 횟수"
- Y축 부 라벨 위쪽: "비효율적", 아래쪽: "효율적"
- 각 곡선 인라인 라벨: "O(1) 상수", "O(log n) 로그", "O(n) 선형",
  "O(n log n) 선형로그", "O(n²) 이차", "O(2ⁿ) 지수", "O(n!) 계승"
- 하단 가운데: "시간복잡도 성장률 비교 — Big-O 표기"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
