# 이미지 생성 프롬프트 — 2026-05-03-ai-advanced-classical-ai

> 원본 포스트: [`_posts/2026-05-03-ai-advanced-classical-ai.md`](../_posts/2026-05-03-ai-advanced-classical-ai.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `classical_ai_hero.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/classical_ai_hero.png`

```
Minimalist isometric illustration of classical AI topics: on the left a
stylized expert system with a knowledge base shelf and an inference
engine gear, in the middle a 3x4 decision matrix with five different
criteria icons (averaging, max, min, balance scale, regret arrow), on
the right a fuzzy set graph showing a smooth membership curve from
0 to 1. Soft pastel palette (sky blue, warm beige, charcoal accents).
Clean white background. Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 μ, α, ∩, ∪는 그대로 유지).
라벨: 왼쪽 영역 "전문가 시스템", 가운데 영역 "5기준 의사결정", 오른쪽 영역 "퍼지 이론",
하단 가운데 "고전 AI 보충 — 전문가시스템·5기준·퍼지·DIKW".
```

### A2. 5기준 의사결정 표 (`decision_5criteria.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/decision_5criteria.png`

```
Wide infographic showing a 3x4 decision table at the top (rows: high/
medium/low demand, columns: small/medium/large/extra-large factory)
with example payoff numbers. Below, five colored boxes side by side,
each representing one decision criterion (Laplace, Maximax, Maximin,
Hurwitz, Savage), each box showing the chosen alternative and the key
formula. Clean infographic style, distinct color for each criterion,
white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수식 max, min, α, β=1-α는 그대로 유지).
라벨:
- 표 행: "수요 상", "수요 중", "수요 하"
- 표 열: "소규모", "중규모", "대규모", "초대규모"
- 5개 박스 제목: "라플라스 (평균→최대)", "맥시맥스 (낙관)", "맥시민 (비관·안전제일)",
  "호르비츠 (α·max+(1-α)·min)", "새비지 (후회 최소)"
- 하단 가운데: "불확실성 5기준 의사결정"
```

### A3. 퍼지 vs 확률 (`fuzzy_vs_probability.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/fuzzy_vs_probability.png`

```
Two-panel comparison illustration. Left panel shows a die mid-roll with
six possible faces, each labeled with a probability of 1/6, captioned
as "결정적 결과, 일어날 확률". Right panel shows a smooth membership
function curve over a number line for "젊은이 (Young)" — flat at 1
for ages 0-30, smoothly decreasing to 0 over ages 30-60, captioned as
"대상 자체가 모호". Clean educational style, two distinct color
palettes for each panel, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 μ, x, [0,1]은 그대로 유지).
라벨:
- 좌측 패널 제목: "확률 (Probability)"
- 좌측 부제: "결과는 확정, 일어날 가능성"
- 좌측 주사위 라벨: "P(눈=k) = 1/6"
- 우측 패널 제목: "퍼지 (Fuzzy)"
- 우측 부제: "대상 자체가 모호"
- 우측 그래프 X축: "나이"
- 우측 그래프 Y축: "소속도 μ"
- 우측 곡선 라벨: "'젊은이' 집합"
- 하단 가운데: "확률 vs 퍼지 — 같은 불확실, 다른 본질"
```

### A4. DIKW 계층 + 관련 시스템 (`dikw_pyramid.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/dikw_pyramid.png`

```
Pyramid infographic with four horizontal layers (bottom to top): Data,
Information, Knowledge, Wisdom. Each layer colored differently and
larger at the bottom. To the right of each layer, a small system icon
list shows the related technologies. From bottom: Data → DB, OLTP,
ETL, Data Lake icons; Information → DW, OLAP icons; Knowledge → KMS,
EKP icons; Wisdom → BI icon. Clean infographic style, gradient color
from blue (bottom) to gold (top). White background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 시스템 약어 DB, OLTP, ETL, DW, OLAP, KMS, EKP, BI, Data Lake는 영문 그대로 유지).
라벨:
- 바닥층: "Data (데이터) — 가공되지 않은 원자료"
- 2층: "Information (정보) — 정제·가공"
- 3층: "Knowledge (지식) — 일반화·체계화"
- 꼭대기: "Wisdom (지혜) — 근본원리 + 창의"
- 우측 시스템 라벨:
  - "Data 단계: DB · OLTP · ETL · Data Lake"
  - "Information 단계: DW · OLAP"
  - "Knowledge 단계: KMS · EKP"
  - "Wisdom 단계: BI"
- 하단 가운데: "DIKW 계층구조 + 관련 시스템"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
