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

This code is a slight variant of the counter from the previous section. You set up a model. You define some messages. You say how to `update`. You make your `view`. The difference is just in how we filled this skeleton in. Let's walk through that!

As always, you start by guessing at what your `Model` should be. In our case, we know we are going to have to keep track of whatever the user has typed into the text field. We need that information so we know how to render the reversed text.

```elm
type alias Model =
  { content : String
  }
```

This time I chose to represent the model as a record. \(You can read more about records [here](http://guide.elm-lang.org/core_language.html#records) and [here](http://elm-lang.org/docs/records).\) For now, the record stores the user input in the `content` field.

> **Note:** You may be wondering, why bother having a record if it only holds one entry? Couldn't you just use the string directly? Yes, of course! But starting with a record makes it easy to add more fields as our app gets more complicated. When the time comes where we want _two_ text inputs, we will have to do much less fiddling around.

Okay, so we have our model. Now in this app there is only one kind of message really. The user can change the contents of the text field.

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

