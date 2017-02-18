# 텍스트 필드\(Text fields\)

---

#### [코드를 복사](https://github.com/evancz/elm-architecture-tutorial/)하거나 [온라인 에디터](http://elm-lang.org/examples/field)에서 함께 따라해주세요

---

텍스트 필드의 내용을 반대로 뒤집는 간단한 앱을 만들 거에요. 이 예제에서 배우는 새로운 것들은 다음 예제를 진행할 때도 도움이 될 거에요.

또 한번 매우 짧은 프로그램이에요. 그래서 모든 것들을 한 곳에 포함시켰어요. 전체적으로 어떻게 돌아가는 지 살펴보시고, 그 후 각각에 대해서 자세히 알아 볼게요!

```elm
import Html exposing (Html, Attribute, div, input, text)
import Html.Attributes exposing (..)
import Html.Events exposing (onInput)
import String


main =
  Html.beginnerProgram { model = model, view = view, update = update }


-- MODEL

type alias Model =
  { content : String
  }

model : Model
model =
  { content = "" }


-- UPDATE

type Msg
  = Change String

update : Msg -> Model -> Model
update msg model =
  case msg of
    Change newContent ->
      { model | content = newContent }


-- VIEW

view : Model -> Html Msg
view model =
  div []
    [ input [ placeholder "Text to reverse", onInput Change ] []
    , div [] [ text (String.reverse model.content) ]
    ]
```

이 코드는 이전 단원의 카운터를 약간 변형한 거에요. 모델을 설정하는 것과, 메시지를 정의하고 어떻게 변경\(`update`\) 할 지, `view`를 만드는 것까지 똑같죠. 차이점이라면 '어떻게 이 구조를 채워나갈 것인가' 뿐이네요. 자 시작해봅시다!

언제나처럼, 모델\(`Model`\)이 무엇인지 생각하는 걸로 시작해야 해요. 이 예제에선, 사용자가 텍스트 필드에 입력한 내용들을 관리해야 한다는 사실을 알고 있죠. 이 정보는 뒤집혀진 텍스트를 렌더링하기 위해 필요하니까요.

```elm
type alias Model =
  { content : String
  }
```

이번엔 모델을 레코드로 표현 했어요\(레코드에 대해서 더 자세히 알고 싶으시다면 [여기](http://guide.elm-lang.org/core_language.html#records)와 [여기](http://elm-lang.org/docs/records)를 참고하세요.\) 지금은 사용자 입력을, 레코드에 `content` 필드에 저장합니다.

> **공지** **:** 왜 귀찮게 레코드에 하나의 필드만 사용하는 지 의아할 수도 있어요. 그냥 바로 문자열을 사용해도 되는 데 말이죠. 물론 그렇게도 가능하지만, 레코드로 시작하는 건,  앱이 복잡해질 때 필드를 쉽게 추가 할 수 있답니다. 두 개의 텍스트 입력을 받게 될 때, 훨씬 적은 노력을 들여 수정할 수 있어요.

자 이제 모델이 있으니, 메시지를 선언하죠. 이 앱의 메시지는 딱 한 종류 밖에 없어요. 유저가 텍스트 필드의 내용을 변경하는 것 뿐이죠.

```elm
type Msg
  = Change String
```

이건  update 함수도 그저 한가지 경우만 다룰거라는 걸 의미해요.

```elm
update : Msg -> Model -> Model
update msg model =
  case msg of
    Change newContent ->
      { model | content = newContent }
```

새로운 내용을 받았을 때, 레코드를 `content`의 내용을 새로운 내용으로 바꿀 거에요.

마지막으로 `view`를 작성해야 되요.

```elm
view : Model -> Html Msg
view model =
  div []
    [ input [ placeholder "Text to reverse", onInput Change ] []
    , div [] [ text (String.reverse model.content) ]
    ]
```

두개의 자식을 갖는 `<div>` 를 만들어요.

자식 중 `<input>` 노드가 재밌는데요. `placeholder` 속성을 집어 넣는 것 외에도,  `onInput` 속성에 어떤 메시지가 전달되어야 할지도 선언하고 있죠.

이 `onInput` 함수도 흥미로운데요. 하나의 인자를 받아서 전달하는데요. 이 경우엔 Msg 타입을 선언할 때 만든`Change` 함수가 필요해요.

```elm
Change : String -> Msg
```

이 함수는 현재 텍스트 필드에 있는 내용을 태그하는 데 사용되요. 현재 텍스트 필드에 `yol` 이 있는 상태에서, `o` 를 입력할 때 `input` 이벤트가 발생하므로, 이 트리거를 통해`Change "yolo"` 메시지를 `update` 함수에서 받을 수 있죠.

유저 입력을 뒤집는 간단한 텍스트 필드를 만들어 보았는데요. 자 이제 더 많은 텍스트 필드를 좀 더 전통적인 폼 안에 넣어볼게요.

