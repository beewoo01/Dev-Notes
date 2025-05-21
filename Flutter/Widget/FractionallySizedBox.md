# Flutter 위젯 탐구: Expanded, Flexible, FractionallySizedBox 차이점 완전정복

Flutter 레이아웃을 구성할 때 자주 등장하는 세 위젯 — `Expanded`, `Flexible`, `FractionallySizedBox`.  
이 위젯들은 모두 **공간을 나누는 역할**을 하지만, **동작 방식과 목적이 서로 다르다.**

---

## ✅ 공통점 먼저 짚고 가자

이 세 위젯은 모두 **부모가 Row, Column, Flex일 때** 자식 위젯의 크기를 결정하는 데 관여한다.  
즉, **레이아웃 내에서 공간을 어떻게 분배할지**를 정의하는 역할을 한다는 점에서 유사한다.

하지만 동작 방식은 전혀 다르다.

---

## 🔹 Expanded – 남는 공간을 강제로 차지
```dart
Expanded(
  child: Container(
    color: Colors.blue,
    height: 50,
  ),
)
```
- 나는 남은 영역을 전부 차지하는 위젯이 필요할 때 아래와 같이 사용한다.
```dart
Expanded(
  child: SizedBox.shrink();
)
```


- 가능한 **모든 남는 공간을 차지**합니다.
- `flex` 속성으로 비율을 나눌 수 있습니다. (기본값은 1)
- 자식이 원하는 크기가 있더라도 무시하고 **남은 공간에 맞게 확장**한다.

```dart
Row(
  children: [
    Expanded(flex: 1, child: Container(color: Colors.red)),
    Expanded(flex: 2, child: Container(color: Colors.green)),
  ],
)
```

위 예제에서 초록색 컨테이너는 빨간색보다 2배 넓게 그려진다.

---

## 🔹 Flexible – 자식이 원하는 만큼 + 여유 공간 분배

```dart
Flexible(
  flex: 1,
  fit: FlexFit.loose,
  child: Container(
    width: 100,
    height: 50,
    color: Colors.orange,
  ),
)
```

- 자식 위젯이 **원하는 크기를 우선 적용**합니다.
- 남는 공간이 있을 경우, 그 비율만큼 **추가로 확장**할 수 있다.
- `fit`이 `FlexFit.tight`일 경우, `Expanded`처럼 작동한다.

즉, `Flexible`은 "자식이 크기를 결정하고, 필요하면 남은 공간도 일부 차지하겠다"는 유연한 방식이다.

---

## 🔹 FractionallySizedBox – 부모의 비율로 크기 지정

```dart
FractionallySizedBox(
  widthFactor: 0.5,
  heightFactor: 0.3,
  child: Container(color: Colors.purple),
)
```

- 부모 위젯의 **정확한 크기 비율**로 자식의 크기를 지정한다.
- `widthFactor: 0.5` → 부모 너비의 50%  
- `heightFactor: 0.3` → 부모 높이의 30%
- **Row나 Column 내부가 아니어도 사용 가능** 하다.

반응형 UI 구현 시 유용하며, `MediaQuery` 없이도 비율 기반 배치가 가능하다.

---

## 📌 한눈에 비교

| 항목 | Expanded | Flexible | FractionallySizedBox |
|------|----------|----------|-----------------------|
| 공간 분배 기준 | 남은 공간 모두 | 자식의 크기 + 일부 남은 공간 | 부모 크기의 비율 |
| 사용 위치 | Row, Column, Flex | Row, Column, Flex | 어떤 위젯 안에서도 가능 |
| 자식 크기 우선권 | ❌ 없음 | ✅ 있음 | ✅ 비율로 직접 설정 |
| 주 용도 | 비율 분할 레이아웃 | 유연한 레이아웃 | 반응형 크기 설정 |

---

## 🧠 언제 어떤 걸 써야 할까?

- ✅ **항상 남는 공간을 채워야 할 때** → `Expanded`
- ✅ **자식 크기를 우선 적용하고 싶을 때** → `Flexible`
- ✅ **부모 비율에 맞춘 정밀한 크기 조절이 필요할 때** → `FractionallySizedBox`

---

## ✅ 마무리

이 세 위젯은 Flutter UI에서 **공간을 어떻게 배분하고, 자식 위젯에 어떤 제약을 줄지 결정하는 핵심 도구**다.  
특히 반응형 레이아웃이나 가변적인 UI를 다룰 때 그 차이를 이해하고 적절히 선택하는 것이 중요하다.

---
