# 🧠 Semantic Segmentation 정리

---

## 1. 개념 정의

**Semantic Segmentation**은 이미지의 **각 픽셀에 클래스 라벨을 부여하는 기술**로,  
같은 클래스에 속한 픽셀들을 모두 하나의 영역으로 구분함.

예: 사람 3명이 있어도 전부 ‘사람’ 클래스 하나로 마스킹됨.

---

## 2. 주요 특징

- **픽셀 단위 분류**  
- 같은 클래스의 객체는 **개별 구분 없이 하나의 덩어리로 처리**
- Instance 구분은 안 됨 (→ 그건 Instance Segmentation의 역할)

---

## 3. 입력과 출력

| 항목 | 설명 |
|------|------|
| **Input** | 일반 RGB 이미지 |
| **Output** | 각 픽셀에 대한 클래스 맵  
예: `[0, 0, 1, 1, 2, 2, ...]` (배경, 사람, 도로 등) |

---

## 4. 전처리 필요 요소

Semantic Segmentation은 정확한 마스킹을 위해 다음과 같은 이미지 전처리가 적용되기도 함:

- **Filters** (Gaussian, Sobel 등)
- **Gradient 정보** (엣지 감지)
- **Color 정보** (HSV, LAB 색공간 등)
- **Normalize, Resize, Augmentation 등**

---

## 5. 대표 모델

| 모델 | 특징 |
|------|------|
| **FCN** | 최초의 End-to-End 픽셀 분류 네트워크 |
| **U-Net** | 의료 이미지에서 시작. Encoder–Decoder + Skip 연결 |
| **DeepLab (v3+)** | Atrous Convolution + CRF로 경계 개선 |
| **SegFormer** | Transformer 기반 경량 모델. 최근 SOTA 중 하나 |

---

## 6. 활용 분야

- 자율주행 (도로, 차선, 보행자 구분)
- 의료 영상 분석 (장기/병변 분할)
- 위성 영상 (토지, 건물 분할)
- 산업/검사/AR 등

---

## 7. 한 줄 요약

> ✅ Semantic Segmentation은 **이미지를 픽셀 단위로 해석하여 각 픽셀의 의미(Class)를 분류하는 기술**이며,  
> 같은 클래스를 가진 객체들은 **하나의 덩어리**로 취급됨.

