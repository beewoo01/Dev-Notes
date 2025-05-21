# Flutter 위젯 탐구: SizedBox 제대로 이해하기

Flutter에서 가장 자주 쓰이는 위젯 중 하나인 `SizedBox`.  
심플하지만 다양한 용도로 활용되는 이 위젯을 제대로 이해하고 사용하는게 중요하다.

이번 글에서는 `SizedBox`의 기본 사용법부터 실전 팁까지 정리 할 것이다.

## 📌 SizedBox란?

`SizedBox`는 Flutter에서 **고정된 크기의 박스를 만들거나**  
**공백을 삽입할 때** 사용하는 위젯.  
크기를 지정하지 않으면 기본적으로 자식의 크기에 따라 결정된다.

---

## ✅ 기본 사용법

```dart
SizedBox(
  width: 100,
  height: 50,
  child: ElevatedButton(
    onPressed: () {},
    child: Text("Click me"),
  ),
)
```

- 버튼의 크리를 100x50로 고정함
- 자식 위젯이 있든 없든, 정해진 크기를 가지게 된다.

## 🔹 공백용 Spacer로 활용
```dart
Row(
  children: [
    Icon(Icons.star),
    SizedBox(width: 16), // 아이콘 간격
    Icon(Icons.favorite),
  ],
)
```

`SizedBox`는 자식 없이도 사용할 수 있다.  
이 경우 **정해진 크기만큼의 공백**을 만들어주는 역할을 한다.

---

## 🔹 정사각형 박스를 만들고 싶다면?

```dart
SizedBox.square(
  dimension: 100,
  child: Container(color: Colors.blue),
)
```

`SizedBox.square()`를 사용하면 가로/세로가 같은 정사각형을 간결하게 만들 수 있다.  
코드 가독성을 높이고 의도를 명확히 전달할 수 있다.

---


## 🔹 SizedBox.expand – 남은 공간을 모두 채우기

```dart
SizedBox.expand(
  child: Container(color: Colors.orange),
)
```

`SizedBox.expand()`는 가능한 모든 부모의 제약을 받아서  
**남은 공간을 가득 채우는** 역할을 한다.

- 부모가 `Column`, `Row`, `Container` 같은 고정된 크기를 제공하는 경우  
  → 그 **영역 전체를 채우는 박스**를 만든다.
- 내부 위젯이 크기를 결정하지 않아도 된다.

🟠 주의: `Expanded`와 역할이 유사하지만, `SizedBox.expand()`는 `Row`, `Column` 내부가 아니더라도 사용할 수 있다.

---


## 🔹 SizedBox.shrink – 크기를 0으로 고정

```dart
SizedBox.shrink()
```

`sizedBox.shrink()`은 **완전히 빈 박스**를 만든다.  
즉, **width와 height가 0**인 위젯이다.

- **조건부 렌더링에서 임시로 비워두는 공간**이 필요할 때 사용된다.  
- 자리를 차지하지 않으므로 레이아웃에 영향을 주지 않는다.

예시:

```dart
condition ? Text("보임") : SizedBox.shrink()
```

## 🔹 SizedBox.fromSize – Size 객체를 그대로 전달

```dart
SizedBox.fromSize(
  size: Size(120, 80),
  child: ElevatedButton(
    onPressed: () {},
    child: Text("고정 버튼"),
  ),
)
```
`SizedBox.fromSize()`는 `Size(width, height)` 객체를 사용해서 크기를 지정할 수 있다.

- 이미 `Size` 객체가 정의돼 있는 경우 유용하다.  
- 다른 위젯에서 받은 크기를 그대로 넘길 수 있어 코드 일관성이 좋다.

예시:

final mySize = Size(80, 40)  
...  
SizedBox.fromSize(size: mySize, child: ...)
---

## 📌 생성자별 정리

| 생성자 | 역할 |
|--------|------|
| SizedBox(width, height) | 고정된 크기 지정 |
| SizedBox.square(dimension) | 정사각형 박스 생성 |
| SizedBox.expand() | 가능한 모든 공간 채움 |
| SizedBox.shrink() | 크기 0 (공간 차지 X) |
| SizedBox.fromSize(size: Size(...)) | Size 객체 기반 크기 설정 |

---

## ⚠️ 주의사항

- `SizedBox`는 **부모의 제약이 있는 상황에서만 유효**하다.  
- 레이아웃 전체가 무한대 공간일 경우, `SizedBox`로 크기를 지정해도 **의미 없을 수 있다.**

---

## ✨ 실전 팁

| 상황 | 추천 방식 |
|------|-----------|
| 두 위젯 사이에 일정한 간격을 두고 싶을 때 | `SizedBox(width: x)` or `SizedBox(height: x)` |
| 위젯 크기를 고정하고 싶을 때 | `SizedBox(width: x, height: y)` |
| 정사각형 위젯을 만들고 싶을 때 | `SizedBox.square(dimension: x)` |
| 빈 공간을 차지하고 싶을 때 | `SizedBox.shrink()` 또는 `SizedBox.expand()` |

---

## ✅ 마무리

`SizedBox`는 단순한 레이아웃 도우미를 넘어서,  
**정확한 크기 제어**, **간격 조정**, **UI 구조 표현**에 유용한 도구다.

Flutter 레이아웃에서 자주 사용되는 만큼, 이 위젯을 잘 활용하면  
코드의 **가독성**, **유지보수성**, **UI 명확성**까지 높일 수 있다.


