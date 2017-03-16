# 시간

---

#### [코드를 클론하](https://github.com/evancz/elm-architecture-tutorial/)거나 [온라인 에디터](http://elm-lang.org/examples/time)에서 따라해 주세요.

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

There is nothing new in the `MODEL` or `UPDATE` sections. Same old stuff. The `view` function is kind of interesting. Instead of using HTML, we use the `Svg` library to draw some shapes. It works just like HTML though. You provide a list of attributes and a list of children for every node.

The important thing comes in `SUBSCRIPTIONS` section. The `subscriptions` function takes in the model, and instead of returning `Sub.none` like in the examples we have seen so far, it gives back a real life subscription! In this case `Time.every`:

```elm
Time.every : Time -> (Time -> msg) -> Sub msg
```

The first argument is a time interval. We chose to get ticks every second. The second argument is a function that turns the current time into a message for the `update` function. We are tagging times with `Tick` so the time 1458863979862 would become `Tick 1458863979862`.

That is all there is to setting up a subscription! These messages will be fed to your `update` function whenever they become available.

> **Exercises:**
>
> * Add a button to pause the clock, turning the `Time` subscription off.
> * Make the clock look nicer. Add an hour and minute hand. Etc.



