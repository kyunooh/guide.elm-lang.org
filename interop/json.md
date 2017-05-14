> 이 단원은 elm-repl을 사용해요.  만약 아직 [설치](install.md)하지 않으셨다면,  [온라인REPL](http://elmrepl.cuberoot.in/)을 사용하셔도 됩니다 .

# JSON

여러분은 프로그램에서 JSON을 많이 전송하실 텐데요. [`Json.Decode` 라이브러리](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode)를 이용해서 이상한 형태의 JSON을 잘 구조화된 Elm 값으로 변환 할 수 있어요.

JSON을 사용하여 작업할 때 핵심적인 개념은 바로 **디코더\(decoder\)**에요. 디코드는 JSON 값을 Elm값으로 해석해줘요. 이제 기본적인 디코더를 살펴보고, 어떻게 복잡한 상황에서도 다룰 수 있는지도 살펴 볼게요.

## 원시형 디코더\(Primitive Decoders\)

다음은 몇 가지의 원시형 디코더들이에요.

```elm
string : Decoder String
int : Decoder Int
float : Decoder Float
bool : Decoder Bool
```

이것들을 `decodeString` 같이 쓸 때 유용하게 사용할 수 있어

```elm
decodeString : Decoder a -> String -> Result String a
```

이 의미는 다음과 같이 사용한다는 거에요.

```elm
> import Json.Decode exposing (..)

> decodeString int "42"
Ok 42 : Result String Int

> decodeString float "3.14159"
Ok 3.14159 : Result String Float

> decodeString bool "true"
Ok True : Result String Bool

> decodeString int "true"
Err "Expecting an Int but instead got: true" : Result String Int
```

이 작은 디코더들 JSON 문자열 값을 `Result` 값으로 변환시켜줍니다.

간단한 JSON 값들을 다루어 보았는데요. 배열과 객체같은 복잡한 것들은 어떻게 처리해야 할까요?

## 디코더 조합하기\(Combining Decoders\)

디코더의 장점 중 블록처럼 분리되어 사용된다는 거에요. 값 리스트를 다루고 싶다면 [`list`](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode#list) 함수를 사용 할 수 있죠.

```elm
list : Decoder a -> Decoder (List a)
```

이제 원시형 디코더를 조합하여 사용할 수 있어요.

```elm
> import Json.Decode exposing (..)

> int
<decoder> : Decoder Int

> list int
<decoder> : Decoder (List Int)

> decodeString (list int) "[1,2,3]"
Ok [1,2,3] : Result String (List Int)

> decodeString (list string) """["hi", "yo"]"""
Ok ["hi", "yo"] : Result String (List String)
```

자 그럼 이제  JSON 배열도 다룰 수 있어요. 리스트를 중첩시킨, 미친듯이 복잡해보이는 데이터도 다룰 수 있어요.

```elm
> decodeString (list (list int)) "[ [0], [1,2,3], [4,5] ]"
Ok [[0],[1,2,3],[4,5]] : Result String (List (List Int))
```

위에 건  `list`에요 하지만`JSON.Decode`는 다른 형태의 데이터도 다룰 수 있어요. 예를들어, [`dict`](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode#dict) 는  JSON 객체를 Elm의 Dict로 바꿀 수 있고, [`keyValuePairs`](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode#keyValuePairs)는  JSON 객체를  Elm의 키와 값 리스트로 바꿀 수 있죠.

## 객체 디코딩\(Decoding Objects\)

JSON 객체를 [`field`](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode#field) 함수로 디코딩 해볼게요. `list` 와 비슷하게 진행되요

```elm
field : String -> Decoder a -> Decoder a
```

자 여러분이 field `"x" int` 와 같이 작성하면, 먼저 JSON 객체를 원하는데, 이 객체는 x 라는 필드를 가져야만하고 x의 값은 정수여야 한다라는 걸 의미해요. 다음과 같이 사용하면 된답니다.

So when you say `field "x" int` you are saying \(1\) I want a JSON object, \(2\) it should have a field `x`, and \(3\) the value at `x` should be an integer. So using it looks like this:

```elm
> import Json.Decode exposing (..)

> field "x" int
<decoder> : Decoder Int

> decodeString (field "x" int) """{ "x": 3, "y": 4 }"""
Ok 3 : Result String Int

> decodeString (field "y" int) """{ "x": 3, "y": 4 }"""
Ok 4 : Result String Int
```

`field "x" int` 디코더는 오직 `x`에 관해서만 신경써요. 객체는 다른 내용을 가진 다른 필드들을 가질 수도 있습니다. 각각 모두 별개에요. 하지만 많은 필드로 부터 정보들을 가져오고 싶으면 어떻게 할까요? 그냥 많은 디코더를 조합하시면 돼요. 이건  [`map2`](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode#map2)같은 함수를 이용하면 가능해요.

```elm
map2 : (a -> b -> value) -> Decoder a -> Decoder b -> Decoder value
```

이 함수는 두개의 다른 디코더를 인자로 사용해요. 만약 두개다 성공하면, 둘 다 성공하면 주어진 함수로 결과들을 조합해주죠. 자 그럼 이제 두개의 다른 디코더를 넣어볼게요. 

```elm
> import Json.Decode exposing (..)

> type alias Point = { x : Int, y : Int }

> Point
<function> : Int -> Int -> Point

> pointDecoder = map2 Point (field "x" int) (field "y" int)
<decoder> : Decoder Point

> decodeString pointDecoder """{ "x": 3, "y": 4 }"""
Ok { x = 3, y = 4 } : Result String Point
```

자 이제 두개의 필드도 다룰 수 있네요. 하지만 세개 또는 네개는 어떨까요? 코어 라이브러리[`map3`](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode#map3), [`map4`](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode#map4) 그리고 더 큰 객체도 다룰 수 있게 제공하고 있어요.

여러분이 큰 JSON 객체로 작업을 한다면, [`NoRedInk/elm-decode-pipeline`](https://www.gitbook.com/book/kyunooh/elm/edit#) 을 확인해보는 게 도움이 될 거에요. 

Okay, that covers two fields, but what about three? Or four? The core library provides [`map3`](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode#map3), [`map4`](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode#map4), and others for handling larger objects.

As you start working with larger JSON objects, it is worth checking out [`NoRedInk/elm-decode-pipeline`](http://package.elm-lang.org/packages/NoRedInk/elm-decode-pipeline/latest). 여기서 설명한 `Json.Decode` 모듈을 중심으로 작성되었으며, 다음과 같이 작성할 수 있습니다.

```elm
import Json.Decode exposing (Decoder, int)
import Json.Decode.Pipeline exposing (decode, required)

type alias Point = { x : Int, y : Int }

pointDecoder : Decoder Point
pointDecoder =
  decode Point
    |> required "x" int
    |> required "y" int
```

`optional`과 `hardcoded` 필드 사용할 수 있어요. 아주 좋은 라이브러리이니 한번 살펴보세요.

You can have `optional` and `hardcoded` fields as well. It is quite a nice library, so take a look!

> ## Broader Context
>
> By now you have seen a pretty big chunk of the actual `Json.Decode` API, so I want to give some additional context about how this fits into the broader world of Elm and web apps.
>
> ### Validating Server Data
>
> The conversion from JSON to Elm doubles as a validation phase. You are not just converting from JSON, you are also making sure that JSON conforms to a particular structure.
>
> In fact, decoders have revealed weird data coming from NoRedInk’s _backend_ code! If your server is producing unexpected values for JavaScript, the client just gradually crashes as you run into missing fields. In contrast, Elm recognizes JSON values with unexpected structure, so NoRedInk gives a nice explanation to the user and logs the unexpected value. This has actually led to some patches in their Ruby server!
>
> ### A General Pattern
>
> JSON decoders are an example of a more general pattern in Elm. You see it whenever you want to wrap up complicated logic into small building blocks that snap together easily. Other examples include:
>
> * `Random` — The `Random` library has the concept of a `Generator`. So a `Generator Int` creates random integers. You start with primitive building blocks that generate random `Int` or `Bool`. From there, you use functions like `list` and `map` to build up generators for fancier types.
>
> * `Easing` — The Easing library has the concept of an `Interpolation`. An `Interpolation Float` describes how to slide between two floating point numbers. You start with interpolations for primitives like `Float` or `Color`. The cool thing is that these interpolations compose, so you can build them up for much fancier types.
>
> As of this writing, there is some early work on Protocol Buffers \(binary data format\) that uses the same pattern. In the end you get a nice composable API for converting between Elm values and binary!



