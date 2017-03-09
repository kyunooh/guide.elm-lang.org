# 랜덤\(Random\)

---

#### [코드를 클론](https://github.com/evancz/elm-architecture-tutorial/)하거나 [온라인 에디터](http://elm-lang.org/examples/random)에서 함께 따라해주세요.

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

`Roll`은 그대로 두고, 난수를 받을 때 사용할 `NewFace`를 추가했어요. 이것만으로 `update`를 채우기 위한 준비는 끝났어요.

```elm
update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    Roll ->
      (model, Random.generate NewFace (Random.int 1 6))

    NewFace newFace ->
      (Model newFace, Cmd.none)
```

두 가지 새로운 게 보이는데요. **첫번째는** `NewFace` 메시지 부분,** 두번째는** 이제 진짜 `Roll` 명령어를 추가했다는 거죠. [`Random` 라이브러리](http://package.elm-lang.org/packages/elm-lang/core/latest/Random)에서 두가지 함수를 사용했는데, 가장 중요한 것은 `Random.generate` 이에요.

```elm
Random.generate : (a -> msg) -> Random.Generator a -> Cmd msg
```

이 함수는 두개의 인자를 받아요. 첫번째 인자는 랜덤 값의 태그를 지정하는 것인데요. 위 경우에선 `NewFace : Int -> Msg` 를 사용하여, 난수를 `update` 함수에 전달했어요. 두번째 인자는 특정 유형의 임의 값을 지정하는 "생성자" 인데요. `Int`, `Float`, `Bool` 과 같은 간단한 타입을 사용할 수도 있지만, 필드가 많은 레코드에서도 사용할 수 있어요. 이 예제에서는 간단한 생성자를 사용하였어요.

```elm
Random.int : Int -> Int -> Random.Generator Int
```

정수에 상한과 하한을 정한 뒤, 자 이제 지정된 범위의 생성자를 갖게 되었습니다.

이제 모두 끝났어요. 버튼을 클릭해보시고 새로운 값이 나오는 지 확인해 보세요!

이 단원에서 중요한 교훈이 있어요.

* 프로그램을 조금씩 조금씩 작성하세요. 일단 간단한 골격을 잡고, 점진적으로 발전시키세요.
* `update` 함수에서 새로운 모델과 명령을 만들수 있어요. 
* 그냥 랜덤 값을 얻을 순 없어요. 명령을 만들면, Elm은 안보이는 곳에서 그것을 처리하여 제공해요. 사실, 프로그램에서 신뢰할 수 없는 값\(랜덤 값, HTTP, file I/O, 데이터베이스 읽기 등\)은 Elm을 통해서 처리하게 돼요.

이 시점에서, 명령에 대한 이해를 향상시키는 가장 좋은 방법은 예제들을 더 많이 보는거에요!  HTTP 와 웹소켓 라이브러리에서 많이 사용되어지는 데요. 만약 어렵게 느껴지신다면, 랜덤과 같이 명령에 관한 더 많은 예제를 통해 연습해보시는 게 좋아요!

> **연습문제:** 이미 소개 된 것들을 바탕으로 다음을 진행해보세요.
>
> * 숫자를 보여주는 대신 이미지를 이용해 주사위의 면을 표시해 보세요.
> * 주사위를 하나 더 추가해서, 두 주사위를 동시에 굴릴 수 있도록 해보세요
>
> 다음은 새로운 기술이 필요해요.
>
> * 이미지로 표현하는 대신 `elm-lang/svg` 라이브러리를 통해 직접 그려보세요.
> * 작업과 애니메이션을 배운 뒤에, 최종 값을 보여주기 전에 아무렇게나 빙글 돌도록 해보세요.



