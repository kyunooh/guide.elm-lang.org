# 타입\(Types\)

**유저가 런타임 에러를 볼일이 없는 건** Elm의 장점중 하나에요. Elm 컴파일러가 소스코드를 분석하고 매우 빠르게 프로그램에서 값의 흐름들을 분석하기에 가능하죠. 만약에 값이 잘못된 길로 가고 있다면, 컴파일러가 여러분에게 친근한 에러 메시지로 알려줘요. 이게 바로 _타입 추론\(type inference\)_이에요. 컴파일러는 모든 함수에 대해 값의 흐름을 다 파악하죠.

## 타입 추론 예제

`toFullName` 함수의 선언을 따라가다 보면, `person`의 `fullName`이 문자열 형태란 걸 알게 되죠.

```elm
toFullName person =
  person.firstName ++ " " ++ person.lastName

fullName =
  toFullName { fistName = "Hermann", lastName = "Hesse" }
```

JavaScript나 Python과 같이, 추가적으로 다른 코드 없이 작성했는데요. 혹시 버그가 보이시나요?

자바스크립트에선 "undefined Hesse" 가 될 거에요. 심지어 에러가 발생하지도 않죠! 이럴때 제발 누군가 이런 버그가 있다고 알려주길 원하게 되죠. 반대로 Elm 컴파일러에선 소스 코드를 가르키며 다음과 같이 알려주죠.

    -- TYPE MISMATCH ---------------------------------------------------------------

    The argument to function `toFullName` is causing a mismatch.

    6│   toFullName { fistName = "Hermann", lastName = "Hesse" }
                    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    Function `toFullName` is expecting the argument to be:

        { …, firstName : … }

    But it is:

        { …, fistName : … }

    Hint: I compared the record fields and found some potential typos.

        firstName <-> fistName

`toFullName`이 잘못된 타입의 인자라는 걸 알아차린 뒤 `fist`를 `first` 대신 잘못 쓴게 아니냐고 에러메시지로 힌트를 알려주죠.

이와 같이 간단한 실수에서 훌륭한 조언을 얻을 수 있고, 심지어 수백개가 넘는 파일에서 공동작업에선 더욱 유용하죠. 복잡해지고 커져가는 것에 대해서 걱정할 필요가 없도록, Elm 컴파일러는 소스코드를 기반으로 모든 것이 잘 되어있는 지 확인해주죠.

타입에 대해 이해를 잘할수록, 컴파일러는 더욱 친절한 조수처럼 느껴줘요. 자 이제 좀 더 배워볼게요!

