# 버튼\(Buttons\)

---

#### [코드를 복사](https://github.com/evancz/elm-architecture-tutorial/)하거나 [온라인 에디터](http://elm-lang.org/examples/buttons)에서 함께 따라해주세요.

---

첫번째 예제는 증감이 가능한 간단한 카운터 앱이에요. 프로그램 전체를 한 곳에서 작성하는 게 도움이 된다는 걸 깨닫고, 아래와 같이 작성했어요. 추후에는 나누어 봅시다.

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

> **공지:** 이 단원은 `타입(type)`과 `타입 별칭(type alias)` 선언에 대한 내용을 담고 있어요. 이건 [타입\(types\)](http://guide.elm-lang.org/types/index.html)에서 더 자세히 다룰 거에요. 지금 당장 깊게 모든 것을 이해하려 하지 말고, 편안한 마음으로 부담없이 읽으세요.

프로그램의 스케치를 할 때, 전 모델\(model\)이 무엇인지 먼저 생각해봐요. 카운터를 만든다면, 일단 증가하고 감소하는 숫자를 관리해야겠죠. 자 그럼 시작해보죠.

```elm
type alias Model = Int
```

자 이제 우리는 모델을 생겼으니, 시간이 지남에 따라 어떻게 바뀔지 정해야겠죠. 전 항상 UI에서 보일 메시지를 정의한 뒤 `UPDATE` 부분을 시작해요.

```elm
type Msg = Increment | Decrement
```

전 사용자가 카운터를 증감시키는 게 가능하다는 걸 알고 있어요. `Msg` 타입은 이런 기능을 데이터로 표현할 수 있죠. 여기서 `update` 는 이러한 메시지중 하나를 받았을 때, 수행할 작업을 선택한다는 걸 기억하세요.

```elm
update : Msg -> Model -> Model
update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1
```

`Increment` 메시지를 받았다면 해당 모델은 증가할 것이고, `Decrement` 메시지를 받았다면 감소해요. 참 직관적이죠?

자 모든 게 좋아 보입니다만, 화면에 보여 줄 HTML은 어떻게 만들어야 할까요? Elm엔 `elm-lang/html` 이라고 하는 라이브러리가 있어서, 대부분의 HTML5를 Elm 함수로 접근할 수 있어요.

```elm
view : Model -> Html Msg
view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (toString model) ]
    , button [ onClick Increment ] [ text "+" ] 
    ]
```

[여기](http://elm-lang.org/examples)에서 기본적인 HTML을 확인 할 수 있어요.

중요한 것은 `view` 함수가 `Html Msg` 값을 만들어준다는 것이에요.즉 `Msg`값들을 HTML 코드들로 생성해낸다는 것이에요. 함수를 보시면, `onClick` 속성\(attributes\)에 `Increment`, `Decrement` 값을 넘겨주고 있죠. 이건 직접 `update` 함수에 반영되어, 앱 전체를 변경해요.

한 가지 더 말씀드리자면, `div`와 `button`은 그냥 일반적인 Elm 함수에요. 이 함수는 첫번째\(1\) 인자의 리스트를  속성으로 사용하고 두번째\(2\) 인자의 리스트를 자식 노드\(child node\)로 받아요. 그냥 조금 다른 문법의 HTML일 뿐이에요. 어디에나 &lt; 와 &gt; 를 사용하는 대신 \[ 와 \] 를 사용하죠. 기존의 HTML을 읽던 사람들은 이 형태도 쉽게 익숙해진다는 걸 발견했어요. 하지만 왜 그냥 HTML과 같은 걸 사용하지 않았을까요? **일반적인 Elm 함수를 사용함으로써, Elm 프로그래밍 언어가 가진 모든 능력을 사용하여 **`views`**를 만드는 데 도움이 된다는 걸 알았죠! **반복적인 함수를 코드로 리팩토링할 수 있고, 헬퍼\(helpers\)와 모듈을 다른 코드처럼 집어넣어서 사용할 수 있고, 동일한 테스팅 프레임워크와 라이브러리를 다른 Elm 코드 처럼 사용할 수 있었죠. Elm으로 전부 프로그래밍 하는 것이 `view` 를 만드는 데 굉장히 좋답니다. 템플릿 언어\(template language\)를 사용할 필요도 없죠!

좀 더 깊게 들어가 본다면, **view** **코드는 자연스러운 영어 문장처럼 작성하고,** Model에서 Html을 제공하는 것 뿐이에요. DOM을 직접 제어할 필요가 없고,  Elm은 뒤쪽에서 이런 과정들을 처리하는 거죠. 이건 Elm이 [뛰어난 죄적화를 더 자유롭게 할수있도록 해주고](http://elm-lang.org/blog/blazing-fast-html), 전체 렌더링 속도를 빠르게 해요. 그리하여 적은 코드로 빠르게 실행하도록 해주죠. 이건 최고의 추상화라고 생각해요

이 패턴은 Elm 아키텍처의 핵심 패턴이에요. 모든 예제들에서 기본 패턴인 `Model`, `update`, `view` 를 사용한 조합을 볼 수 있을 거에요.

> **연습 문제:** Elm 아키텍처의 좋은 점중 하나는 요구사항을 반영하여 확장하는 게 엄청나게 쉽다는 거에요. 만약에 PM\(Product Manager\)이 우리에게 놀라운 "초기화" 기능을 만들라고 가정해보세요, 카운터를 0으로 초기화 하는 버튼이 필요해요.
>
> 이 기능을 추가하려면 `Msg` 타입으로 돌아가서 `Reset` 을 추가해요. 그리고 `update` 함수에서 그 메시지를 받았을때 어떻게 처리해야할 지 작성합시다. 마지막으로 버튼을 `view` 에 추가해주면 되요.
>
> 구현할 수 있다면 "초기화" 기능을 구현해서 확인해보세요!



