# 🔥 Kotlin Flow 완전정복: Cold Flow vs Hot Flow 차이와 활용법

Kotlin Flow를 처음 접하면 `Cold Flow`와 `Hot Flow`의 차이가 가장 큰 헷갈림 중 하나다. 이번 포스팅에서는 그 차이를 직관적으로 설명하고, 실제로 어떤 상황에서 무엇을 써야 하는지도 함께 정리해본다.

---

## ✅ Cold Flow

- **collect 하기 전에는 아무 값도 emit 하지 않음**
- **collect 할 때마다 처음부터 다시 시작됨**
- 기본 `flow {}`, `flowOf()` 등은 모두 Cold Flow

### 예시

```kotlin
fun coldFlow(): Flow<Int> = flow {
    println("Flow 시작")
    emit(1)
    emit(2)
}

// 두 번 collect하면 "Flow 시작"이 두 번 출력됨
coldFlow().collect { println("A: $it") }
coldFlow().collect { println("B: $it") }
```

## ✅ Hot Flow
- collect 하지 않아도 값을 emit 할 수 있음

- 중간부터 collect 해도 최근 값이나 replay된 값을 받을 수 있음

- 대표적인 예: SharedFlow, StateFlow

## 🔄 SharedFlow
- Hot & Multicast: 여러 구독자가 동시에 값을 받을 수 있음

- replay, extraBufferCapacity, drop 정책 등 설정 가능

- Navigation, Toast, 단발성 이벤트에 적합


### 예시
```kotlin
val sharedFlow = MutableSharedFlow<Int>(replay = 1)

scope.launch {
    sharedFlow.collect { println("수신자 A: $it") }
}

sharedFlow.emit(100) // 수신자 A가 받음

```

## 🟢 StateFlow
- Hot & 상태 보관용

- 항상 최신 값을 보관하고, collect 시 최신 값부터 수신

- UI 상태 관리에 매우 적합

### 예시
```kotlin
val state = MutableStateFlow(0)

state.value = 10
state.collect { println("UI 상태: $it") } // 10부터 출력

```

## 🔁 stateIn
- Cold Flow를 Hot한 StateFlow로 변환

- viewModelScope 등의 CoroutineScope 안에서 사용해야 함

- Flow를 UI에 바인딩할 때 자주 사용

``` kotlin
val coldFlow = flow {
    emit(1)
    delay(1000)
    emit(2)
}

val hotStateFlow = coldFlow.stateIn(
    scope = viewModelScope,
    started = SharingStarted.WhileSubscribed(),
    initialValue = 0
)

```

## ⚡ collectLatest
이전 collect 블록을 취소하고 새 값만 처리

빠르게 연속 emit 되는 값 중, 마지막 값만 처리하고 싶을 때 사용

### 예시
```kotlin
flowOf(1, 2, 3).collectLatest { value ->
    delay(100) // 오래 걸리는 작업
    println("수신: $value")
}

// 출력: 수신: 3 (1, 2는 취소됨)

```

## ⏱ debounce
일정 시간 동안 값이 변경되지 않았을 때만 emit

검색어 입력창 등에서 자주 사용됨

### 예시
``` kotlin
searchInput
    .debounce(300)
    .collectLatest { query -> search(query) }
```

## 📌 언제 뭘 써야 할까?

| 상황 | 추천 도구 |
|------|-----------|
| UI 상태를 화면에 유지하고 최신값을 보이게 하고 싶을 때 | `StateFlow`, `stateIn` |
| Toast, Navigation 등 일회성 이벤트를 전달할 때 | `SharedFlow` |
| 사용자의 입력이 자주 발생하는 경우 (예: 검색창 입력) | `debounce`, `collectLatest` |
| 여러 View나 컴포넌트에서 하나의 Flow를 공유해서 쓰고 싶을 때 | `stateIn`, `shareIn` |
| 이전 작업은 취소하고 가장 마지막 emit만 처리하고 싶을 때 | `collectLatest` |

---

## ✅ 정리 요약

