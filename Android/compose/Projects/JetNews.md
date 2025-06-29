# JetNews 유용한 코드 정리

> JetNews 프로젝트의 코드를 하나하나 살펴보고 학습중이다.
> HomeSearch 부분에 숙지해두면 유용하게 잘 사용될것 같아서 작성한다.

``` kotlin
@OptIn(ExperimentalMaterial3Api::class, ExperimentalComposeUiApi::class)
@Composable
private fun HomeSearch(
    modifier: Modifier = Modifier,
    searchInput: String = "",
    onSearchInputChanged: (String) -> Unit
) {
    val context = LocalContext.current
    val focusManager = LocalFocusManager.current
    val keyboardController = LocalSoftwareKeyboardController.current

    OutlinedTextField(
        value = searchInput,
        onValueChange = onSearchInputChanged,
        placeholder = { Text(stringResource(R.string.home_search)) },
        leadingIcon = { Icon(Icons.Filled.Search, null) },
        modifier = modifier
            .fillMaxWidth()
            .interceptKey(Key.Enter) {
                // submit a search query when Enter is pressed
                submitSearch(onSearchInputChanged, context)
                keyboardController?.hide()
                focusManager.clearFocus(force = true)
            },
        singleLine = true,
        // keyboardOptions change the newline key to a search key on the soft keyboard
        keyboardOptions = KeyboardOptions(imeAction = ImeAction.Search),
        // keyboardActions submits the search query when the search key is pressed
        keyboardActions = KeyboardActions(
            onSearch = {
                submitSearch(onSearchInputChanged, context)
                keyboardController?.hide()
            },
        ),
    )
}
```

--- 

## 1. @OptIn(ExperimentalMaterial3Api::class, ExperimentalComposeUiApi::class)
#### ExperimentalMaterial3Api 
- Material 3 컴포넌트 일부가 아직 실험 단계. OutlinedTextField 자체는 Stable이지만, 내부 구현에 실험 API가 남아 있어 경고를 피하려면 opt-in 필요.


#### ExperimentalComposeUiApi
- `interceptKey` 확장 함수가 실험적 UI API에 속함. 이 키 이벤트 후킹 기능을 쓰려면 opt-in 해야 함.


## 2. Composition Locals
> LocalContext.current
> - 토스트, Res 접근, Activity launch 등 안드로이드 레벨 작업에 필요.

> LocalFocusManager.current
> - 포커스 제거(키보드 내리기 등)

> LocalSoftwareKeyboardController.current
> - Show/Hide 소프트 키보드 제어


## 3. OutlinedTextField 속성별 해설
> `value` & `onValueChange`
> - value : `searchInput` 상태 바인딩
> - onValueChange : 매 키 입력마다 `onSearchInputChanged` 호출 -> ViewModel 업데이트 -> recomposition


> `placeholder`
> - 사용자가 아무 것도 입력하지 않았을 때 UX 메시지.
> - 로컬라이징은 `stringResource(R.string.home_search)`로 처리.

> `leadingIcon`
> - Meterial Icons `Search` 그림.
> - `contentDescription`을 null로 둔 것은 Decorative 역할

> `modifier`체인
> ``` kotlin
> modifier
>    .fillMaxWidth()
>    .interceptKey(Key.Enter) { ... }
> ```
>
> `fillMaxWidth()` : 부모 너비 가득 채움.
> `interceptKey(Key.Enter)` **하드키(Enter)**를 직접 훅킹. 아래 란다에서 검색 로직 실행.


> `interceptKey` 람다 내부
> 1. `submitSearch(onSearchInputChanged, context) - 현재 입력값으로 실제 검색 실행
> 2. `keyboardController?.hide()` - 소프트 키보드 숨김
> 3. `focusManager.clearFocus(force - true)` - 텍스트필드 포커스 해제.

> `singleLine = true`
> - 생략하면 TextField가 줄바꿈을 허용해 스크롤이 생김
> - 검색창은 한 줄 고정이므로 true.

> `ketboardOptions`
> ``` kotlin
> KeyboardOptions(imeAction = ImeAction.Search)
> ```
> SoftKeyboard의 확인/엔터 키를 🔍검색 아이콘으로 바꿔줌.
> 자연스럽게 검색 의도를 유도

> `keyboardActions`
> ``` kotlin
> KeyboardActions(
>     onSearch = { ... }
> )
> ```
> 
> `onSearch`
> - 사용자가 소프트 키보드의 "검색" 버튼 터치 시 호출
>
> 내부 구현
> - `submitSearch` -> 키보드 숨김. (`focusManager.clearFocus()`는 필요없음, `onSearch`후 자동 해제)
