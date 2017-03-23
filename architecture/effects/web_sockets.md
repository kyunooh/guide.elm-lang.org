# 웹 소켓\(Web Sockets\)

---

#### [코드를 클론](https://github.com/evancz/elm-architecture-tutorial/)하거나 [온라인 에디터](http://elm-lang.org/examples/websockets)에서 함께 따라해 주세요.

---

이번엔 간단한 채팅앱을 만들어 볼게요. 채팅 입력을 할 수 있는 텍스트 필드와 받은 메시지 들이 보여지는 화면을 만들거에요. `웹 소켓`은 서버와 지속적인 연결을 해주기 때문에 이 예제에 적합해요. 이 의미는 다음과 같아요.

1. 여러분은 언제든지 적은 비용으로 메시지를 보낼 수 있어요. 
2. 서버는 메시지를 받을 때마다 여러분에게 메시지를 보낼 수 있어요.

즉 웹 소켓은 명령과 구독 두가지 다를 하는 몇 안되는 라이브러리중 하나에요.

이 프로그램은 생각보다 짧아서, 아래 코드가 전부에요.

```elm
import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (..)
import WebSocket


main =
  Html.program
    { init = init
    , view = view
    , update = update
    , subscriptions = subscriptions
    }


-- MODEL

type alias Model =
  { input : String
  , messages : List String
  }


init : (Model, Cmd Msg)
init =
  (Model "" [], Cmd.none)


-- UPDATE

type Msg
  = Input String
  | Send
  | NewMessage String


update : Msg -> Model -> (Model, Cmd Msg)
update msg {input, messages} =
  case msg of
    Input newInput ->
      (Model newInput messages, Cmd.none)

    Send ->
      (Model "" messages, WebSocket.send "ws://echo.websocket.org" input)

    NewMessage str ->
      (Model input (str :: messages), Cmd.none)


-- SUBSCRIPTIONS

subscriptions : Model -> Sub Msg
subscriptions model =
  WebSocket.listen "ws://echo.websocket.org" NewMessage


-- VIEW

view : Model -> Html Msg
view model =
  div []
    [ div [] (List.map viewMessage model.messages)
    , input [onInput Input] []
    , button [onClick Send] [text "Send"]
    ]


viewMessage : String -> Html msg
viewMessage msg =
  div [] [ text msg ]
```

흠미로운 부분은  Websocket.send 와 WebSocket.listen을 사용한 부분인데요.

예제를 간단하게 진행하기 위해, 입력한 메시지를 그냥 다시 반환해주는 간단한 서버로 잡았어요. 따라서 위 버전에서는 막 흥미로운 채팅은 불가능 하겠지만 이 예제는 연습용으론 충분하죠!

