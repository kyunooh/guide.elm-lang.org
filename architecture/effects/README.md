# Elm 아키텍쳐 + 효과

지난 단원에서는 사용자 입력에 대해서 배웠는데요. 여러분은 프로그램이 다음과 같이 동작한다고 생각하실 수 있겠죠.

![](beginnerProgram.svg)

위와 같은 같은 관점에서 본다면, 그저 메시지를 받고 새로운 렌더링 된 HTML을 화면에 제공해주는 것 뿐이에요. "Elm Runtime"은 뒤에 숨어 이런 처리를 해주는 것이고요. HTML을 렌더링해 가져오는 과정이 [무척 빠르게 동작하죠.](http://elm-lang.org/blog/blazing-fast-html-round-two) 사용자가 클릭과 같은 동작을 하였을 때 Msg가 전달되어 변화를 파악하고, Elm Runtime 은 이런 과정을 처리해줍니다. 우리는 그냥 데이터만 변화시켜주면 되는 거죠.

이 단원은 Http 요청을 받거나, 웹소켓\(web sockets\)을 통한 메시지를 구독하는 등의 패턴을 기반으로 진행합니다. 다음과 같이 생각하시면 되죠.

![](program.svg)

`Html`을 제공하는 대신에, 명령\(commands\)와 구독\(subscriptions\)을 제공할 거에요.

* **명령\(Commands\)** — `Cmd`는 랜덤 숫자 생성이나, HTTP 요청등의 _행위_를 맡아요.

* **구독\(Subscriptions\)** — `Sub`엔 위치 변경이라던가, 웹 소켓 메시지 등의 관심사를 등록해요.

코드를 대충 보면, 명령과 구독은 `Html` 값과 굉장히 비슷합니다. `Html`을 사용하면, DOM을 손수 건드릴 일이 없어요. HTML로 _데이터_ 등을 표현하지 않고, Elm Runtime이 이런과정을 [무척 빠르게](http://elm-lang.org/blog/blazing-fast-html-round-two) 렌더링하죠. 명령과 구독도 마찬가지로 동작해요. 우리가 _구독_하게 할 데이터를 만들고 Elm Runtime에서 복잡한 작업들을 처리해주죠.

복잡한가요? 지금은 혼란하고 어려운 개념 같지만, 예제를 통해 배워 나갈 수 있을 거에요. 우선 이런 개념들이 코드에 어떻게 적용되는지부터 살펴 볼게요.

## 설계로부터 확장해나가기

자 지금까지의 아키텍처는 `Model`타입과 `update`, `view` 함수에 초점을 맞췄었어요. 명령과 구독을 처리하기 위해선 기본적인 패턴을 조금 확장 시킬 필요가 있어요.

```elm
-- MODEL

type alias Model =
  { ...
  }


-- UPDATE

type Msg = Submit | ...

update : Msg -> Model -> (Model, Cmd Msg)
update msg model =
  ...


-- VIEW

view : Model -> Html Msg
view model =
  ...


-- SUBSCRIPTIONS

subscriptions : Model -> Sub Msg
subscriptions model =
  ...


-- INIT

init : (Model, Cmd Msg)
init =
  ...
```

처음 세 부분은 거의 똑같아 보이지만, 몇가지 새로운 사항들이 있어요.The first three sections are almost exactly the same, but there are a few new things overall:

1. 이제 `update` 함수는 새로운 모델을 반환해주는 것 이상의 일을 해요. 새로운 모델과 실행할 명령도 같이 반환하죠. 이 명령은 `update` 함수에 들어갈 `Msg` 값을 제공해요.

2. `구독(subscriptions)` 함수를 봐볼게요. 이 함수엔 현재의 모델이 구독해야할 그 어떤 이벤트든 선언할 수 있어요. `Html Msg` 와 `Cmd Msg`처럼, 구독 함수는 `update` 함수에 들어갈 `Msg` 값을 제공해요.

3. 지금까지 `init` 은 초기화 된 모델이었죠. 이제는 새로운 `update` 함수처럼, 모델과 명령, 둘 다를 제공해요. 초기화가 필요한 HTTP 요청이라던가, 시작시 필요한 그 어떤 값이든 초기화시킬 수 있어요.

아직 완벽하게 이해되지 않더라도 괜찮아요! 실제로 동작하는 것을 본다면 이해하실 수 있을 거에요. 자 그런 의미에서 예제를 통해 확인해볼게요!

> ** 조언 :** 중요한 건 명령과 구독이 _데이터_ 라는 것이에요. 명령을 만들때 실제로 무언가 _동작_ 되는 것이 아니랍니다. 실제 상황에서의 명령과 같아요. 자 보세요. 누군가 "수박 한 통을 전부 한입에 먹어봐!"라고 했을 때, 이미 그것을 했을까요? 아니죠! 심지어 당신이 '작은 수박을 사야지'라고 _생각_하기도 전부터, 그 말을 듣고 있는 중인거에요. 행동하는 것이 아니라는 거죠!
>
> 요점은, 명령과 구독이 데이터라는 것이에요. 실제로 Elm을 다룰 때, 모든 정보에 대한 기록들을 Elm이 넘겨받도록 해요. 이런 과정을 통해 엘름이 다음과 같은 것들을 할 수 있게 되죠.
>
> * 시간여행\(time-travel\) 디버거
> * 모든 Elm 함수가 "같은 입력엔, 같은 출력"을 갖게하도록 유지함
> * `update` 로직을 테스트할 때, setup/teardown 구문등을 피함
> * 효과를 캐시하거나 배치\(batch\)처리, HTTP 요청과 기타 다른 자원을 최소화 함
>
> 세부 구현들을 신경쓰지 않으면서 Elm 코드가 동작을 보장할 수 있는 것은, 효과들을 데이터로 초점을 맞추는 데에서 비롯되었어요! Elm에 대해 깊이 들어갈수록 이런 이유를 잘 이해하게 될 거에요.



