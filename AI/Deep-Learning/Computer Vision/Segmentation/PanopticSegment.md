# 🧠 Panoptic Segmentation 정리

---

## 🔍 개념

- 기존 **Instance Segmentation**은 배경은 무시하고, **개별 객체(Things)**만 구분
- **Panoptic Segmentation**은 **배경(Stuff)**과 **객체(Things)** 모두를 **픽셀 단위로 구분**
- 즉, **Semantic Segmentation + Instance Segmentation**을 통합한 접근

---

## 🏗 대표 모델

### 1. **Panoptic FPN**

> FPN 기반 Instance Segmentation 구조에 Semantic Branch 추가

- Instance Branch: Mask R-CNN 방식 사용
- Semantic Branch: 픽셀 단위로 클래스 예측
- **Pixel-wise segmentation을 위해 feature map이 갖춰야 할 조건**
  1. 고해상도 (High resolution)
  2. 풍부한 의미 정보 (Rich semantics)
  3. 다중 크기 대응 (Multi-scale 정보)

---

### 2. **UPSNet**

- FPN 기반 구조
- **Head 구성**
  - `Semantic Head`: Fully Convolution 구조로 semantic map 예측
  - `Instance Head`: Detection, Box Regression, Mask Logit 추출
  - `Panoptic Head`: 두 결과를 융합하여 최종 segmentation map 생성

---

### 3. **VPSNet**

> 실시간 처리를 위한 UPSNet 확장 구조

- **구성 흐름**
  1. Feature Alignment (Pixel-level Fusion)
  2. Track Module (Instance-level 연계)
  3. Fusion & Tracking 모듈 간 joint training

---

### 4. **EfficientPS**

- `EfficientNet`을 백본으로 사용
- `Bidirectional Feature Pyramid Network` 포함  
  → 고품질 panoptic 결과 생성

---

## 🧪 모델 성능 평가 (Evaluation)

### 🎯 Panoptic Quality (PQ)


PQ = SQ × RQ

- **SQ (Segmentation Quality)**  
  → 예측한 segment가 GT와 얼마나 잘 일치하는가 (TP만 고려)
  
- **RQ (Recognition Quality)**  
  → Precision과 Recall을 함께 고려한 인식 성능

---

## 🧾 용어 정리

| 용어 | 의미 |
|------|------|
| **GT (Ground Truth)** | 사람이 라벨링한 실제 정답 데이터 |
| **TP (True Positive)** | 예측이 GT와 정확히 일치한 경우 |
| **FP (False Positive)** | 없는 것을 있다고 예측함 |
| **FN (False Negative)** | 있었지만 놓친 경우 |

---

## 📐 Precision & Recall

### Precision (정밀도)

Precision = TP / (TP + FP)

> 예측한 것 중 실제로 맞은 비율

예시:  
고양이라고 예측한 10개 중 실제 고양이는 7개  
→ Precision = 7 / 10 = 0.7

---

### Recall (재현율)


> 실제 정답 중에서 얼마나 많이 맞췄는가

예시:  
실제 고양이 10마리 중 7마리를 맞춤  
→ Recall = 7 / 10 = 0.7

---

### F1 Score (정밀도 & 재현율 조화 평균)

F1 = 2 × (Precision × Recall) / (Precision + Recall)

---

## ⚠️ 주의사항 (Panoptic 기준)

- 하나의 픽셀은 **오직 하나의 GT segment**에만 매핑
- IoU ≥ 0.5를 만족하는 segment는 **하나만 TP로 인정**
- 나머지는 **FP로 간주되거나 무시**

---
