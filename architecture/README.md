# Elm 아키텍처

'Elm 아키텍처'는 웹 어플리케이션을 위한 간단한 패턴으로 구성되어 있어요. 모듈화 하기도 쉽고, 코드 재사용성을 보장하며, 테스팅도 편해요. 궁극적으로는 복잡한 웹 어플리케이션을 쉽게 만들 수 있게 해주고, 코드를 깨끗하게 유지하므로서 리팩토링과, 기능 추가를 쉽게 해주죠.

이 아키텍츠는 Elm에서 자연스럽게 나타났어요. Elm 커뮤니티에서 게임을 만드는 도중 처음 발견하게 되었죠. 그리고 [TodoMVC](https://github.com/evancz/elm-todomvc)와  [dreamwriter](https://github.com/rtfeldman/dreamwriter#dreamwriter) 에서도 발견되었고, 이제는 [NoRedInk](https://www.noredink.com/)와 [CircuitHub](https://www.circuithub.com/) 같은 회사의 상품 코드에서도 사용하고 있어요. 이 아키텍처는 Elm 그 자체의 결과물이에요. 때문에  이 아키텍처를 알든 모르든, 결국 이 아키텍처를 따르게 될 거에요. 이 아키텍처는 적응하기 쉽다는 게 이미 증명되었어요. 새로운 개발자들은 손쉽게 좋은 구조의 코드를 작성했죠.

'Elm 아키텍처'는 Elm에서 아주 쉬울뿐만 아니라, 어떤 프론트엔드던지 유용하답니다. So The Elm Architecture is _easy_ in Elm, but it is useful in any front-end project. In fact, projects like Redux have been inspired by The Elm Architecture, so you may have already seen derivatives of this pattern. Point is, even if you ultimately cannot use Elm at work yet, you will get a lot out of using Elm and internalizing this pattern.

## The Basic Pattern

The logic of every Elm program will break up into three cleanly separated parts:

* **Model** — the state of your application
* **Update** — a way to update your state
* **View** — a way to view your state as HTML

This pattern is so reliable that I always start with the following skeleton and fill in details for my particular case.

```elm
import Html exposing (..)


-- MODEL

type alias Model = { ... }


-- UPDATE

type Msg = Reset | ...

update : Msg -> Model -> Model
update msg model =
  case msg of
    Reset -> ...
    ...


-- VIEW

view : Model -> Html Msg
view model =
  ...
```

That is really the essence of The Elm Architecture! We will proceed by filling in this skeleton with increasingly interesting logic.

