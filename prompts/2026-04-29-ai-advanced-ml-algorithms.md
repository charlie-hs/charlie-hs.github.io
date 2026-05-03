# 이미지 생성 프롬프트 — 2026-04-29-ai-advanced-ml-algorithms

> 원본 포스트: [`_posts/2026-04-29-ai-advanced-ml-algorithms.md`](../_posts/2026-04-29-ai-advanced-ml-algorithms.md)
>
> 본 포스트에서 사용·예정인 이미지 생성 프롬프트 모음.
> *모든 텍스트 라벨은 한글*, 파일명·저장 경로 명시.

---

## 부록 A: 이미지 생성 프롬프트

> 본문은 Mermaid로 거의 모든 시각화를 처리했다. 추가 이미지가 필요할 때만 사용.

### A1. Hero 이미지 (포스트 상단용 — `ml_algorithms_hero.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/ml_algorithms_hero.png`

```
Minimalist isometric illustration of machine learning algorithms:
on the left a 2D scatter plot with three colored clusters separated by
a smooth decision boundary curve, in the middle a stylized decision tree
branching into multiple leaves, on the right a forest of small trees
representing an ensemble. A subtle robotic agent observes the three
structures from below. Soft pastel palette (sky blue, warm beige,
charcoal accents). Clean white background. Vector flat design. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 표준 약어 ML, KNN, SVM 등은 영문 그대로 유지 가능).
라벨: 왼쪽 영역 "분류 (Classification)", 가운데 "결정 트리", 오른쪽 "앙상블",
하단 로봇 옆 "머신러닝 알고리즘".
```

### A2. SVM 마진 시각화 (`svm_margin.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/svm_margin.png`

```
Clean technical illustration of SVM with linear decision boundary:
two clusters of data points (one red, one blue) on a 2D plane separated
by a solid black line (decision boundary). Two parallel dashed lines on
either side of the boundary represent the margins. Several data points
on each margin line are highlighted with circles to indicate support
vectors. Arrow shows the margin width. Clean infographic style, white
background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, 수학 기호 w, b, ξ는 그대로 유지).
라벨:
- 가운데 실선: "결정 경계 (w·x + b = 0)"
- 양쪽 점선: "+ 마진 경계", "- 마진 경계"
- 강조된 점: "서포트 벡터"
- 화살표: "마진 = 2/||w||"
- 두 클러스터: "클래스 +1", "클래스 -1"
```

### A3. 앙상블 3종 비교 (`ensemble_methods.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/ensemble_methods.png`

```
Three-panel comparison illustration of ensemble methods.
Left panel: multiple identical small trees in a row, all receiving the
same input simultaneously, with output combined by voting (parallel).
Middle panel: small trees arranged sequentially, each one focusing on
errors of the previous (sequential). Right panel: multiple diverse
models (tree, line, network) feeding their outputs into a single larger
"meta-model" box at the top. Clean infographic style, consistent color
palette, white background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지.
라벨:
- 왼쪽 패널 제목: "배깅 (Bagging) — 병렬 학습"
- 왼쪽 패널 부제: "Random Forest"
- 가운데 패널 제목: "부스팅 (Boosting) — 순차 학습"
- 가운데 패널 부제: "AdaBoost / XGBoost"
- 오른쪽 패널 제목: "스태킹 (Stacking) — 메타 모델"
- 오른쪽 패널 상단 박스: "메타 모델 (Level-2)"
- 오른쪽 패널 하단: "다양한 기본 모델 (Level-1)"
```

### A4. 혼동행렬과 평가지표 (`confusion_matrix.png`)

> 📁 저장 경로: `/assets/images/ai-introduction/confusion_matrix.png`

```
Clean infographic illustration of a 2x2 confusion matrix on the left
side, with four cells filled with example numbers. On the right side,
formulas for Accuracy, Precision, Recall, Specificity, and F1-score are
listed with arrows pointing to the cells they reference. Color coding:
TP and TN cells in green, FP and FN cells in red. Modern educational
style, light gradient background. 16:9.

CRITICAL: 이미지 내 모든 문자/라벨은 반드시 한글로 표시. 영문 텍스트 금지
(단, TP, TN, FP, FN, P, R 등 표준 약어는 영문 그대로 유지 가능).
라벨:
- 표 행 헤더: "실제 양성 (Positive)", "실제 음성 (Negative)"
- 표 열 헤더: "예측 양성", "예측 음성"
- 각 셀: TP / FN / FP / TN (영문 약어 그대로)
- 우측 공식 라벨:
  - "정확도 (Accuracy) = (TP+TN) / 전체"
  - "정밀도 (Precision) = TP / (TP+FP)"
  - "재현율 (Recall) = TP / (TP+FN)"
  - "특이도 (Specificity) = TN / (TN+FP)"
  - "F1 = 2·P·R / (P+R)"
```

> 💡 위 프롬프트는 모두 본문 텍스트에 의존하지 않는 자기 완결형 이미지를 만들도록 작성됐다.

---
