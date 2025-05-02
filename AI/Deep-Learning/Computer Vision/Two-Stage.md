
# 🎯 Two-Stage Object Detection 모델 정리

Two-stage 객체 탐지 모델은 객체 탐지(Object Detection)를  
두 개의 주요 단계로 분리하여 수행하는 구조를 갖는다.

---

## ✅ 구조 개요

Two-stage 모델은 다음과 같은 두 단계로 구성된다:

1. **Stage 1: Region Proposal**
   - 입력 이미지에서 객체가 있을 법한 후보 영역(Region Proposal)을 생성
   - 예: RPN (Region Proposal Network), Selective Search 등

2. **Stage 2: Classification & Regression**
   - Stage 1에서 생성된 Region Proposal을 기반으로
   - 각 영역이 어떤 클래스인지 분류(Classification)
   - 해당 영역의 바운딩 박스를 정밀하게 조정(Regression)

---

## 🧩 구성 요소

| 구성 요소 | 역할 | 설명 |
|------------|------|------|
| **CNN Backbone** | Feature Map 생성 | 이미지에서 시각적 특징 추출 |
| **Region Proposal Module** | 객체 후보 영역 생성 | 예: RPN (딥러닝), Selective Search (전통 알고리즘) |
| **Detection Head** | 분류 + 위치 보정 | FC Layer 기반, 분기 구조로 구성됨 |

---

## 📦 대표적인 Two-Stage 모델

- **R-CNN**  
  CNN + SVM + Bounding Box Regressor  
  (Region Proposal: Selective Search)

- **Fast R-CNN**  
  CNN → Feature Map 공유  
  RoI Pooling + FC + Softmax + BBox Regression

- **Faster R-CNN**  
  RPN을 통해 Region Proposal도 CNN으로 처리  
  완전한 end-to-end 학습 가능

---

## ✅ 특징 및 장점

- **정확도 우수**  
  → 객체 탐지 정확도가 One-stage 모델보다 일반적으로 높음  
- **모듈화 구조**  
  → 각 단계의 기능이 분리되어 설계 유연성 높음  
- **복잡한 객체 분할에 강함**  
  → 작은 객체나 겹친 객체 탐지에 유리함

---

## ⚠️ 단점

- **속도 느림**  
  → 두 단계의 처리로 인해 Inference 시간이 길어짐  
- **구현 복잡도 높음**  
  → 각 구성요소 간의 연계, 좌표 매핑 등 처리 필요

---

## 🧠 학습 구조 관점

Two-stage 모델은 내부적으로 다음과 같은 세 파트로 나뉘어 학습된다:

1. **Backbone CNN**: Feature Map 생성
2. **RPN**: Anchor 기반 Region Proposal 예측 (Classification + Regression)
3. **Detection Head**: 각 RoI에 대해 클래스 + 위치 예측 (Softmax + Regression)

> ✔️ 따라서 구조적으로 보면 **3개의 학습 대상이 있는 복합 모델**이라고 볼 수 있다.  
> ✔️ 하지만 대부분의 프레임워크에선 통합된 하나의 모델로 구현된다.

---

## ✔️ 한 문장 요약

> **Two-stage 모델은 Region Proposal과 객체 인식(분류/위치 보정)을 분리하여 처리함으로써,  높은 정확도의 객체 탐지를 달성하는 구조이다.**




# 🔍 R-CNN, Fast R-CNN, Faster R-CNN 파이프라인 정리

객체 탐지(Object Detection)의 대표적인 Two-Stage 모델인  
R-CNN 계열 3가지 알고리즘의 구조 차이를 단계별로 정리한다.

---

## 1️⃣ R-CNN (Region-based CNN, 2014)

### 📦 파이프라인 흐름

1. **Input Image**
2. **Selective Search**
   → 입력 이미지에서 Region Proposal(약 2000개) 생성
3. **Crop & Resize**
   → 각 Region Proposal을 원본 이미지에서 잘라서 CNN 입력 크기로 조정
4. **CNN (개별 수행)**
   → 각 Region에 대해 CNN을 따로 수행하여 Feature Vector 추출
5. **SVM 분류기** (Classification)
   → 추출된 Feature Vector로 클래스 분류
6. **Bounding Box Regressor**
   → 초기 박스의 위치를 보정

### 🔧 특징

- Region마다 CNN을 수행 → 매우 느림
- CNN, SVM, Regressor가 각각 따로 학습됨
- End-to-end 학습 불가능

---

## 2️⃣ Fast R-CNN (2015)

### 📦 파이프라인 흐름

1. **Input Image**
2. **CNN (한 번만 수행)**
   → 전체 이미지에 대해 Feature Map 생성
3. **Selective Search**
   → Region Proposal을 이미지 기준 좌표로 생성
