# 📦 Flutter BLoC 패턴 완전 정리

BLoC(Business Logic Component) 패턴은 Flutter 애플리케이션에서 상태 관리를 위해 사용되는 디자인 패턴이다.  
UI와 비즈니스 로직을 분리하여 **유지보수성**과 **재사용성**을 높이기 위한 목적으로 널리 활용된다.

---

## 💡 BLoC 패턴의 구성 요소

### ✅ Event
- 사용자의 인터랙션이나 시스템 이벤트가 발생했을 때, **BLoC로 전달되는 트리거**
- 예: 버튼 클릭, 데이터 로드 요청 등

### ✅ BLoC
- **앱의 비즈니스 로직을 담당하는 중심 컴포넌트**
- 들어온 이벤트를 처리하고, 그 결과로 새로운 상태를 생성하여 UI 또는 다른 BLoC에 전달

### ✅ State
- BLoC에서 생성되어 **UI에 전달되는 데이터 상태**
- 화면에 어떤 내용을 보여줄지 결정하는 정보

---

## 🔁 BLoC 패턴 작동 원리

1. **이벤트 발생**  
   사용자 인터페이스에서 특정 액션이 발생하면, 관련된 이벤트가 BLoC로 전달됨  
   (예: 버튼 클릭, 스크롤 등)
   
```dart
class CounterBloc {
  final StreamController<CounterEvent> _eventController = StreamController<CounterEvent>();

  Sink<CounterEvent> get eventSink => _eventController.sink;

  CounterBloc() {
    _eventController.stream.listen(
      (event) {
        switch (event) {
          case CounterEvent.increment:
            // TODO: Handle this case.
            break;
          case CounterEvent.decrement:
            // TODO: Handle this case.
            break;
        }
      },
    );
  }
}

```


2. **비즈니스 로직 처리**  
   BLoC는 이벤트를 기반으로 로직을 수행  
   (예: 외부 API 호출, 로컬 DB 접근, 데이터 가공 등)

```dart
class CounterBloc {
  int _counter = 0;

  final StreamController<CounterEvent> _eventController = StreamController<CounterEvent>();

  Sink<CounterEvent> get eventSink => _eventController.sink;

  CounterBloc() {
    _eventController.stream.listen(
      (event) {
        switch (event) {
          case CounterEvent.increment:
            _counter++;
            break;
          case CounterEvent.decrement:
            _counter--;
            break;
        }
      },
    );
  }
}

```

3. **상태 생성**  
   로직 처리 결과를 바탕으로 새로운 상태를 생성

```dart
class CounterBloc {
  int _counter = 0;

  final StreamController<CounterEvent> _eventController = StreamController<CounterEvent>();
  Sink<CounterEvent> get eventSink => _eventController.sink;

  final StreamController<int> _stateController = StreamController<int>();
  Stream<int> get stateStream => _stateController.stream;

  CounterBloc() {
    _eventController.stream.listen(
      (event) {
        switch (event) {
          case CounterEvent.increment:
            _counter++;
            break;
          case CounterEvent.decrement:
            _counter--;
            break;
        }

        // 상태 업데이트
        _stateController.sink.add(_counter);
      },
    );
  }
}

```

4. **상태 전달**  
   생성된 상태가 UI로 전달되어 화면이 갱신됨

```dart
class CounterBloc {
  int _counter = 0;

  final StreamController<CounterEvent> _eventController = StreamController<CounterEvent>();
  Sink<CounterEvent> get eventSink => _eventController.sink;

  final StreamController<int> _stateController = StreamController<int>();
  Stream<int> get stateStream => _stateController.stream;

  CounterBloc() {
    _eventController.stream.listen((event) {
      switch (event) {
        case CounterEvent.increment:
          _counter++;
          break;
        case CounterEvent.decrement:
          _counter--;
          break;
      }

      // 변경된 상태를 외부로 전달
      _stateController.sink.add(_counter);
    });
  }
}
   
```

```dart
class CounterBloc {
  int _counter = 0;

  final StreamController<CounterEvent> _eventController = StreamController<CounterEvent>();
  Sink<CounterEvent> get eventSink => _eventController.sink;

  final StreamController<int> _stateController = StreamController<int>();
  Stream<int> get stateStream => _stateController.stream;

  CounterBloc() {
    _eventController.stream.listen((event) {
      switch (event) {
        case CounterEvent.increment:
          _counter++;
          break;
        case CounterEvent.decrement:
          _counter--;
          break;
      }

      _stateController.sink.add(_counter); // 상태 스트림으로 값 전달 **
    });
  }

  void dispose() {
    _eventController.close();
    _stateController.close();
  }
}

// 여기로 전달
StreamBuilder<int>(
  stream: bloc.stateStream,
  builder: (BuildContext context, AsyncSnapshot<int> snapshot) {
    final int count = snapshot.data ?? 0;
    return Text(count.toString());
  },
)
```

---

## ✅ BLoC 패턴의 장점

### 📍 비즈니스 로직과 UI 분리
- UI와 로직을 독립적으로 유지할 수 있음
- 코드의 재사용성과 유지보수가 쉬움

### 📍 테스트 용이성
- UI에 의존하지 않고 비즈니스 로직만 독립적으로 테스트 가능
- 안정성 높은 앱 구축에 도움

### 📍 상태 관리의 일관성
- 모든 상태 변경이 BLoC를 통해 이뤄지므로 일관성 유지
- 예측 가능성과 신뢰성 향상

---

## ⚠️ BLoC 패턴의 단점

### ❗ 학습 곡선
- Stream, async 등 비동기 프로그래밍에 대한 이해가 필요
- 초보자에게는 진입장벽이 존재

### ❗ 보일러플레이트 코드
- 이벤트, 상태, BLoC 클래스를 매번 정의해야 함
- 코드량이 많아지고 가독성이 떨어질 수 있음

### ❗ 상태 관리의 복잡성
- 대규모 앱에서 BLoC 간 상호작용이 복잡해질 수 있음
- 아키텍처 설계가 중요해짐

---

BLoC는 복잡한 앱 구조에서 **상태 관리의 일관성**을 확보할 수 있는 강력한 방법이지만,  
복잡성과 코드량 증가의 단점도 있으므로 **규모에 맞게 선택하는 유연함**이 중요하다.
