# 🧠 One-Stage Object Detection 정리

## ✅ 개요

**One-Stage Object Detection**은 입력 이미지를 한 번에 처리하여  
객체의 **위치(Bounding Box)**와 **클래스(Classification)**를 동시에 예측하는 방식이다.

> Region Proposal 없이 작동하며, 실시간 탐지에 적합한 구조로 설계된다.

---

## 📦 전체 구조

Input Image
↓
Convolution Layers (Feature Extraction)
↓
Multi-Scale Feature Maps
↓
Object Detection Head
→ Classification (무엇인지)
→ Localization (어디 있는지: Bounding Box)


---

## 🧱 핵심 개념

### 🎯 Object Detection이란?

- 이미지 또는 영상에서 **객체가 어디에 있고, 무엇인지**를 탐지하는 기술
- 구성 요소:
  - **Localization**: 객체의 위치(Bounding Box) 예측
  - **Classification**: 객체의 종류(Class) 분류

---

### 🔄 Multi-Scale Feature Layer (SSD 기준)

> 다양한 크기의 객체를 탐지하기 위해  
여러 해상도의 Feature Map을 동시에 활용한다.

- CNN을 여러 번 통과시키며 Feature Map을 축소
- 작은 Feature Map → 큰 객체 탐지  
  큰 Feature Map → 작은 객체 탐지

> 각 Feature Map에 Detection Head를 붙여 독립적으로 객체 탐지를 수행하고, 결과를 통합한다.

---

### 📐 Default Box (Anchor Box)

- **각 Feature Map의 위치마다 다양한 비율/크기의 Anchor Box를 미리 배치**
- 이 Anchor를 기준으로 객체가 있는지 판단하고 (Classification),  
  실제 객체와 얼마나 위치/크기가 다른지 보정 (Regression)
- 학습 시 Ground Truth와 비교해 손실 계산

> Anchor는 객체가 있을 법한 후보 박스의 역할을 한다.

---

### 📉 NMS (Non-Maximum Suppression)

> 겹치는 박스가 너무 많을 때,  
**중복되는 박스를 제거하고 신뢰도 높은 것만 남기는 후처리 기법**

- 예측된 모든 BBox를 신뢰도 순으로 정렬  
- 하나 선택 후, **IoU가 일정 기준 이상인 박스는 제거**  
- 이 과정을 반복하여 중복을 제거함

---

### 📏 IoU (Intersection over Union)

> 예측된 박스와 실제 박스가 얼마나 겹치는지를 0~1 사이로 계산한 점수

- 수식:  IoU = (두 박스가 겹치는 면적) / (두 박스가 차지하는 전체 면적)


- IoU가 높을수록 예측이 잘된 것
- 일반적으로 0.5 이상이면 양호한 예측으로 간주

---

### 🧾 Ground Truth

- 사람이 직접 라벨링한 **정답 Bounding Box**
- 모델은 이 Ground Truth와 Anchor Box를 비교하여  
Classification과 Regression을 학습한다

---

## 🚀 대표 모델

| 모델 | 설명 |
|------|------|
| **SSD** | 다양한 크기의 객체 탐지를 위해 Multi-Scale 구조 사용 |
| **YOLOv1~v8** | 속도 최적화에 초점을 둔 초고속 실시간 탐지 모델 |
| **RetinaNet** | Focal Loss를 도입해 작은 객체와 불균형 데이터 문제 해결 |
| **FCOS / YOLOX / CenterNet** | Anchor-free 구조의 최신 탐지 방식 |

---

## ✅ 최종 요약

> **One-Stage Object Detection은 Region Proposal 단계 없이  
입력 이미지에서 객체의 위치와 클래스를 한 번에 예측하는  
빠르고 효율적인 탐지 방식이다.**  

> SSD, YOLO 등 다양한 모델이 이 방식을 기반으로 발전해왔으며,  
최근에는 Anchor-free 모델들도 활발히 연구되고 있다.

