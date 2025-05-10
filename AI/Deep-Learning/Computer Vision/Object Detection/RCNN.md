# 📦 R-CNN (Region-based Convolutional Neural Network) 구조 정리

딥러닝 기반 객체 탐지의 시초가 된 R-CNN의 전체 구조와 각 단계를 정리합니다.  
하나의 이미지를 입력으로 받아, 객체의 **위치(Bounding Box)** 와 **클래스(Class)** 를 예측하는 전체 파이프라인입니다.

---

## ✅ 전체 흐름

1. **Input Image**
   → 한 장의 이미지를 입력한다.

2. **Selective Search**  
   → 이미지에서 객체가 있을 법한 후보 영역(Region Proposal)을 약 2000개 생성한다.  
   → 전통적인 방식으로 색상, 질감, 모양 등의 유사성을 기반으로 영역을 병합하여 생성.
   → 논문에서는 Selective Search라고 되어있지만 Region Proposal이 더 정확한거 같다.

3. **Crop & Resize**  
   → 각 Region Proposal을 이미지에서 잘라내고  
   → CNN의 입력 크기(예: 224×224)에 맞게 Resize한다.

4. **CNN 통과 → Feature Vector 추출**  
   → 잘라낸 이미지를 CNN에 넣어 특징을 추출하고,  
   → 고정된 길이의 **feature vector**로 변환한다.  
   → 이 과정에서 Fully Connected Layer가 내부적으로 포함됨.

5. **SVM 분류 (Classification)**  
   → 추출된 feature vector를 전통적인 SVM 분류기에 넣어,  
   → 해당 Region이 어떤 클래스(고양이, 자동차 등)인지 예측한다.

6. **Bounding Box Regression**  
   → 초기 Region Proposal의 위치는 부정확할 수 있기 때문에,  
   → 별도의 회귀 모델을 통해 (x, y, w, h) 바운딩 박스 좌표를 정밀하게 보정한다.

---

## 🔍 핵심 특징

- **하이브리드 구조**:  
  → CNN은 특징 추출만 하고, 분류는 SVM, 위치 보정은 Linear Regressor로 따로 수행한다.

- **비효율적인 연산**:  
  → Region Proposal마다 CNN을 **개별적으로 수행**하기 때문에 매우 느림.

- **전통 + 딥러닝의 혼합**:  
  → Region Proposal은 전통 알고리즘(Selective Search),  
  → 특징 추출은 딥러닝(CNN),  
  → 분류는 전통 ML(SVM)을 사용.

---

## 🧠 R-CNN이 남긴 의미

- CNN 기반 객체 탐지의 문을 연 모델
- 이후 Fast R-CNN, Faster R-CNN, YOLO 등의 기반이 되었음
- End-to-End 학습이 불가능하다는 한계로 인해 구조 개선이 계속됨

---

