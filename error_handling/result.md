# Result

`Result` 는 실패\(fail\) 할 수 있는 로직에서 유용해요. 예를 들어서 String을 Int 로 파싱하다가 실패할 수 있겠죠. 문자열에 B 같은 문자가 들어 있을 수 있으니까요. 이런 경우에 다음과 같이 함수를 정의해요.

```elm
String.toInt : String -> Result String Int
```

`String.toInt` 는 문자열을 처리하고 만약 정로 변환될 수 없다면, 문자열로 에러 메시지를 제공해요. 물론 정수로 변환된다면 `Int` 를 반환해요. 즉 `Result String Int` 타입의 의미는 "에러메시지가 문자열이고, 성공한 값은 정수에요"라는 거에요.

가능한 한 구체적으로 만들기 위해, 실제 `Result`의 정의를 살펴보죠. `Maybe` 타입과 비슷해 보이지만 `Result` 타입은 두개의 변수가 있어요.

```elm
type Result error value
  = Err error
  | Ok value
```

두개의 생성자 중 `Err`은 에러를 태그할 때, `Ok`는 성공의 경우 태그해요. 자 실제로 어떤 동작을 하는지 REPL에서 `String.toInt` 를 사용해볼게요.

```elm
> import String

> String.toInt "128"
Ok 128 : Result String Int

> String.toInt "64"
Ok 64 : Result String Int

> String.toInt "BBBB"
Err "could not convert string 'BBBB' to an Int" : Result String Int
```

대부분의 언어에서는 예외를 던지지만, Elm에서는 제대로 동작이 된 건지 데이터를 반환해요. 누군가가 나이를 텍스트 필드에 입력한다고 상상해보세요. 그리고 검증 메시지를 보여줄 거에요.

```elm
view : String -> Html msg
view userInputAge =
  case String.toInt userInputAge of
    Err msg ->
      span [class "error"] [text msg]

    Ok age ->
      if age < 0 then
        span [class "error"] [text "I bet you are older than that!"]

      else if age > 140 then
        span [class "error"] [text "Seems unlikely..."]

      else
        text "OK!"
```

다시 한번 `case`를 사용해서 이상한 숫자가 들어가는 등의 특이 케이스를 다룰 수 있어요.

