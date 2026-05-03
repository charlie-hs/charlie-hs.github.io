# 이미지 생성 프롬프트 — 2026-05-02-ai-advanced-rnn-rl

> 원본 포스트: [`_posts/2026-05-02-ai-advanced-rnn-rl.md`](../_posts/2026-05-02-ai-advanced-rnn-rl.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `rnn_rl_hero.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/rnn_rl_hero.png`

```
Minimalist isometric illustration combining sequence learning and
reinforcement learning: on the left a chain of RNN cells with arrows
showing recurrent connections through time, in the middle an LSTM cell
with three highlighted gates (Forget, Input, Output), on the right a
robotic agent interacting with an environment in a closed loop with
arrows for action and reward. Soft pastel palette (sky blue, warm
beige, charcoal accents). Clean white background. Vector flat design.
16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 RNN, LSTM은 영문 그대로 유지 가능).
라벨: 왼쪽 영역 "RNN — 시간 순환", 가운데 영역 "LSTM — 3 게이트",
오른쪽 영역 "강화학습 — 에이전트와 환경", 환경-에이전트 화살표 "행동·보상",
하단 가운데 "RNN과 강화학습".
```

### A2. LSTM 게이트 구조 (`lstm_gates.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/lstm_gates.png`

```
Detailed technical diagram of a single LSTM cell. The cell contains:
input x_t arriving from below, previous hidden state h_{t-1} and
previous cell state C_{t-1} arriving from the left, three gates
(Forget, Input, Output) shown as colored boxes with sigmoid activation
inside, a candidate cell state computation with tanh, multiplication
and addition operations along the cell-state highway. Output: new cell
state C_t and new hidden state h_t exit on the right. Modern infographic
style, three accent colors for the three gates. White background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 σ, tanh, ×, +, 변수 C_t, h_t, x_t, f_t, i_t, o_t는 그대로 유지).
라벨:
- 입력: "현재 입력 x_t", "이전 은닉 상태 h_{t-1}", "이전 셀 상태 C_{t-1}"
- 게이트: "망각 게이트 (Forget)", "입력 게이트 (Input)", "출력 게이트 (Output)"
- 후보 상태: "후보 셀 상태 (C̃_t)"
- 출력: "새 셀 상태 C_t", "새 은닉 상태 h_t"
- 핵심 식 박스: "C_t = f_t × C_{t-1} + i_t × C̃_t"
```

### A3. 강화학습 사이클 (`rl_cycle.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/rl_cycle.png`

```
Clean technical illustration of the reinforcement learning loop. A
robotic agent on the left and a stylized environment (game world or
maze) on the right, connected by two thick curved arrows forming a
cycle. The top arrow goes from agent to environment labeled "Action".
The bottom arrow returns from environment to agent, splitting into two
labeled flows: "State (next observation)" and "Reward (scalar signal)".
Below the diagram, a small reward bar chart visualizes accumulated
returns over time. Modern infographic style, two accent colors, white
background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수식 r_t, s_t, a_t, γ는 그대로 유지).
라벨:
- 왼쪽 인물: "에이전트 (Agent)"
- 오른쪽 영역: "환경 (Environment)"
- 위쪽 화살표: "행동 (Action) a_t"
- 아래쪽 화살표 1: "다음 상태 (State) s_{t+1}"
- 아래쪽 화살표 2: "보상 (Reward) r_{t+1}"
- 하단 누적 보상 라벨: "누적 보상 G_t = r_{t+1} + γ·r_{t+2} + ..."
- 하단 부제: "강화학습 사이클 — 시행착오로 정책 학습"
```

### A4. 강화학습 분류 (`rl_taxonomy.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/rl_taxonomy.png`

```
Hierarchical tree diagram classifying reinforcement learning methods.
Root: "강화학습". Splits into two main branches:
1. Model-Free (left): Further splits into Value-Based (DQN, Q-learning)
   and Policy-Based (REINFORCE, PPO, A3C).
2. Model-Based (right): AlphaZero, World Models, MuZero.
A small Actor-Critic bridge connects Value-Based and Policy-Based.
Each leaf node labeled with model name and key year. Clean infographic
style, two accent colors for the main branches, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 모델명 DQN, Q-learning, REINFORCE, PPO, A3C, AlphaZero, MuZero,
World Models는 영문 그대로 유지).
라벨:
- 루트: "강화학습 (Reinforcement Learning)"
- 1차 분기: "Model-Free (모델 없음)", "Model-Based (모델 사용)"
- 2차 분기 (Model-Free 아래): "Value-Based (가치 기반)", "Policy-Based (정책 기반)"
- 가운데 다리: "Actor-Critic (둘 다)"
- 모델 라벨: "DQN (2015)", "Q-learning (1989)", "PPO (2017)", "A3C (2016)", "AlphaZero (2018)"
```

### A5. 시그모이드 vs Tanh 미분 비교 (`sigmoid_vs_tanh_derivative.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/sigmoid_vs_tanh_derivative.png`

```
Two-panel mathematical comparison illustration of activation function
derivatives. Left panel: sigmoid σ(z) curve in (0,1) with its derivative
σ'(z) = σ(1-σ) below it, peak value 0.25 at z=0 highlighted with a
horizontal dashed line. Right panel: tanh(z) curve in (-1,1) with its
derivative 1 - tanh²(z) below it, peak value 1.0 at z=0 highlighted.
Between the two panels, a vertical bar chart compares the maximum
gradient values (0.25 vs 1.0). Below, a small annotation explains why
RNNs prefer tanh: "tanh의 그래디언트가 4배 커서 장기 의존성에 유리".
Clean educational style, light grid lines, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수식 σ(z), σ'(z), tanh(z), 1-tanh²(z), z, 숫자 0.25, 1.0은 그대로 유지).
라벨:
- 좌측 패널 제목: "시그모이드와 미분"
- 좌측 위 곡선: "σ(z) — (0,1) 범위"
- 좌측 아래 곡선: "σ'(z) = σ(1-σ) — 최댓값 0.25"
- 우측 패널 제목: "Tanh와 미분"
- 우측 위 곡선: "tanh(z) — (-1,1) 범위, 0 중심"
- 우측 아래 곡선: "1 - tanh²(z) — 최댓값 1.0"
- 가운데 막대 차트: "최대 그래디언트 비교: 0.25 vs 1.0 (4배 차이)"
- 하단 가운데: "RNN이 tanh를 쓰는 이유 — 그래디언트 소실 완화"
```

### A6. 2024-24번 CNN·RNN 보기 4종 통합 분석 (`cnn_rnn_2024_analysis.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/cnn_rnn_2024_analysis.png`

```
Four-panel educational diagram analyzing 2024 7급 데이터직 24번 question
options on CNN and RNN architectures. Each panel shows one option with a
small visual and a verdict (true/false) marker.
Top-left (CNN): convolution operation with shared kernel sliding over
input — verdict checkmark, label "CNN의 가중치 공유 — 맞음".
Top-right (RNN): chain of unrolled RNN cells through time — verdict
checkmark, label "RNN의 시간 순환 구조 — 맞음".
Bottom-left (Pooling): max-pooling reducing 4x4 feature map to 2x2 —
verdict checkmark, label "풀링으로 차원 축소 — 맞음".
Bottom-right (LSTM gates): LSTM cell with three gates highlighted —
verdict cross, label "LSTM 게이트 수 = 3 (맞음/틀림 보기 분석)".
Modern infographic style, consistent color palette, white background,
two accent colors for true/false. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 CNN, RNN, LSTM, 숫자는 그대로 유지).
라벨:
- 상단 제목: "2024 7급 데이터직 24번 — CNN·RNN 통합 분석"
- 좌상단 패널: "보기 ① CNN 가중치 공유"
- 우상단 패널: "보기 ② RNN 시간 순환"
- 좌하단 패널: "보기 ③ 풀링 차원 축소"
- 우하단 패널: "보기 ④ LSTM 게이트 구조"
- 정답 표시: "✓ 옳음", "✗ 틀림"
- 하단 가운데: "기출 1문제로 4개 핵심 개념 동시 점검"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
