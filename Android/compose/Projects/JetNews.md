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
## 1. @OptIn(ExperimentalMaterial3Api::class, ExperimentalComposeUiApi::class)
#### ExperimentalMaterial3Api 
- Material 3 컴포넌트 일부가 아직 실험 단계. OutlinedTextField 자체는 Stable이지만, 내부 구현에 실험 API가 남아 있어 경고를 피하려면 opt-in 필요.


#### ExperimentalComposeUiApi
- `interceptKey` 확장 함수가 실험적 UI API에 속함. 이 키 이벤트 후킹 기능을 쓰려면 opt-in 해야 함.
