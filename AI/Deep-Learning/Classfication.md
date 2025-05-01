# 📊 Classification Metrics in Deep Learning

딥러닝에서 **메트릭(Metrics)**은 모델의 성능을 평가하기 위해 사용되는 측정 지표입니다.  
이는 모델의 **예측 결과와 실제 정답(ground truth)**을 비교하여,  
모델이 특정 작업에서 얼마나 잘 작동하는지를 수치적으로 나타내줍니다.

메트릭의 선택은 **태스크 유형**과 **출력 형식**에 따라 달라지며,  
**분류(Classification)** 작업에서는 아래와 같은 메트릭들이 일반적으로 사용됩니다.

---

## ✅ 1. Accuracy (정확도)

> 전체 예측 중에서 정답을 맞힌 비율



$$
\text{Accuracy} = \frac{\text{True Positives (TP)} + \text{True Negatives (TN)}}{\text{Total Samples}}
$$

- 가장 기본적인 메트릭
- 클래스가 균형 잡혀 있을 때 유용함
- **단점:** 클래스 불균형(class imbalance)이 심한 경우 의미 없는 결과를 줄 수 있음

---

## ✅ 2. Precision (정밀도)

> 모델이 양성이라고 예측한 것들 중 실제로 양성인 비율

$$
\text{Precision} = \frac{\text{True Positives (TP)}}{\text{True Positives (TP)} + \text{False Positives (FP)}}
$$

- **정확하게 양성만 골라내는 능력**을 측정
- 잘못된 양성 예측을 줄이고 싶을 때 중요 (ex: 스팸 필터)

---

## ✅ 3. Recall (재현율, 민감도)

> 실제 양성 중에서 모델이 양성으로 잘 맞춘 비율

$$
\text{Recall} = \frac{\text{True Positives (TP)}}{\text{True Positives (TP)} + \text{False Negatives (FN)}}
$$

- **놓치지 않고 양성을 잘 잡아내는 능력**을 측정
- 놓치는 게 치명적인 경우 중요 (ex: 암 진단)

---

## ✅ 4. F1 Score (조화 평균)

> 정밀도와 재현율의 균형을 잡기 위한 지표

$$
\text{F1 Score} = 2 \times \frac{\text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}}
$$

- Precision과 Recall 간의 **trade-off**를 반영
- **불균형 데이터셋**에서 유용

---

## ✅ 5. AUC-ROC (Area Under the ROC Curve)

> 분류 모델의 **판별 능력**을 평가하는 지표  
> ROC 커브는 **거짓 양성 비율(FPR)** vs **참 양성 비율(TPR)**의 관계를 시각화한 그래프

- AUC는 ROC 곡선 아래 면적을 의미 (1에 가까울수록 좋음)
- **이진 분류에서 모델의 전반적인 성능 평가**에 유용
- **불균형 클래스 문제**에 강함

---

## 📌 요약 비교표

| Metric      | 수식 | 언제 유용한가? |
|-------------|------|----------------|
| Accuracy    | (TP + TN) / Total | 클래스 균형일 때 |
| Precision   | TP / (TP + FP)    | False Positive 줄이고 싶을 때 |
| Recall      | TP / (TP + FN)    | False Negative 줄이고 싶을 때 |
| F1 Score    | 2 × (P × R) / (P + R) | Precision과 Recall 사이 균형이 중요할 때 |
| AUC-ROC     | 면적 기반 지표    | 분류 임계값이 다양한 상황에서 모델 성능 비교 시 |

---

### 🧠 Tip:
- 다중 클래스 분류(multi-class classification)에서는 위 메트릭들을 **micro, macro, weighted average** 방식으로 확장해서 사용합니다.
- 모델 목적에 따라 어떤 메트릭을 사용할지 전략적으로 선택해야 합니다.

