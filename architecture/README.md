# Elm 아키텍처

'Elm 아키텍처'는 웹 어플리케이션을 위한 간단한 패턴으로 구성되어 있어요. 모듈화 하기도 쉽고, 코드 재사용성을 보장하며, 테스팅도 편해요. 궁극적으로는 복잡한 웹 어플리케이션을 쉽게 만들 수 있게 해주고, 코드를 깨끗하게 유지하므로서 리팩토링과, 기능 추가를 쉽게 해주죠.

이 아키텍츠는 Elm에서 자연스럽게 나타났어요. Elm 커뮤니티에서 게임을 만드는 도중 처음 발견하게 되었죠. 그리고 [TodoMVC](https://github.com/evancz/elm-todomvc)와  [dreamwriter](https://github.com/rtfeldman/dreamwriter#dreamwriter) 에서도 발견되었고, 이제는 [NoRedInk](https://www.noredink.com/)와 [CircuitHub](https://www.circuithub.com/) 같은 회사의 상품 코드에서도 사용하고 있어요. 이 아키텍처는 Elm 그 자체의 결과물이에요. 때문에  이 아키텍처를 알든 모르든, 결국 이 아키텍처를 따르게 될 거에요. 이 아키텍처는 적응하기 쉽다는 게 이미 증명되었어요. 새로운 개발자들은 손쉽게 좋은 구조의 코드를 작성했죠.

'Elm 아키텍처'는 Elm에서 아주 쉬울뿐만 아니라, 모든 프론트엔드 프로젝트에서 유용하답니다. 사실 Redux 같은 건 이미 Elm 아키텍처의 영향을 받았어요. 그렇기에 여러분들도 이 패턴에서 파생한 것들을 이미 보셨을 수도 있죠. 요점을 말씀드리자면, 여러분이 Elm을 실제 회사에서 사용할 수 없더라도, Elm 외에 다른 곳에서 이런 패턴을 적용할 수 있다는 것입니다.

## 기본적인 패턴

모든 Elm 프로그램의 로직들은 아래와 같이 세개로 깨끗하게 분리되요.

* **Model** — 어플리케이션의 상태 the state of your application
* **Update** — 상태를 수정하는 부분 a way to update your state
* **View** — HTML로 상태를 표현하는 부분 a way to view your state as HTML

저는 항상 다음과 같이 뼈대를 잡고 점점 채워 나가는 데, 이 방식은 매우 신뢰할 수 있는 패턴이에요.

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

이게 Elm 아키텍처의 핵심입니다! 이제 점점 더 재미있는 로직으로 뼈대를 채워나가시면 되요. 

