# 폼\(Forms\)

---

#### [코드를 복사](https://github.com/evancz/elm-architecture-tutorial/)하거나 [온라인 에디터](http://elm-lang.org/examples/form)에서 함께 따라해주세요.

---

여기에선 당신의 이름, 비밀번호, 비밀번호 확인을 위한 필드 등이 있는, 기본적인 폼을 만들어 볼 거에요. 가장 기본적인 기능인, 입력한 두 패스워드가 같은 지 확인하는 기능도 만들 거에요. 왜냐면 아주 쉽게 추가할 수 있으니까요.

이번 코드는 좀 더 길긴 하지만 여전히 보기 불편하진 않다고 생각하요. 이번에도 코드를 한번 쭉 보고, 설명으로 들어가봅시다.

```elm
import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (onInput)


main =
  Html.beginnerProgram { model = model, view = view, update = update }


-- MODEL

type alias Model =
  { name : String
  , password : String
  , passwordAgain : String
  }


model : Model
model =
  Model "" "" ""


-- UPDATE

type Msg
    = Name String
    | Password String
    | PasswordAgain String


update : Msg -> Model -> Model
update msg model =
  case msg of
    Name name ->
      { model | name = name }

    Password password ->
      { model | password = password }

    PasswordAgain password ->
      { model | passwordAgain = password }


-- VIEW

view : Model -> Html Msg
view model =
  div []
    [ input [ type_ "text", placeholder "Name", onInput Name ] []
    , input [ type_ "password", placeholder "Password", onInput Password ] []
    , input [ type_ "password", placeholder "Re-enter Password", onInput PasswordAgain ] []
    , viewValidation model
    ]

viewValidation : Model -> Html msg
viewValidation model =
  let
    (color, message) =
      if model.password == model.passwordAgain then
        ("green", "OK")
      else
        ("red", "Passwords do not match!")
  in
    div [ style [("color", color)] ] [ text message ]
```

필드가 좀 더 많아 진 것 빼곤 [텍스트 필드 예제](text_fields.md)와 비슷해 보이는데요. 어떻게 동작 되는지 살펴 볼게요!

언제나 처럼, `Model` 을 구상하는 거로 시작해요. 이번엔 세개의 필드가 있겠죠. 바로 시작하죠.

```elm
type alias Model =
  { name : String
  , password : String
  , passwordAgain : String
  }
```

잘 구성한 것 같군요. 각 필드는 개별적으로 변경 되기 때문에, 메시지도 각각 나누어 져요.

```elm
type Msg
    = Name String
    | Password String
    | PasswordAgain String
```

메시지가 각각 나누어져 있어서, `update` 도 기계적으로 구성되겠네요. 관련 필드별로 수정되게 끔 하죠.

```elm
update : Msg -> Model -> Model
update msg model =
  case msg of
    Name name ->
      { model | name = name }

    Password password ->
      { model | password = password }

    PasswordAgain password ->
      { model | passwordAgain = password }
```

일반적인 `view` 보단, 살짝 전문가 같아 보이는 걸 해봅시다.

```elm
view : Model -> Html Msg
view model =
  div []
    [ input [ type_ "text", placeholder "Name", onInput Name ] []
    , input [ type_ "password", placeholder "Password", onInput Password ] []
    , input [ type_ "password", placeholder "Re-enter Password", onInput PasswordAgain ] []
    , viewValidation model
    ]
```

처음엔 평범하게 시작하네요. `<div>`를 만들고, 몇개의 `<input>` 노드를 그 안에 만들어요. 각 노드는 `onInput` 속성을 갖고, 해당하는 `update` 함수를 설정해줘요. \(전부 이전 단원의 텍스트 필드 예제를 바탕으로 만들어 진 거에요.\)

하지만 마지막은 직접적으로 HTML 함수를 사용하지 않아요. 대신 현재의 모델이 전달되는 `viewValidation` 함수를 호출해요.

```elm
viewValidation : Model -> Html msg
viewValidation model =
  let
    (color, message) =
      if model.password == model.passwordAgain then
        ("green", "OK")
      else
        ("red", "Passwords do not match!")
  in
    div [ style [("color", color)] ] [ text message ]
```

이 함수는 두 비밀번호를 비교한 뒤, 매칭 된다면 초록색으로 가능하다는 메시지를, 매칭되지 않는다면 빨간색으로 도움이 되는 메시지를 만들어요. 그리고 그 정보로 화려한 메시지를 가진 `<div>`를 만들죠.

일반적인 Elm 코드로 이루어진 HTML 라이브러리의 장점이 보이기 시작하네요. 만약에 모든 코드를 `view` 에 쑤셔넣어다면 보기에 굉장히 이상했을 거에요. Elm에선 다른 코드들 처럼 리팩토링을 할 수 있답니다!

같은 줄에서, `<input>` 노드들은 전부 비슷하게 코드로 작성되어 있죠. 좀 더 멋있게 하고 싶다면, 바깥 쪽 `<div>`에 특정 class를 가진 `<span>`과 `<input>`을 담으면 되요. 이걸 또 viewInput 분해하여 만든다면, 반복할 필요도 없죠. 한곳을 수정하는 것 만으로 모든 HTML에 반영할 수 있게 되는 거에요.

> **연습하기:**  `viewValidation` 을 분리하는 것의 장점은 기능을 늘리는 것이 쉽다는 거에요. 만약 코드를 읽기 어렵게 짜고 있다는 생각이 들면, 코드를 분리하세요. \(꼭 하는 것이 좋습니다!\) 다음을 시도해 보세요.
>
> * 비밀번호가 8자리보다 긴 지 확인하세요.
> * 비밀번호에 대문자, 소문자 및 숫자가 포함되어 있는지 확인하세요.
> * `age` 필드를 추가하고 숫자인지 확인하세요.
> * "완료" 버튼을 만든 뒤, 버튼을 눌렀을 때만 error를 보여주도록 수정하세요.

위 기능을 구현하게 되면 [String 라이브러리](http://package.elm-lang.org/packages/elm-lang/core/latest/String)의 헬퍼를 사용해야해요! 또한 서버와의 통신을 배우기 전에, 더 많은 것으 밸워야 하기 때문에, HTTP 소개 전까지 계속 이 부분을 읽으세요. 그렇다면 앞으로도 훨씬 쉽게 이해가 될 거에요!

