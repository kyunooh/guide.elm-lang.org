# HTTP

---

#### [코드를 클론](https://github.com/evancz/elm-architecture-tutorial/)하거나 [온라인 에디터](http://elm-lang.org/examples/http)에서 함께 따라해주세요.

---

유저가 새로운 이미지를 요청했을 때, 임의의 이미지를 가져오는 앱을 만들어 볼 거에요.

랜덤 예제를 읽었다고 가정하에 진행할 거에요. 이전 단원에서, 앱을 작성하는 두단계의 과정을 배웠고, 간단한 종류의 명령을 보았어요. 이 단원에서는  같은 과정으로 좀 더 멋있는 종류의 명령을 해볼 거에요. 그렇기 때문에 이전 단원을 아직 안보셨다면, 되돌아 가시길 권해요. 맹세코 목표에 가장 빨리 도달하는 방법은 탄탄한 기초를 쌓는 거에요!

...

자 이제 다 읽으셨나요? 좋군요. 그럼 임의의 이미지를 가져오도록 해보죠!

## 첫번째 단계 - 최소한의 코드

이제는 여러분이 Elm 앱의 기본구조를 익숙하게 만들 수 있어야 해요. 모델과 메시지 등등을 생각해보고 채워 보세요.

```elm
-- MODEL

type alias Model =
  { topic : String
  , gifUrl : String
  }

init : (Model, Cmd Msg)
init =
  (Model "cats" "waiting.gif", Cmd.none)


-- UPDATE

type Msg = MorePlease

update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    MorePlease ->
      (model, Cmd.none)


-- VIEW

view : Model -> Html Msg
view model =
  div []
    [ h2 [] [text model.topic]
    , img [src model.gifUrl] []
    , button [ onClick MorePlease ] [ text "More Please!" ]
    ]
```

모델의 경우 어떤 종류의 이미지인지 지정하기 위해 `topic` 을 추가했어요. 사실 전 "cats"라고 하드코딩 하고 싶진 않고, 나중에 사용자가 주제를 정할 수 있게 끔도 하고 싶어요. 또한 임의의 이미지 URL을 가르키는 `gifUrl` 도 추가했어요.

랜덤 예제에서 했듯이 임시로 `init` 과 `update` 함수를 만들었어요. 지금은 사실 아무 명령도 제공하지 않지만, 요점은 화면이 있다는 거에요!

## 두번째 단계 - 동작 추가하기

이제 HTTP 요청을 해야겠죠. 동작을 처리하기 위해, 새로운 메시지를 추가하는 것부터 시작하는 게 가장 쉬운 길로 보이네요. **여러분이 명령을 주면, 처리될 때까지 기다려야 한다는 걸 **기억하세요. 우리가 Elm에 HTTP 요청을 하면, 결국 "자 여기 너가 원하던 것이 있어!" 또는 "아 이런! HTTP요청에 문제가 생겼네.." 라고 말해줄 거에요. 우리는 이것을 메시지에 반영시켜줘야 해요.

```elm
type Msg
  = MorePlease
  | NewGif (Result Http.Error String)
```

We add a case for `NewGif` that holds a `Result`. You can read more about results [here](/error_handling/result.md), but the key idea is that it captures the two possible outcomes of an HTTP request. It either \(1\) succeeded with the URL of a random gif or \(2\) failed with some HTTP error \(server is down, bad URL, etc.\)

That is enough to start filling in `update`:

```elm
update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  case msg of
    MorePlease ->
      (model, getRandomGif model.topic)

    NewGif (Ok newUrl) ->
      ( { model | gifUrl = newUrl }, Cmd.none)

    NewGif (Err _) ->
      (model, Cmd.none)
```

So I added branches for our new messages. When `NewGif` holds a success, we update the `gifUrl` field to have the new URL. When `NewGif` holds an error, we ignore it, giving back the same model and doing nothing.

I also changed the `MorePlease` branch a bit. We need an HTTP command, so I called the `getRandomGif` function. The trick is that I made that function up. It does not exist yet. That is the next step!

Defining `getRandomGif` might look something like this:

```elm
getRandomGif : String -> Cmd Msg
getRandomGif topic =
  let
    url =
      "https://api.giphy.com/v1/gifs/random?api_key=dc6zaTOxFJmzC&tag=" ++ topic

    request =
      Http.get url decodeGifUrl
  in
    Http.send NewGif request

decodeGifUrl : Json.Decoder String
decodeGifUrl =
  Json.at ["data", "image_url"] Json.string
```

With that added, the "More" button actually goes and fetches a random gif. Check it out [here](http://elm-lang.org/examples/http)! But how does `getRandomGif` work exactly?

It starts out simple, we define `url` to be some giphy endpoint for random gifs. Next we create an HTTP `request` with [`Http.get`](http://package.elm-lang.org/packages/elm-lang/http/latest/Http#get). Finally, we turn it into a command with [`Http.send`](http://package.elm-lang.org/packages/elm-lang/http/latest/Http#send). Let’s break those steps down a bit more:

* `Http.get : String -> Json.Decoder value -> Http.Request value`

  This function takes a URL and a JSON decoder. This is our first time seeing a JSON decoder \(and we will cover them in depth [later](/interop/json.md)\), but for now, you really just need a high-level understanding. It turns JSON into Elm. In our case, we defined `decodeGifUrl` which tries to find a string value at `json.data.image_url`. Between the URL and the JSON decoder, we create an `Http.Request`. This is similar to a `Random.Generator` like we saw in [the previous example](random.md). It does not actually _do_ anything. It just describes how to make an HTTP request.

* `Http.send : (Result Error value -> msg) -> Http.Request value -> Cmd msg`

  Once we have an HTTP request, we can turn it into a command with `Http.send`. This is just like how we used `Random.generate` to create a command with a random generator. The first argument is a way to turn the result of the HTTP request into a message for our `update` function. In this case, we create a `NewGif` message.

This has been a very quick introduction, but the key idea is that you must \(1\) create an HTTP request and \(2\) turn that into a command so Elm will actually _do_ it. You can go pretty far using the basic pattern here, and we will be looking into JSON decoders [later on](/interop/json.md), which will let you deal with whatever crazy JSON you run into.

> **Exercises:** To get more comfortable with this code, try augmenting it with skills we learned in previous sections:
>
> * Show a message explaining why the image didn't change when you get an [`Http.Error`](http://package.elm-lang.org/packages/elm-lang/http/latest/Http#Error).
> * Allow the user to modify the `topic` with a text field.
> * Allow the user to modify the `topic` with a drop down menu.



