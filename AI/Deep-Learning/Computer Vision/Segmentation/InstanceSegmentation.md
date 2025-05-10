# 📌 Instance Segmentation 정리

---

## ✅ 개념 정의

**Instance Segmentation**은 **객체 감지(Object Detection)**와 **세그멘테이션(Segmentation)**을 결합한 기술로,  
- 이미지 속 **각 객체를 픽셀 단위로 구분**하고,  
- 같은 클래스 안에서도 객체 하나하나를 **개별 인스턴스로 식별**합니다.

---

## ✅ 차이점 비교

| 구분 | 설명 |
|------|------|
| **Object Detection** | 고양이 2마리의 위치를 네모 박스로만 표시 |
| **Semantic Segmentation** | 고양이 전체를 픽셀 단위로 마킹하나, 두 마리를 **하나처럼** 취급함 |
| **Instance Segmentation** | 고양이 두 마리를 **각각 픽셀 단위로 구분**함 |

---

## ✅ 사용 분야

- 자율주행 차량 (보행자/차량 구분)  
- 의료 영상 분석 (조직/세포 분할)  
- 로보틱스 (객체 조작 시 픽셀 정확도 필요)  
- 증강현실(AR), 영상 편집  

---

## ✅ 장점

- **정밀한 객체 분리 가능**  
  → 픽셀 단위로 객체를 구분하기 때문에, 물체의 정확한 윤곽을 파악할 수 있음

- **복잡한 장면에서도 객체 인식 가능**  
  → 객체가 겹쳐 있거나 비슷한 배경을 가진 상황에서도 정확한 분할이 가능

- **후처리 및 연계 활용도 높음**  
  → 추출된 마스크를 통해 객체 영역만 따로 잘라내거나, 합성·추적 등 다른 작업에 활용하기 용이함

- **세밀한 행동 인식/제스처 분석 가능**  
  → 예: 사람의 팔, 손, 다리 등 객체 내부의 미세한 움직임까지 분석 가능

- **AR/VR 및 로봇 비전 등에 적합**  
  → 배경 제거, 객체 조작 등 실시간·정밀 환경에서 특히 유리함

---

## ⚠️ 단점

- **Small Object 문제**: 작고 희미한 객체는 감지·분할이 어려움  
- **Annotation 난이도**: 인스턴스별 마스크 주석은 노동 집약적이며,  
  Semantic Segmentation보다 **모델에 직접 활용하기 어려움**

---

## 🧭 Instance Segmentation 방법론

---

### ✅ Proposal-Based 방식

> "Detection → Segmentation" 순서로 수행

- **SS / RPN / Faster R-CNN** 등으로 Bounding Box (proposal) 생성  
- 각 박스 내부에서 픽셀 단위 마스크 생성

**요약:**  
탐지된 Bounding Box를 기반으로 픽셀 수준의 segmentation mask를 예측하는 방식

---

### ✅ Proposal-Free 방식

> 후보 영역 없이 **픽셀 feature를 학습하여 클러스터링**으로 인스턴스 분리

- 픽셀 단위 feature를 학습하고  
- **비슷한 픽셀끼리 묶는(clustering)** 과정을 통해 객체를 분리

**요약:**  
픽셀 수준의 feature를 기반으로 객체를 직접 클러스터링하여 인스턴스를 분리

---

## 🧠 Clustering Pixels

- 비슷한 feature를 가진 픽셀을 그룹화  
- 일종의 **Super Pixel 기법과 유사**하게, 각 객체를 독립 클러스터로 나눔

---

## 🧪 대표 모델 요약

---

### ✅ Proposal-Based 모델들

#### 🔸 MNC (Multi-task Network Cascade)
- 모든 Proposal에 대해 개별적으로 처리  
- Instance-sensitive map 사용  
- Fixed-size window 내 pooling

#### 🔸 FCIS (Fully Convolutional Instance-aware Segmentation)
- Position-sensitive score map을 활용하여 각 인스턴스를 분리  
- FCN 구조를 확장한 인스턴스 분할 구조

#### 🔸 Mask R-CNN
- Faster R-CNN에 segmentation 분기를 추가한 구조  
- **클래스 분류 + 박스 예측 + 마스크 생성**을 동시에 수행  
- 가장 널리 사용되는 Instance Segmentation 표준 모델

#### 🔸 PANet (Path Aggregation Network)
- Feature pyramid를 더 깊이 결합 (path augmentation)  
- Adaptive Feature Pooling 적용  
- Mask R-CNN보다 더 깊고 정밀한 구조

#### 🔸 YOLACT (You Only Look At CoefficienTs)
- **실시간 가능한 One-stage Instance Segmentation**  
- Feature Pyramid 사용  
- Prototype mask + coefficient 방식으로 빠르게 마스크 생성  
- Soft mask basis 개념 (선형 결합을 통한 마스크 합성)  
- 실제 마스크를 만들기 위한 재료(basis)들을 생성한다고 이해하면 좋음

#### 🔸 YOLACT Edge
- YOLACT를 **모바일/엣지 환경**에 최적화  
- Keyframe의 feature를 다음 프레임에 전달하여 연산량 절감  
- 빠른 속도와 경량화된 구조로 엣지 디바이스에서 유용

---

### ✅ Proposal-Free 모델들

#### 🔸 InstanceCut
- 경계 감지를 활용한 그래프 컷 기반 인스턴스 분할

#### 🔸 SGN (Spatial Graph Network)
- 픽셀 간 관계를 그래프 형태로 표현하여 인스턴스 분리
