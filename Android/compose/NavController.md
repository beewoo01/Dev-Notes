# NavController와 NavHostController의 차이점

## 개요

Jetnews App을 클론 코딩하는 중 `NavController` 관련 오류가 발생했다.  
한참 바라보며 고민하고, ChatGPT, StackOverflow, Google을 뒤져봤지만  
결정적인 해결책은 찾지 못했다.

코드를 하나하나 자세히 뜯어보던 중,  
`NavHostController`를 사용해야 하는 곳에 `NavController`를 사용하고 있었다는 걸 발견했다.

아래는 문제가 되었던 코드이다.

```kotlin
fun JetnewsNavGraph(
    modifier: Modifier = Modifier,
    navController: NavController = rememberNavController(), // ❌
    openDrawer: () -> Unit = {},
    startDestination: String = JetnewsDestinations.HOME_ROUTE
) {
    NavHost(
        navController = navController, // 타입 불일치
        startDestination = startDestination,
        modifier = modifier,
    )
}

// Error Message: None of the following candidates is applicable
```

## 공식 문서의 코드 예시
아래는 Jetpack Compose 공식 문서에 나오는 NavHost 함수 정의 예시이다.

```kotlin
@Composable
fun AppNavHost(navController: NavHostController){
    NavHost(navController = navController) { ... }
}
```

또한 실제 NavHost 함수의 정의를 보면 다음과 같이 되어 있다.
```kotlin
@Composable
fun NavHost(
    navController: NavHostController, // 👈 여기!
    startDestination: String,
    modifier: Modifier = Modifier,
    contentAlignment: Alignment = Alignment.TopStart,
    route: String? = null,
    ...
)
```
즉, NavHost는 반드시 NavHostController 타입을 받아야 한다.
그런데 나는 NavController로 선언했으니 타입이 일치하지 않았고, 이로 인해 오버로딩 선택이 불가능해져 컴파일 오류가 발생한 것이다.

|항목|NavController|NavHostController|
|---|--------------|----------------|
|정의|추상 클래스 (공통 API)|구현 클래스 (Compose 전용)|
| 사용 가능 환경 | Compose + XML 모두 가능 | Compose전용|
|반환 방식| 업케스팅 가능 | rememberNavController() 로 생성 |

## 왜 타입이 중요한가?

Kotlin에서는 함수 오버로딩 시, 전달하는 파라미터의 **정확한 타입**이 어떤 시그니처에 해당하는지를 기준으로 함수를 선택한다.  
이때, `NavHost()`는 `NavHostController` 타입을 인자로 받는 함수만 제공하고 있기 때문에, `NavController`를 넘기면 타입이 일치하지 않아 **컴파일 오류가 발생**한다.

예를 들어:

```kotlin
val navController: NavController = rememberNavController()
NavHost(navController = navController, ...) // ❌ 오류 발생
```
위 코드는 rememberNavController()가 실제로는 NavHostController를 반환하지만, 변수의 타입이 NavController로 선언되어 있기 때문에 NavHost()와 일치하지 않아 컴파일이 실패한다.

반면 아래처럼 선언하면 정상적으로 작동한다:

```kotlin
val navController: NavHostController = rememberNavController()
NavHost(navController = navController, ...) // ✅ 정상 작동
```
즉, 타입 명시만 바꿔줘도 오류가 사라지고 기능이 올바르게 작동하게 된다.

## 역할 정리

- **NavController**  
  `NavController`는 Navigation 기능을 정의한 추상 인터페이스로,  
  XML 기반 뷰 시스템과 Jetpack Compose 양쪽 모두에서 공통적으로 사용될 수 있다.  
  기본적인 탐색 기능인 `navigate()`, `popBackStack()` 등을 제공한다.

- **NavHostController**  
  `NavHostController`는 `NavController`를 구현한 **Jetpack Compose 전용 클래스**다.  
  Compose에서 `NavHost()` 컴포저블과 연결될 때 반드시 이 타입이 요구되며,  
  Compose 내부 상태나 백스택 관리를 위한 기능이 포함돼 있다.

- **rememberNavController()**  
  이 함수는 내부적으로 `NavHostController`를 생성해 반환한다.  
  따라서 타입을 명시할 때는 `NavHostController`로 선언하는 것이 오류를 예방하는 가장 안전한 방법이다.

---

💡 요약하면, Compose에서 Navigation을 구성할 때는 **항상 `NavHostController`로 타입을 명시하자.**  
이는 `NavHost()` 함수와의 타입 일치를 보장하며, 오버로딩 오류 없이 안전하게 동작한다.

## 결론

- `NavController`는 Navigation 기능을 위한 추상 타입으로, Compose와 XML 모두에서 사용할 수 있다.
- `NavHostController`는 `NavController`의 구현체이며, Jetpack Compose 전용으로 사용된다.
- `NavHost()`는 오직 `NavHostController` 타입만 인자로 받을 수 있으므로, 정확한 타입 선언이 필수이다.
- `rememberNavController()`는 내부적으로 `NavHostController`를 반환하므로, **타입을 명시하지 않으면 컴파일 오류가 발생할 수 있다**.
- Compose에서 Navigation을 사용할 땐 **무조건 `NavHostController`를 명시적으로 사용할 것.**

---

## 💡 실무 팁

- `rememberNavController()`의 반환값을 무심코 `NavController`로 선언하지 말고, **명확하게 `NavHostController`로 선언**하자.
  ```kotlin
  val navController: NavHostController = rememberNavController() // ✅ 올바른 방식
  
- `NavHost()` 컴포저블에 넘기는 컨트롤러는 반드시 `NavHostController`여야만 작동한다.  
  `NavController`로 넘기면 타입이 맞지 않아 **컴파일 에러**가 발생한다.

- 에러 메시지가  
  `"None of the following candidates is applicable"`  
  처럼 오버로드 관련 오류라면,  
  **타입 불일치**를 의심해보고 `NavController` → `NavHostController` 변경 여부를 점검하자.

- 타입은 맞는데도 `composable` 등이 unresolved 에러가 난다면,  
  `navigation-compose` 라이브러리 의존성과 `import` 구문을 반드시 확인하자.
  ```kotlin
  implementation("androidx.navigation:navigation-compose:2.9.0")

  // import 확인
  import androidx.navigation.compose.composable
  ```
