# 🎯 Object Detection (객체 탐지)

## 📌 객체 감지(Object Detection)란?

**객체 탐지(Object Detection)** 는 컴퓨터 비전 및 이미지 처리 분야의 핵심 기술 중 하나로,  
**디지털 이미지나 영상 속에서 특정 객체가 "어디에" 있는지**, 그리고 **"무엇"인지를 인식하는 작업**입니다.

- 단순히 **무엇이 있는지만 판단하는 분류(Classification)** 와는 달리,  
- 객체 탐지는 **객체의 위치(Bounding Box)** 와 **클래스(Label)** 를 함께 예측합니다.

---

## 🖼️ 예시:  
> 이미지 속에서 고양이와 강아지를 찾고, 각각의 위치에 박스를 그려 클래스까지 알려주는 작업  
→ 이게 바로 **Object Detection**

---

## 🧠 잘 알려진 객체 탐지 예시 분야

- 얼굴 인식 (Face Detection)
- 보행자 감지 (Pedestrian Detection)
- 차량 탐지 (Vehicle Detection)
- 사람, 동물, 사물 등 다중 클래스 탐지

---

## 🔲 Bounding Box란?

객체 탐지 모델은 객체가 이미지 상의 어느 위치에 존재하는지를  
**Bounding Box** 라는 사각형으로 표시합니다.

- 일반적으로 `[x_min, y_min, x_max, y_max]` 또는 `[x_center, y_center, width, height]` 형식
- 이 박스를 기준으로 해당 객체의 **클래스(label)**를 함께 예측

---

## 📌 Object Detection과 Classification의 차이

| Task              | 목표                                     | 출력                                      |
|-------------------|------------------------------------------|-------------------------------------------|
| Classification    | 이미지에 **무엇이 있는지** 파악         | `Label (e.g., Dog)`                       |
| Object Detection  | 이미지에 **무엇이 어디에 있는지** 파악 | `Bounding Box + Label (e.g., [x,y,w,h], Dog)` |

---

## 📚 다음에 다룰 내용 예고

- 🔍 주요 객체 탐지 알고리즘 (YOLO, SSD, Faster R-CNN 등)
- 🏷️ 클래스 불균형, Anchor box, NMS 등 핵심 개념
- 🧪 실습 및 예제 코드 소개 (PyTorch / TensorFlow)

---





# 📌 이미지 기반 딥러닝 작업 정리 (Classification / Localization / Detection)
---
## 1. 🧠 Image Classification (이미지 분류)

- **목표**: 이미지 전체에 대해 어떤 클래스인지 예측
- **출력**: 클래스(label) 1개
- **입력 라벨링**: 이미지 + 클래스 정답 (바운딩 박스 필요 없음)

### 예시
- 입력: 개 사진 한 장
- 출력: `dog`

### 특징
- 단순하고 가장 기본적인 작업
- 바운딩 박스 없음
- 예: ResNet, VGG, EfficientNet 등




---

## 2. 🎯 Single-object Localization (단일 객체 위치 추정)

- **목표**: 이미지에 있는 **하나의 객체** 클래스 + 위치 예측
- **출력**: 클래스(label) + Bounding Box 1개
- **입력 라벨링**: 이미지 + 클래스 + 바운딩 박스 1개

### 예시
- 입력: 개 한 마리가 있는 사진
- 출력: `dog`, 바운딩 박스 (x, y, width, height)

### 특징
- 객체가 1개만 있다고 가정
- 초기 단계 딥러닝 실험에서 자주 사용
- 바운딩 박스 필요

---

## 3. 🔍 Object Detection (객체 탐지)

- **목표**: 이미지에 있는 **모든 객체**의 클래스 + 위치 예측
- **출력**: 클래스 + 바운딩 박스 여러 개
- **입력 라벨링**: 이미지 + 모든 객체의 바운딩 박스 + 클래스

### 예시
- 입력: 개와 고양이가 같이 있는 사진
- 출력:  
  - `dog`, 바운딩 박스 A  
  - `cat`, 바운딩 박스 B

### 특징
- 객체 수 제한 없음
- 위치 예측 + 클래스 분류를 동시에 수행
- 대표 모델: YOLO, SSD, Faster R-CNN

---

## 🧾 요약 비교표