4. **RoI Projection**
   → Region Proposal을 Feature Map 상 좌표로 매핑
5. **RoI Pooling**
   → 각 RoI를 고정 크기(예: 7×7)로 변환
6. **Flatten → FC Layer**
7. **Softmax (분류) + BBox Regression (좌표 보정)**

### 🔧 특징

- CNN을 한 번만 수행 → 연산량 대폭 감소
- RoI Pooling 도입 → 다양한 크기의 박스를 고정 벡터로 변환
- End-to-end 학습 가능 (SVM 제거됨)

---

## 3️⃣ Faster R-CNN (2016)

### 📦 파이프라인 흐름

1. **Input Image**
2. **CNN (Backbone)**
   → Feature Map 생성
3. **Region Proposal Network (RPN)**
   → Feature Map에서 Anchor 기반 Region Proposal 생성  
   → Classification + Bounding Box Regression 포함
4. **RoI Projection**
   → RPN의 출력을 Feature Map 상 위치로 매핑
5. **RoI Pooling**
6. **Flatten → FC Layer**
7. **Softmax (분류) + BBox Regression (좌표 보정)**

### 🔧 특징

- Region Proposal까지 CNN으로 처리 (Selective Search 제거)
- 완전한 End-to-End 구조
- 빠르면서도 높은 정확도 유지

---

## ✅ 세 모델 비교 요약표

| 항목 | R-CNN | Fast R-CNN | Faster R-CNN |
|------|-------|-------------|---------------|
| Region Proposal 방식 | Selective Search | Selective Search | ✅ RPN (CNN 기반) |
| CNN 수행 횟수 | 수천 번 (Region마다) | 1번 (전체 이미지) | 1번 (전체 이미지) |
| RoI Pooling | ❌ 없음 | ✅ 있음 | ✅ 있음 |
| 분류기 | SVM (외부) | Softmax (내부) | Softmax (내부) |
| 위치 보정 | 별도 회귀기 | 내부 회귀기 | 내부 회귀기 |
| End-to-End 학습 | ❌ 불가능 | ✅ 가능 | ✅ 가능 |
| 속도 | ❌ 매우 느림 | ⚠️ 개선됨 | ✅ 빠름 |

---

## ✔️ 한 문장 요약

> R-CNN → Fast R-CNN → Faster R-CNN 순으로  
> Region Proposal 생성 방식과 CNN 연산 구조를 점차 통합하고 최적화하여,  
> 더 빠르고 효율적인 객체 탐지 모델로 발전해왔다.






# 🗣️ R-CNN 계열 모델 파이프라인 (말로 푼 설명)

---

## 1️⃣ R-CNN

입력 이미지를 받으면,  
Selective Search를 사용해 객체가 있을 법한 위치(Region Proposal)를 수천 개 생성한다.  
각 Region Proposal마다 이미지를 잘라서 CNN에 개별적으로 입력하고,  
CNN을 통해 각 Region의 특징 벡터(Feature Vector)를 뽑는다.  
이 벡터를 SVM에 넣어 어떤 클래스인지 분류하고,  
별도의 Bounding Box Regressor로 위치를 보정한다.

CNN, SVM, 회귀기가 각각 따로 작동하며, 연산량이 많고 느리다.

---

## 2️⃣ Fast R-CNN

입력 이미지를 CNN에 한 번만 통과시켜 전체 이미지에 대한 Feature Map을 먼저 만든다.  
같은 이미지에서 Selective Search로 Region Proposal을 생성하고,  
각 Region Proposal을 Feature Map 위 좌표로 매핑(RoI Projection)한다.  
매핑된 영역을 RoI Pooling을 통해 고정 크기의 특징 벡터로 압축하고,  
Flatten → Fully Connected Layer를 거쳐  
Softmax로 분류(클래스 예측), BBox Regression으로 위치를 보정한다.

CNN 연산을 공유하고, 모든 과정이 end-to-end로 학습 가능하다.

---

## 3️⃣ Faster R-CNN

입력 이미지를 CNN에 통과시켜 Feature Map을 생성하고,  
이 Feature Map을 다시 Region Proposal Network(RPN)에 입력하여  
객체가 있을 법한 위치를 Anchor 기반으로 예측한다.  
RPN이 생성한 Region Proposal은 Feature Map 상에 매핑(RoI Projection)되고,  
RoI Pooling을 통해 고정 크기의 벡터로 변환된다.  
이 벡터를 Fully Connected Layer에 통과시켜  
Softmax로 분류하고, BBox Regression으로 위치를 보정한다.

Region Proposal까지도 CNN으로 처리되기 때문에,  
완전한 end-to-end 학습이 가능하고 속도도 대폭 향상된다.
