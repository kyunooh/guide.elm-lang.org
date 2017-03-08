# 랜덤\(Random\)

---

#### [코드를 클론 하세요](https://github.com/evancz/elm-architecture-tutorial/\) or follow along in the [online editor]\(http://elm-lang.org/examples/random).

---

이번엔 1에서 6까지 중 숫자 하나를 반환하는 "주사위 굴리기" 앱을 만들거에요.

제가 효과가 있는 코드를 작성할 땐, 보통 두 단계로 나누어서 작업해요. 처음엔 화면이 되는 최소한의 코드를, 그 다음엔 세부 사항들을 채워나가는 거죠. 예제를 이런 과정을 통해 작성해 볼게요.

## 첫번째 단계 - 최소한의 코드

항상 시작할때는 `Model` 을 고려하세요.

```elm
type alias Model =
  { dieFace : Int
  }
```

지금은 일단 `dieFace(주사위의 면)`을 1부터 6까지의 정수라고 생각할거에요. 그리고 나서 빠르게 `view` 함수의 뼈대를 잡고 맘 편하게 다음단계로 넘어갈게요.

```elm
view : Model -> Html Msg
view model =
  div []
    [ h1 [] [ text (toString model.dieFace) ]
    , button [ onClick Roll ] [ text "Roll" ]
    ]
```

Elm 아키텍처의 사용자 입력 단원 예제에서 본 전형적인 형태에요. `<button>` 클릭하면  `Roll` 메시지를 전달하고, `update` 함수에 전달되겠죠.

```elm
type Msg = Roll

update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    Roll ->
      (model, Cmd.none)
```

이 `update` 함수는 이전과 같은 형태로 보이지만, 반환 타입이 살짝 달라요. 이전엔 모델만 반환했어요. 하지만 지금은 `모델`과 `명령`, 둘 다를 반환하죠. "**모델을 반환할 건데, 거기에 '무엇'을 더 하고 싶어**"라고 생각하시면 되요. 이 예제의 경우엔, '무엇'이 Elm에게 랜덤값을 요청하는 거죠. 일단 [`Cmd.none`](https://www.gitbook.com/book/kyunooh/elm/edit#) 으로 채웠는데요. 이 의미는 "우리는 명령할 게 없어, 아무것도 하지마"를 뜻해요. 두번째 단계에서 '무엇'을 채울거에요.

마지막으로 아래와 같이 `init` 값을 만들거에요.

```elm
init : (Model, Cmd Msg)
init =
  (Model 1, Cmd.none)
```

앱이 시작될 때 즉시 실행될 때, 초기화 되어야 할 모델과 명령을 명시했어요. 이건 현재 `update`가 하고 있는 일과 거의 비슷해요.

지금까지 모든것을 연결될 수 있도록 하였어요. 동작이 않돼지만 `<button>`을 클릭할 수도 있죠. 자 그럼 동작하도록 바꿔 볼게요!

## 두번째 단계 - 동작 추가하기

랜덤에 관한 걸 놓치고 있었네요! 유저가 버튼을 클릭 했을 때, Elm에게 명령을 내려 1부터 6사이의 난수를 생성해야 해요. 그러기 위해 새로운 종류의 메시지를 추가할 거에요. 

```elm
type Msg
  = Roll
  | NewFace Int
```

We still have `Roll` from before, but now we add `NewFace` for when Elm hands us our new random number. That is enough to start filling in `update`:

```elm
update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    Roll ->
      (model, Random.generate NewFace (Random.int 1 6))

    NewFace newFace ->
      (Model newFace, Cmd.none)
```

There are two new things here. **First**, there is now a branch for `NewFace` messages. When a `NewFace` comes in, we just step the model forward and do nothing. **Second**, we have added a real command to the `Roll` branch. This uses a couple functions from [the `Random` library](http://package.elm-lang.org/packages/elm-lang/core/latest/Random). Most important is `Random.generate`:

```elm
Random.generate : (a -> msg) -> Random.Generator a -> Cmd msg
```

This function takes two arguments. The first is a function to tag random values. In our case we want to use `NewFace : Int -> Msg` to turn the random number into a message for our `update` function. The second argument is a "generator" which is like a recipe for producing certain types of random values. You can have generators for simple types like `Int` or `Float` or `Bool`, but also for fancy types like big custom records with lots of fields. In this case, we use one of the simplest generators:

```elm
Random.int : Int -> Int -> Random.Generator Int
```

You provide a lower and upper bound on the integer, and now you have a generator that produces integers in that range!

That is it. Now we can click and see the number flip to some new value!

So the big lessons here are:

* Write your programs bit by bit. Start with a simple skeleton, and gradually add the tougher stuff.
* The `update` function now produces a new model _and_ a command.
* You cannot just get random values willy-nilly. You create a command, and Elm will go do some work behind the scenes to provide it for you. In fact, any time our program needs to get unreliable values \(randomness, HTTP, file I/O, database reads, etc.\) you have to go through Elm.

At this point, the best way to improve your understanding of commands is just to see more of them! They will appear prominently with the `Http` and `WebSocket` libraries, so if you are feeling shaky, the only path forward is practicing with randomness and playing with other examples of commands!

> **Exercises:** Here are some that build on stuff that has already been introduced:
>
> * Instead of showing a number, show the die face as an image.
> * Add a second die and have them both roll at the same time.
>
> And here are some that require new skills:
>
> * Instead of showing an image of a die face, use the `elm-lang/svg` library to draw it yourself.
> * After you have learned about tasks and animation, have the die flip around randomly before they settle on a final value.



