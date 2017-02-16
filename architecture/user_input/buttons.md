# 버튼\(Buttons\)

---

#### [코드를 복사](https://github.com/evancz/elm-architecture-tutorial/)하거나 [온라인 에디터](http://elm-lang.org/examples/buttons)에서 함께 따라해주세요.

---

첫번째 예제는 증감이 가능한 간단한 카운터 앱이에요. 프로그램 전체를 한 곳에서 작성하는 게 도움이 된다는 걸 깨닫, 아래와 같이 작성했어요. 추후에는 나누어 봅시다.

```elm
import Html exposing (Html, button, div, text)
import Html.Events exposing (onClick)


main =
  Html.beginnerProgram { model = model, view = view, update = update }


-- MODEL

type alias Model = Int

model : Model
model =
  0


-- UPDATE

type Msg = Increment | Decrement

update : Msg -> Model -> Model
update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1


-- VIEW

view : Model -> Html Msg
view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (toString model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]
```

위 코드가 전부에요.

> **공지:** 이 단원은 `타입(type)`과 `타입 별칭(type alias)` 선언에 대한 내용을 담고 있어요. 이에 대해선 [타입\(types\)](http://guide.elm-lang.org/types/index.html)에서 더 자세히 다룰 거에요. 지금 당장 깊게 모든 것을 이해하려 하지 말고, 편안한 마음으로 부담없이 읽으세요.

프로그램 스케치를 작성 할 때, 전 모델\(model\)이 무엇인지 먼저 생각해봐요. 카운터를 만든다면, 일단 증가하고 감소하는 숫자를 관리해야겠죠. 자 그럼 시작하죠.

```elm
type alias Model = Int
```

자 이제 우리는 모델을 생겼으니, 시간이 지남에 따라 어떻게 바뀔지 정해야겠죠. 전 항상 UI에서 보일 메시지를 정의하고 `UPDATE` 부분을 시작해요.

```elm
type Msg = Increment | Decrement
```

전 사용자가 카운터를 증감시키는 게 가능하다는 걸 알고 있어요. `Msg` 타입은 이런 기능을 데이터로 표현할 수 있죠. 여기서 `update` 기능은 이러한 메시지중 하나를 받았을 때, 수행할 작업을 선택한다는 것이 중요해요.

```elm
update : Msg -> Model -> Model
update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1
```

만약 `Increment` 메시지를 받았다면 해당 모델은 증가할 것이고, `Decrement` 메시지를 받았다면 감소해요. 참 직관적이죠.

Okay, so that's all good, but how do we actually make some HTML and show it on screen? Elm has a library called `elm-lang/html` that gives you full access to HTML5 as normal Elm functions:

```elm
view : Model -> Html Msg
view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (toString model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]
```

You can see more examples of basic HTML [here](http://elm-lang.org/examples).

One thing to notice is that our `view` function is producing a `Html Msg` value. This means that it is a chunk of HTML that can produce `Msg` values. And when you look at the definition, you see the `onClick` attributes are set to give out `Increment` and `Decrement` values. These will get fed directly into our `update` function, driving our whole app forward.

Another thing to notice is that `div` and `button` are just normal Elm functions. These functions take \(1\) a list of attributes and \(2\) a list of child nodes. It is just HTML with slightly different syntax. Instead of having `<` and `>` everywhere, we have `[` and `]`. We have found that folks who can read HTML have a pretty easy time learning to read this variation. Okay, but why not have it be _exactly_ like HTML? **Since we are using normal Elm functions, we have the full power of the Elm programming language to help us build our views!** We can refactor repetitive code out into functions. We can put helpers in modules and import them just like any other code. We can use the same testing frameworks and libraries as any other Elm code. Everything that is nice about programming in Elm is 100% available to help you with your view. No need for a hacked together templating language!

There is also something a bit deeper going on here. **The view code is entirely declarative**. We take in a `Model` and produce some `Html`. That is it. There is no need to mutate the DOM manually, Elm takes care of that behind the scenes. This gives Elm [much more freedom to make clever optimizations](http://elm-lang.org/blog/blazing-fast-html) and ends up making rendering _faster_ overall. So you write less code and the code runs faster. The best kind of abstraction!

This pattern is the essence of The Elm Architecture. Every example we see from now on will be a slight variation on this basic pattern: `Model`, `update`, `view`.

> **Exercise:** One cool thing about The Elm Architecture is that it is super easy to extend as our product requirements change. Say your product manager has come up with this amazing "reset" feature. A new button that will reset the counter to zero.
>
> To add the feature you come back to the `Msg` type and add another possibility: `Reset`. You then move on to the `update` function and describe what happens when you get that message. Finally you add a button in your view.
>
> See if you can implement the "reset" feature!



