# 🧠 Convolutional Neural Network (CNN)

**CNN(합성곱 신경망)** 은 주로 **이미지 데이터** 를 처리하기 위해 고안된 딥러닝 모델입니다.  
입력 데이터의 **공간 구조(위치, 패턴 등)**를 고려하여 효율적으로 특징을 추출할 수 있습니다.  
최근에는 컴퓨터 비전뿐 아니라 **자연어 처리(NLP)** 분야에도 응용되고 있습니다.

---

## 1️⃣ Convolutional Layer (합성곱 계층)

CNN의 핵심은 **합성곱(convolution)** 연산을 통해 **로컬 패턴(local patterns)**을 포착하는 것입니다.

- 입력 이미지에 대해 여러 개의 **필터(커널)**를 적용
- 각 필터는 특정한 특징(예: 에지, 패턴 등)을 감지
- 출력은 **특징 맵(feature map)** 또는 **activation map**이라고 불림

\[
\text{Output} = \text{Input} * \text{Filter} + \text{Bias}
\]

---

## 2️⃣ Pooling Layer (풀링 계층)

풀링은 특징 맵의 **공간 크기를 줄이고** 계산량과 과적합을 방지합니다.

- **Max Pooling**: 필터 영역 내에서 최대값을 선택
- **Average Pooling**: 평균값을 선택

📌 장점:
- **차원 축소**
- **위치 변화에 대한 불변성 확보**

---

## 3️⃣ Non-Linearity (비선형성)

합성곱과 풀링을 거친 출력은 **비선형 활성화 함수**를 통과하게 됩니다.

- 가장 널리 사용되는 함수는 **ReLU(Rectified Linear Unit)**:
  \[
  f(x) = \max(0, x)
  \]
- 이를 통해 **복잡한 함수 표현**이 가능해짐
- 최근에는 Leaky ReLU, GELU 등 다양한 변형도 사용됨

---

## 4️⃣ Fully Connected Layer (완전 연결 계층)

합성곱 및 풀링 계층을 통과한 데이터를 **1차원 벡터로 평탄화(flatten)** 하여  
전통적인 MLP(Multi-Layer Perceptron) 방식의 FC 계층에 연결합니다.

- 마지막 FC 계층에서 **분류 결과(softmax 등)**를 출력
- 예: 10개 클래스 → FC output: `[0.1, 0.05, ..., 0.2]`

---

## 5️⃣ Applications (적용 분야)

CNN은 다양한 분야에서 활용됩니다:

### 🖼️ 컴퓨터 비전
- 이미지 분류 (Image Classification)
- 객체 탐지 (Object Detection)
- 이미지 세그멘테이션 (Segmentation)
- 얼굴 인식, 자율 주행 등

### 🧾 자연어 처리
- 문장 분류 (Sentiment Analysis)
- 키워드 감지
- 문서 분류 등 (단어 임베딩 + 1D Convolution 조합으로 사용)

---

## 6️⃣ Advantages (장점)

CNN이 널리 사용되는 이유는 다음과 같습니다:

- ✅ **계층적 표현 학습**: 하위 계층은 단순한 특징, 상위 계층은 복잡한 패턴을 학습
- ✅ **변환 불변성**: 위치, 회전, 크기 변화에 강함
- ✅ **특징 추출 자동화**: 수동 feature engineering 없이도 데이터에서 의미 있는 특징을 학습
- ✅ **파라미터 효율성**: 동일한 필터를 공유하여 학습 파라미터 수 감소

---
