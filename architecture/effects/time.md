# 시간

---

#### [코드를 클론](https://github.com/evancz/elm-architecture-tutorial/)하거나 [온라인 에디터](http://elm-lang.org/examples/time)에서 따라해 주세요.

---

간단한 시계를 만들어 보죠.

랜덤 값을 이용한 예제, 서버와 통신하는 HTTP 예제를 통해 지금까지는 명령에 집중했었는데요. 시계 예제에서는 이전 패턴들로는 만들 수가 없어요. 시계가 틱톡 할때 마다 시계도 움직여야겠죠? 이 틱톡을 알아채는 게 바로 **구독\(subscriptions\)**이에요.

코드가 그리 복잡하지 않기 때문에 모든 코드를 한 곳에서 작성했어요. 코드 전체를 살펴 본 뒤 각각을 깊게 들어가 볼게요.

```elm
import Html exposing (Html)
import Svg exposing (..)
import Svg.Attributes exposing (..)
import Time exposing (Time, second)



main =
  Html.program
    { init = init
    , view = view
    , update = update
    , subscriptions = subscriptions
    }


-- MODEL

type alias Model = Time


init : (Model, Cmd Msg)
init =
  (0, Cmd.none)


-- UPDATE

type Msg
  = Tick Time


update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    Tick newTime ->
      (newTime, Cmd.none)


-- SUBSCRIPTIONS

subscriptions : Model -> Sub Msg
subscriptions model =
  Time.every second Tick


-- VIEW

view : Model -> Html Msg
view model =
  let
    angle =
      turns (Time.inMinutes model)

    handX =
      toString (50 + 40 * cos angle)

    handY =
      toString (50 + 40 * sin angle)
  in
    svg [ viewBox "0 0 100 100", width "300px" ]
      [ circle [ cx "50", cy "50", r "45", fill "#0B79CE" ] []
      , line [ x1 "50", y1 "50", x2 handX, y2 handY, stroke "#023963" ] []
      ]
```

`MODEL` 과 `UPDATE` 부분은 별 다른 게 없어요. 이전과 같죠. `view` 함수가 좀 흥미로운데요. HTML을 사용하는 대신 `Svg` 라이브러리를 이용해 도형을 그려요. 이건 HTML처럼 동작하는데요. 모든 노드에 대해 속성 리스트와 자식 노드 리스트를 제공해요.

가장 중요한 중요한 부분은 SUBSCRIPTIONS 부분인데요. `subscriptions` 함수는 모델을 가져와서, `Sub.none`을 반환하는 대신 실제 세상에서 처럼 구독을 제공해요. 이 경우에선 `Time.every` 죠.

```elm
Time.every : Time -> (Time -> msg) -> Sub msg
```

첫번째 인자는 시간 간격이에요. 우리는 매초 마다 반응할거에요. 두번째 인자는 현재 시간을 update 함수를 위한 메시지로 바꿔줘요. 우리가 1458863979862를 `Tick`으로 태깅하면 `Tick 1458863979862`가 되는거죠.

이것이 구독을 설정하는 방법이에요! 이 메시지들이 사용 가능할 때마다 update 함수에 전달 되는 거죠.

> **연습문제:**
>
> * 일시 정지 버튼을 추가해서 Time 구독이 꺼지도록 하세요.
> * 시계를 좀 더 멋있게 만들어보세요. 시침과 분침을 추가하는