- `Cold Flow`는 `collect`할 때마다 처음부터 다시 실행되는 일회성 스트림
- `Hot Flow`는 구독 여부와 상관없이 **계속 흐름이 유지되는 스트림**
- `StateFlow`는 **상태 보관용**, `SharedFlow`는 **이벤트 전달용**
- `stateIn`, `shareIn`을 활용하면 `Cold Flow`도 상태처럼 공유 가능
- 실시간 입력 처리나 UI 반응형 처리에는 `collectLatest`, `debounce`를 적극 활용

---


# Kotlin Flow 사용 목적과 활용 시점 정리


## ✅ 왜 Flow를 사용하는가? (사용 이유 3가지)

1. **비동기 스트림 처리**
   - 데이터를 순차적 또는 지연된 방식으로 처리 가능
   - 예: API 응답을 순서대로 처리, 지연된 계산 등

2. **반응형 UI 구성**
   - UI는 상태(state)에 반응해야 함
   - `collectAsState()`를 통해 UI와 상태를 자연스럽게 연동 가능

3. **연산자 기반 데이터 흐름 처리**
   - `map`, `filter`, `combine`, `flatMapLatest` 등 다양한 연산자로 스트림 조작 가능
   - RxJava의 대체제로 충분한 기능성 제공

---

## 🧊 Cold Flow: 언제 사용하는 것이 적합한가?

| 상황 | 설명 |
|------|------|
| 매번 새로운 작업이 필요한 경우 | collect할 때마다 새로 시작되기 때문에, "수동 호출 → 새 작업" 구조에 적합 |
| API 1 → API 2 → API 3 순차 호출 | 중간 계산 결과를 연결해 나가는 로직 구성에 유리 |
| 일시적인 처리 흐름이 필요할 때 | 화면 진입 시 필요한 데이터만 흐르게 하고 싶을 때 |

**예시**
```kotlin
fun loadUserInfo(): Flow<User> = flow {
    val profile = api.fetchProfile()
    val posts = api.fetchPosts(profile.id)
    emit(User(profile, posts))
}
```

## 🔥 Hot Flow: 언제 사용하는 것이 적합한가?

| 상황                         | 설명 |
|------------------------------|------|
| 상태를 계속 유지해야 할 때  | `StateFlow`는 항상 최신 값을 보존하며 구독자에게 전달함 |
| 이벤트 기반 시스템 구성     | `SharedFlow`는 이벤트(Toast, Navigation 등) 전달에 적합함 |
| 여러 구독자가 동시에 데이터를 받아야 할 때 | `SharedFlow`, `stateIn`, `shareIn` 등을 통해 스트림을 공유 가능 |
| ViewModel에서 Compose UI와 상태 연동 시 | UI 상태를 `StateFlow`로 유지하면 자연스럽게 구독하여 반응형 UI 구성 가능 |

### ✅ 예시: StateFlow

```kotlin
val uiState = MutableStateFlow(LoginUiState())

fun onEmailChanged(email: String) {
    uiState.value = uiState.value.copy(email = email)
}
```

### ✅ 예시: SharedFlow
``` kotlin
val toastEvent = MutableSharedFlow<String>()

fun showToastMessage(msg: String) {
    viewModelScope.launch {
        toastEvent.emit(msg)
    }
}

```

## 🎯 결론 요약

| Flow 종류     | 용도                       | 예시                                    |
|---------------|----------------------------|------------------------------------------|
| `Cold Flow`   | 요청마다 새 흐름이 필요할 때 | API 요청 시마다 새 데이터 필요할 경우     |
| `StateFlow`   | 상태를 저장하고 UI에 반영해야 할 때 | 입력 폼 상태, 로딩 상태 유지 등         |
| `SharedFlow`  | 일회성 이벤트 전파          | Toast 메시지, 화면 전환 트리거 등        |
| `stateIn`     | Cold Flow → Hot 상태로 공유 | 여러 구독자에게 동일한 상태를 보여줄 때  |
| `collectLatest` | 빠른 값 변경 중 마지막 것만 처리 | 검색창, 자동완성 등 입력 지연 처리에 적합 |
