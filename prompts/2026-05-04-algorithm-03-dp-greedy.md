# 이미지 생성 프롬프트 — 2026-05-04-algorithm-03-dp-greedy

> 원본 포스트: [`_posts/2026-05-04-algorithm-03-dp-greedy.md`](../_posts/2026-05-04-algorithm-03-dp-greedy.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `dp_greedy_hero.png`)

> 📁 저장 경로: `/assets/images/algorithm/dp_greedy_hero.png`

```
Minimalist isometric illustration of three algorithm paradigms: on the
left a tree splitting into independent subproblems being solved separately
and merged (Divide & Conquer), in the middle a 2D grid of cells being
filled bottom-up with arrows showing dependencies between cells (Dynamic
Programming), on the right a single path of greedy choices marked with
arrows pointing to the locally best option at each step (Greedy). Soft
pastel palette (sky blue, warm beige, charcoal accents). Clean white
background. Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 DP는 영문 그대로 유지 가능).
라벨: 왼쪽 영역 "분할정복 (Divide & Conquer)",
가운데 영역 "동적 계획법 (DP)",
오른쪽 영역 "그리디 (Greedy)",
하단 가운데 "세 가지 알고리즘 패러다임".
```

### A2. 0/1 배낭 DP 표 채우기 (`knapsack_dp_table.png`)

> 📁 저장 경로: `/assets/images/algorithm/knapsack_dp_table.png`

```
Step-by-step illustration of the 0/1 knapsack DP table being filled.
A 4-row by 6-column grid (i=0..3, w=0..5) shown three times in
sequence: empty → partially filled → fully filled. Each cell shows the
value dp[i][w]. Above the grid, three items are shown with weight and
value labels: (w=2,v=3), (w=3,v=4), (w=4,v=5). Capacity W=5 is
highlighted. The recurrence formula is written below: dp[i][w] =
max(dp[i-1][w], dp[i-1][w-wᵢ] + vᵢ). The final cell dp[3][5] = 7 is
circled in green. Modern infographic style, soft color palette, white
background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 변수 i, w, dp, 수식 max, 숫자는 그대로 유지).
라벨:
- 상단 제목: "0/1 배낭 — DP 표 채우기"
- 물건 라벨: "물건 1 (무게 2, 가치 3)", "물건 2 (무게 3, 가치 4)", "물건 3 (무게 4, 가치 5)"
- 배낭 라벨: "배낭 용량 W = 5"
- 표 헤더: "물건 i \ 무게 w"
- 점화식: "dp[i][w] = max(안 넣음, 넣음)"
- 최종 답: "최댓값 7 — 물건 1+2 선택"
- 하단 가운데: "시간복잡도 O(N · W)"
```

### A3. LCS 표 트레이싱 (`lcs_dp_trace.png`)

> 📁 저장 경로: `/assets/images/algorithm/lcs_dp_trace.png`

```
Detailed visualization of LCS (Longest Common Subsequence) DP table for
X = "ABCBDAB", Y = "BDCAB". A 8x6 grid showing dp[i][j] values, with
diagonal arrows for matching characters (when X[i]=Y[j]) and horizontal/
vertical arrows for the max(dp[i-1][j], dp[i][j-1]) case. The traceback
path showing one of the LCS strings (e.g., "BDAB") is highlighted in
green from the bottom-right corner up to the top-left. The recurrence
formula and the final LCS length 4 are shown below. Clean educational
style, soft pastel palette, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 문자열 ABCBDAB, BDCAB, 변수 dp, i, j, 숫자는 그대로 유지).
라벨:
- 상단 제목: "LCS — 최장 공통 부분수열 표"
- 좌측 헤더: "X = ABCBDAB"
- 상단 헤더: "Y = BDCAB"
- 화살표 범례: "↖ 같음 (대각선 +1)", "↑/← 다름 (max)"
- 점화식 박스: "같으면 dp[i-1][j-1] + 1 / 다르면 max(dp[i-1][j], dp[i][j-1])"
- 정답: "LCS 길이 = 4 (예: BDAB)"
- 하단 가운데: "시간복잡도 O(M · N)"
```

### A4. 그리디 vs DP 의사결정 트리 (`greedy_vs_dp_decision.png`)

> 📁 저장 경로: `/assets/images/algorithm/greedy_vs_dp_decision.png`

