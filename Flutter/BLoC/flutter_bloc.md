# 🔁 Flutter BLoC 패턴 with `flutter_bloc` 패키지

Flutter에서 상태 관리를 효율적으로 할 수 있도록 도와주는 공식 BLoC 라이브러리인 `flutter_bloc`을 사용해서  
Counter 예제를 구현하는 방법을 정리해 보자.

---

## ✅ 1. 패키지 설치

`pubspec.yaml`에 아래 내용을 추가

```yaml
dependencies:
  flutter_bloc: ^8.1.3  # 버전은 최신으로 확인
```

## ✅ 2. 이벤트 정의

```dart
enum CounterEvent { increment, decrement }
```

## ✅ 3. `CounterState` 정의

```dart
class CounterState {
  final int count;

  const CounterState(this.count);
}

```
> 상태를 그냥 `int`로 해도 되지만, 확장성을 고려해 클래스로 정의한다.

## ✅ 4. `CounterBloc` 정의

```dart
import 'package:flutter_bloc/flutter_bloc.dart';

class CounterBloc extends Bloc<CounterEvent, CounterState> {
  CounterBloc() : super(const CounterState(0)) {
    on<CounterEvent>((event, emit) {
      if (event == CounterEvent.increment) {
        emit(CounterState(state.count + 1));
      } else if (event == CounterEvent.decrement) {
        emit(CounterState(state.count - 1));
      }
    });
  }
}
```

## ✅ 5. UI에서 사용
### 📦 5-1. `BlocProvider`로 `BLoC` 주입

```dart
return BlocProvider(
  create: (_) => CounterBloc(),
  child: MyHomePage(),
);

```
> 앱 전체에서 접근하고 싶다면 MaterialApp 위에 감싸면 됩니다.

### 📟 5-2. `BlocBuilder`로 상태 출력

```dart
BlocBuilder<CounterBloc, CounterState>(
  builder: (context, state) {
    return Text(
      state.count.toString(),
      style: TextStyle(fontSize: 32),
    );
  },
)
```

### 🕹️ 5-3. 이벤트 보내기 (버튼)

```dart
ElevatedButton(
  onPressed: () {
    context.read<CounterBloc>().add(CounterEvent.increment);
  },
  child: Text('증가'),
),

ElevatedButton(
  onPressed: () {
    context.read<CounterBloc>().add(CounterEvent.decrement);
  },
  child: Text('감소'),
),
```

## 🧠 구조 요약

| 구성요소       | 역할                         |
|----------------|------------------------------|
| `CounterEvent` | 사용자 액션 정의             |
| `CounterState` | 상태 정의 (`int` 값 포함)    |
| `CounterBloc`  | 이벤트 → 상태 로직 처리      |
| `BlocProvider` | BLoC 인스턴스 주입           |
| `BlocBuilder`  | 상태에 따른 UI 갱신          |
| `add()`        | 이벤트 전송 방식              |

---

## ✅ 마무리

`flutter_bloc` 패턴은 **복잡한 상태 관리도 예측 가능하게 만들어주며**,  
**테스트 가능성과 코드 분리**라는 장점을 갖고 있다.
