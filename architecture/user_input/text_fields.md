# 텍스트 필드\(Text fields\)

---

#### [코드를 복사](https://github.com/evancz/elm-architecture-tutorial/)하거나 [온라인 에디터](http://elm-lang.org/examples/field)에서 함께 따라해주세요

---

텍스트 필드의 내용을 반대로 뒤집는 간단한 앱을 만들 거에요. 이 예제는 배우는 새로운 것들은 다음 예제를 진행할 때도 도움이 될 거에요.

다시 매우 짧은 프로그램이에요. 그래서 모든 것들을 여기에 포함시켰어요. 전체적으로 어떻게 돌아가는 지 살펴보고, 그 후 각각에 대해서 자세히 알아 볼게요!

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

이번엔 모델을 레코드로 표현 했어요\(레코드에 대해서 더 자세히 알고 싶으시다면 [여기](http://guide.elm-lang.org/core_language.html#records)와 [여기](http://elm-lang.org/docs/records)를 참고하세요.\) 지금은 사용자 입력을, 레코드에 `content` 필드에 저장합니다. For now, the record stores the user input in the `content` field.

> **공지** **:** 왜 귀찮게 레코드에 하나의 필드만 사용하는 지 의아할 수도 있어요. 그냥 바로 문자열을 사용해도 되는 데 말이죠. 물론 그렇게도 가능하지만, 레코드로 시작하는 게 앱이 복잡해질 때 필드를 쉽게 추가 할 수 있답니다. 두 개의 텍스트 입력을 받게 될 때, 훨씬 적은 노력을 들여 수정할 수 있어요.

자 모델이 있으, 이 앱의 메시지는 딱 한 종류 밖에 없어요. 유저가 텍스트 필드의 내용을 변경하는 것 뿐이죠.\`\`\`\`\`\`\`

```elm
type Msg
  = Change String
```

This means our update function just has to handle this one case:

```elm
update : Msg -> Model -> Model
update msg model =
  case msg of
    Change newContent ->
      { model | content = newContent }
```

When we receive new content, we use the record update syntax to update the contents of `content`.

Finally we need to say how to view our application:

```elm
view : Model -> Html Msg
view model =
  div []
    [ input [ placeholder "Text to reverse", onInput Change ] []
    , div [] [ text (String.reverse model.content) ]
    ]
```

We create a `<div>` with two children.

The interesting child is the `<input>` node. In addition to the `placeholder` attribute, it uses `onInput` to declare what messages should be sent when the user types into this input.

This `onInput` function is kind of interesting. It takes one argument, in this case the `Change` function which was created when we declared the `Msg` type:

```elm
Change : String -> Msg
```

This function is used to tag whatever is currently in the text field. So let's say the text field currently holds `yol` and the user types `o`. This triggers an `input` event, so we will get the message `Change "yolo"` in our `update` function.

So now we have a simple text field that can reverse user input. Neat! Now on to putting a bunch of text fields together into a more traditional form.