```
Decision tree diagram for choosing among the three paradigms. Root:
"문제 조건 확인". First branch: "부분문제가 겹치는가?". Branch "아니오"
leads to "분할정복" with examples (병합정렬·퀵·이진탐색). Branch "예"
leads to a second question: "탐욕 선택 성질이 만족되는가?". Branch
"증명 가능" leads to "그리디" with examples (활동선택·분수배낭·MST·
다익스트라·허프만). Branch "불확실 / 반례 있음" leads to "DP" with
examples (0/1 배낭·LCS·벨만-포드·동전교환). Each leaf shows time
complexity hint. Modern infographic style, three accent colors (one
per paradigm), white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 DP, MST, 시간복잡도 표기는 영문 그대로 유지).
라벨:
- 루트: "문제 조건 확인"
- 1차 질문: "부분문제가 겹치는가?"
- 2차 질문: "탐욕 선택 성질 만족?"
- 리프 1: "분할정복 — 마스터 정리"
- 리프 2: "그리디 — O(n log n)"
- 리프 3: "DP — 상태 × 전이"
- 예시 박스:
  분할정복: "병합·퀵·이진탐색"
  그리디: "활동선택·MST·다익스트라"
  DP: "0/1 배낭·LCS·벨만-포드"
- 하단 가운데: "패러다임 식별 — 문제 조건으로 결정"
```

### A5. 0/1 배낭 vs 분수 배낭 비교 (`knapsack_01_vs_fractional.png`)

> 📁 저장 경로: `/assets/images/algorithm/knapsack_01_vs_fractional.png`

```
Two-panel comparison illustration of two knapsack variants.
Left panel "0/1 배낭": three indivisible items shown as solid boxes
labeled with weight and value, each with a check or cross indicating
"넣음/안 넣음". A small arrow points to "DP 알고리즘 (O(N·W))" with the
recurrence formula. Final selection shown as a packed knapsack.
Right panel "분수 배낭 (Fractional)": same three items but now drawn as
sliceable bars with dashed division lines. A unit-value column (v/w)
is sorted high to low. The greedy selection takes whole items first,
then fractions of the next. Arrow points to "그리디 알고리즘
(O(n log n))". Final selection shown as a knapsack with one item
partially filled.
Modern infographic style, two accent colors per panel, white
background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 DP, 표기 0/1, v/w, 시간복잡도 표기는 영문 그대로 유지).
라벨:
- 좌측 패널 제목: "0/1 배낭 — DP 전용"
- 좌측 부 라벨: "물건을 쪼갤 수 없음 → 그리디 ✗"
- 좌측 알고리즘: "DP — O(N·W)"
- 우측 패널 제목: "분수 배낭 — 그리디 가능"
- 우측 부 라벨: "물건을 쪼갤 수 있음 → 단위가치 큰 순"
- 우측 알고리즘: "그리디 — O(n log n)"
- 하단 가운데: "이름은 비슷해도 알고리즘이 정반대"
```

### A6. DP 4단계 설계 흐름 (`dp_4_steps.png`)

> 📁 저장 경로: `/assets/images/algorithm/dp_4_steps.png`

```
Linear flow diagram showing the four steps of designing a DP solution,
each step represented as a labeled box with an icon and a sample
fragment from the Fibonacci problem.
Step 1 (state definition) — icon of a labeled cell, sample
"dp[i] = i번째 피보나치 수".
Step 2 (recurrence) — icon of an arrow, sample
"dp[i] = dp[i-1] + dp[i-2]".
Step 3 (initial values) — icon of seed values, sample
"dp[0]=0, dp[1]=1".
Step 4 (iteration order) — icon of a sweeping arrow, sample
"i = 2 → n".
The four boxes connected by arrows from left to right. At the bottom,
a small chart shows naive recursion O(2^n) vs DP O(n) timing
comparison as decreasing bars. Modern infographic style, soft pastel
palette, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 변수 dp, i, n, 수식 표기, 숫자는 그대로 유지).
라벨:
- 상단 제목: "DP 설계 4단계 — 피보나치 예시"
- 단계 라벨:
  ① "상태 정의 — dp[i]가 무엇인가"
  ② "점화식 도출"
  ③ "초기값 설정"
  ④ "반복 순서"
- 하단 차트 라벨: "단순 재귀 O(2^n) vs DP O(n) — 지수에서 선형으로"
- 하단 가운데: "DP 4단계로 분해하면 보인다"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
