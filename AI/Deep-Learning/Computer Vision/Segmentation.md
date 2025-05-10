# 🧠 Segmentation 정리

---

## 1. 개념 정의

**Segmentation**은 이미지에서 의미 있는 **영역(region)** 또는 **구조(structure)**를 식별하고 분리하는 과정입니다.

---

## 2. 입력과 출력

| 항목        | 설명                                                                 |
|-------------|----------------------------------------------------------------------|
| **Input**   | 일반 이미지 (`inputImage`)                                           |
| **Output**  | 분할된 시각적 구조                                                   |
|             | - **Regions** (예: 사람, 배경, 도로 등)                              |
|             | - **Structures** (예: 선분, 곡선 등) → `line segments`, `curve segments` |

---

## 3. 이미지 전처리 (Pre-processing)

> 대부분의 Segmentation 알고리즘은 정확한 구조 추출을 위해 사전 이미지 처리 단계가 필요합니다.

### 🔧 주요 처리 기법

- **Filters**: 노이즈 제거 및 엣지 강조 (예: Gaussian, Sobel)
- **Gradient Information**: 픽셀 간 밝기 변화율 → 경계 감지에 활용
- **Color Information**: 색상 기반 분할 (예: HSV, LAB 등 색공간 변환)
- **기타**: 히스토그램 평활화, 블러링, 샤프닝 등

---

## 4. 분할 방식 분류

| 분류 유형               | 설명                                                      | 대표 모델/기법                  |
|------------------------|-----------------------------------------------------------|----------------------------------|
| **Semantic Segmentation** | 각 픽셀에 클래스 라벨 할당. 동일 클래스는 묶음               | FCN, U-Net, DeepLab              |
| **Instance Segmentation** | 클래스는 물론 개별 객체 단위로도 구분                       | Mask R-CNN, YOLACT               |
| **Panoptic Segmentation** | Semantic + Instance를 모두 처리                            | Panoptic FPN, UPSNet             |
| **기하 기반 Segmentation** | 구조적 특징 (선, 곡선, 경계 등)을 분할                       | Hough Transform, Edge Linking 등 |

---

## 5. 전체 처리 흐름 요약

**입력 → 처리 → 결과까지의 전체 흐름:**

**[Input Image]**  
&nbsp;&nbsp;&nbsp;&nbsp;↓  
**[Pre-processing: Filter, Gradient, Color...]**  
&nbsp;&nbsp;&nbsp;&nbsp;↓  
**[Segmentation Algorithm]**  
&nbsp;&nbsp;&nbsp;&nbsp;↓  
**[Output: Regions, Line Segments, Curve Segments]**


**🔹 입력:** `Input Image`  
➡️ **전처리:** Filters, Gradient Info, Color Info...  
➡️ **Segmentation Algorithm 실행**  
➡️ **출력:** Regions, Line Segments, Curve Segments

