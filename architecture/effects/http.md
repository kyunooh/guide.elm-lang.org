# HTTP

---

#### [코드를 클론](https://github.com/evancz/elm-architecture-tutorial/)하거나 [온라인 에디터](http://elm-lang.org/examples/http)에서 함께 따라해주세요.

---

유저가 새로운 gif를 요청했을 때, 임의의 gif를 가져오는 앱을 만들어 볼 거에요.

랜덤 예제를 읽었다고 가정하에 진행할 거에요. 이전 단원에서, 앱을 작성하는 두단계의 과정을 배웠고, 간단한 종류의 명령을 보았어요. 이 단원에서는  같은 과정으로 좀 더 멋있는 종류의 명령을 해볼 거에요. 그렇기 때문에 이전 단원을 아직 안보셨다면, 되돌아 가시길 권해요. 맹세코 목표에 가장 빨리 도달하는 방법은 탄탄한 기초를 쌓는 거에요!

...

자 이제 다 읽으셨나요? 좋군요. 그럼 임의의 gif를 가져오도록 해보죠!

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

모델의 경우 어떤 종류의 gif인지 지정하기 위해 `topic` 을 추가했어요. 사실 전 "cats"라고 하드코딩 하고 싶진 않고, 나중에 사용자가 주제를 정할 수 있게 끔도 하고 싶어요. 또한 임의의 gif URL을 가르키는 `gifUrl` 도 추가했어요.

랜덤 예제에서 했듯이 임시로 `init` 과 `update` 함수를 만들었어요. 지금은 사실 아무 명령도 제공하지 않지만, 요점은 화면이 있다는 거에요!

## 두번째 단계 - 동작 추가하기

이제 HTTP 요청을 해야겠죠. 동작을 처리하기 위해, 새로운 메시지를 추가하는 것부터 시작하는 게 가장 쉬운 길로 보이네요. **여러분이 명령을 주면, 처리될 때까지 기다려야 한다는 걸 **기억하세요. 우리가 Elm에 HTTP 요청을 하면, 결국 "자 여기 너가 원하던 것이 있어!" 또는 "아 이런! HTTP요청에 문제가 생겼네.." 라고 말해줄 거에요. 우리는 이것을 메시지에 반영시켜줘야 해요.

```elm
type Msg
  = MorePlease
  | NewGif (Result Http.Error String)
```

결과\(`Result`\)를 가지는 `NewGif` 메시지를 추가했습니다. 결과에 관한 자세한건 [여기](/error_handling/result.md)를 참고하세요. 핵심은 HTTP요청에서 가능한 두가지 결과를 받을 수 있다는 거에요. 성공적으로 임의의 gif URL을 받아오는 것, 또는 HTTP 에러가 발생하는 경우죠.\(서버가 죽었거나, URL이 잘못된 경우 등\) 이제 `update`를 작성해도 되겠군요.

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

새로운 메시지에 대한 경우를 추가했어요. `NewGif`가 성공적이면 `gifUrl`을 새로운 URL로 변경해주고, `NewGif`에서 에러가 발생한다면 무시하고 같은 모델을 돌려주도록 처리해요.

또한 `MorePlease`도 살짝 바꿨는데요. `getRandomGif`라는 함수를 호출 할 HTTP 명령이 필요해요. 여기선 일단 호출하도록만 하고, 아직 함수를 만들진 않았으니, 존재하지 않는 함수에요. 이제부터 만들거랍니다!

`getRandomGif`는 다음과 같이 정의해요.

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

추가된 "More" 버튼을 통해 이제 실재 임의의 gif를 가져와요. [여기](http://elm-lang.org/examples/http)서 확인해보세요! 그럼 `getRandomGif`는 과연 어떻게 동작하는 걸까요?

간단하게 시작해보죠. 임의의 gif를 가져오기 위해 giphy의 url을 정의해요.그 다음엔 [`Http.get`](http://package.elm-lang.org/packages/elm-lang/http/latest/Http#get)을 이용해 HTTP 요청\(`request` \)을 만들어주고,  최종적으론 [`Http.send`](http://package.elm-lang.org/packages/elm-lang/http/latest/Http#send) 를 사용하여, 명령으로 전환해줘요. 자 이걸 쪼개서 파헤쳐 봅시다.

* `Http.get : String -> Json.Decoder value -> Http.Request value`

  이 함수는 Url과 JSON 디코더를 받아요. 이번에 JSON디코더를 처음 보셨겠죠.\( [나중에](/interop/json.md) 좀 더 깊게 살펴볼 거에요\) 하지만 지금은 상위 레벨의 개념만 이해하시면 되요. JSON을 Elm코드로 변환된 건데, 위의 경우엔 `decodeGifUrl`이 `json.data.image_url`의 문자열 값을 찾아요.  URL과 JSON 디코더 사이에 `Http.Request` 를 만들었죠. 이건 [이전 예제에서 본](random.md) Random.Generator와 비슷한 거에요. 이건 실제로 그 어떤 동작도 하지 않고, 그저 HTTP request를 만드는 방법인 것 뿐이죠. This function takes a URL and a JSON decoder. This is our first time seeing a JSON decoder \(and we will cover them in depth [later](/interop/json.md)\), but for now, you really just need a high-level understanding. It turns JSON into Elm. In our case, we defined `decodeGifUrl` which tries to find a string value at `json.data.image_url`. Between the URL and the JSON decoder, we create an `Http.Request`. This is similar to a `Random.Generator` like we saw in [the previous example](random.md). It does not actually _do_ anything. It just describes how to make an HTTP request.

* `Http.send : (Result Error value -> msg) -> Http.Request value -> Cmd msg`

  HTTP 요청을 `Http.send`로 바꿀 수 있어요. 이건 `Random.generate`를 이용해서 임의의 생성자를 만든 것과 비슷해요. 첫번째 인자는  update함수의 메시지로 Http request의 결과를 전해줘요. 위의 경우엔 `NewGif` 메시지를 만들죠.

* Once we have an HTTP request, we can turn it into a command with `Http.send`. This is just like how we used `Random.generate` to create a command with a random generator. The first argument is a way to turn the result of the HTTP request into a message for our `update` function. In this case, we create a `NewGif` message.

사실 이 단원은 매우 간단하게 설명했지만, 핵심은 첫번째로 HTTP 요청을 만들고, 그걸 Elm 명령어로 만들면 실제로 동작한다는 거에요. 여러분은 이 기본 패턴을 이용해 꾀 많은 것을 할 수 있어요.  [l나중에 n](/interop/json.md)JSON Decoder를 살펴볼 것이고, 그 어떤 이상한 JSON 이든 사용할 수 있어요.

This has been a very quick introduction, but the key idea is that you must \(1\) create an HTTP request and \(2\) turn that into a command so Elm will actually _do_ it. You can go pretty far using the basic pattern here, and we will be looking into JSON decoders [later on](/interop/json.md), which will let you deal with whatever crazy JSON you run into.

> **Exercises:** To get more comfortable with this code, try augmenting it with skills we learned in previous sections:
>
> * Show a message explaining why the image didn't change when you get an [`Http.Error`](http://package.elm-lang.org/packages/elm-lang/http/latest/Http#Error).
> * Allow the user to modify the `topic` with a text field.
> * Allow the user to modify the `topic` with a drop down menu.