| 작업 종류 | 예측 대상 | 바운딩 박스 | 객체 수 | 주요 모델 |
|-----------|------------|--------------|----------|-------------|
| Image Classification | 클래스 | ❌ 없음 | 1개 | ResNet, EfficientNet 등 |
| Single-object Localization | 클래스 + 위치 | ✅ 있음 | 1개 | (간단한 CNN + 회귀) |
| Object Detection | 클래스 + 위치 | ✅ 있음 | 여러 개 | YOLO, SSD, Faster R-CNN 등 |

---

## 💡 참고 용어 정리

- **Bounding Box**: 객체의 위치를 나타내는 직사각형 정보 (x, y, w, h)
- **Localization**: 객체의 위치만 찾는 개념
- **Detection**: 객체의 위치 + 종류를 찾는 개념
- **Ground Truth**: 학습을 위한 정답 라벨 데이터





---

## 📦 바운딩 박스란?

- 객체가 **어디에 있는지** 나타내는 사각형 좌표 정보
- 모든 Object Detection 모델의 필수 출력 값
- 형식: `(x, y, width, height)` 또는 `(x_min, y_min, x_max, y_max)`


---


# 🧠 Object Detection: One-stage vs Two-stage 정리

---

## ⚙️ Object Detection 전체 흐름

```text
1. 데이터 수집
2. 바운딩 박스 라벨링 (사람이 정답 생성)
3. Train/Validation/Test 데이터 분할
4. 이미지 전처리 (리사이즈, 정규화 등)
5. Detection 알고리즘 선택 (YOLO, Faster R-CNN 등)
6. 모델 학습 (정답과 비교 → Loss 계산 → 역전파)
7. 모델 평가 및 시각화
8. 서비스에 모델 적용
```


## 🔁 One-stage vs Two-stage 차이

| 항목                      | One-stage Detector   | Two-stage Detector             |
| ----------------------- | -------------------- | ------------------------------ |
| 처리 방식                   | 객체 탐지 + 분류를 **한 번에** | 두 단계로 나눠서 처리                   |
| 구조                      | 바로 전체 이미지에서 예측       | 1단계: 후보영역 추출<br>2단계: 후보를 정밀 분류 |
| Region Proposal (후보 영역) | ❌ 없음                 | ✅ 있음                           |
| 속도                      | 빠름 (실시간 가능)          | 느림 (복잡함)                       |
| 정확도                     | 보통 또는 높음             | 일반적으로 더 높음                     |
| 대표 모델                   | YOLO, SSD, RetinaNet | Faster R-CNN, R-CNN            |

---

## 🧠 비유로 이해하기

### 🎯 One-stage Detector  
- 문제를 보면 **바로 답을 찍는 학생**
- 빠르지만 실수 가능성 있음

### 🎯 Two-stage Detector  
- 문제를 보면 먼저 "여기쯤이 정답일 것 같아" → 후보 뽑고  
- 그걸 정답지랑 비교하고 → **맞았는지 확인하면서 정답 보정**
- 느리지만 정확하게 답을 찾음

> 예:  
> - **One-stage**: YOLO는 이미지 전체를 grid로 나누고 각 셀에서 동시에 클래스 + 위치를 예측함  
> - **Two-stage**: Faster R-CNN은 먼저 Region Proposal Network(RPN)으로 후보 박스를 뽑고,  
  그 다음 CNN을 이용해 그 영역을 분류 + 위치 보정함

---

## 📝 핵심 요약

- **바운딩 박스는 항상 존재**하며,  
  객체의 위치를 예측하는 데 사용됨
- **사람이 준 바운딩 박스**는 학습을 위한 "정답지"일 뿐  
  → 모델은 이 정답을 기준으로 **스스로 예측하고 오차를 줄이는 방향으로 학습**함
- **One-stage vs Two-stage**는  
  **예측을 몇 단계로 나눠서 처리하느냐**에 따른 구조적 차이

---

## 🔗 추천 도구

| 작업 | 도구 |
|------|------|
| 바운딩 박스 라벨링 | LabelImg, CVAT, Roboflow |
| One-stage 학습 | YOLOv5, YOLOv8, SSD |
| Two-stage 학습 | Faster R-CNN (Detectron2, MMDetection 등) |

---

## 🔚 마무리 문장

> One-stage 모델은 속도가 빠르고 실시간 애플리케이션에 적합하며,  
> Two-stage 모델은 더 높은 정확도가 필요한 분야에 적합하다.  
> 사용 목적에 따라 적절한 Detection 구조를 선택해야 한다.




