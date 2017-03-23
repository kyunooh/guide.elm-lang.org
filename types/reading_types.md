# 읽기 타입\(Reading Types\)

[언어 살펴보기](../core_language.md) 단원에서 REPL에서 코드를 실행시켜 보았죠. 이번에도 다시 한번 코드를 돌려볼 건데요.  강조하는 부분을 좀 다르게 해서 진행해 볼 예정이에요. 자 `elm repl` 을 터미널에서 다시 한번 입력해주시면, 아래와 갈은 화면이 나올 거에요.

```elm
---- elm repl 0.17.0 -----------------------------------------------------------
 :help for help, :exit to exit, more at <https://github.com/elm-lang/elm-repl>
--------------------------------------------------------------------------------
>
```

## 원시 타입과 리스트\(Primitives and Lists\)

자 간단한 표현문을 입력하고, 어떻게 동작하는 지 확인해 보세요.

```elm
> "hello"
"hello" : String

> not True
False : Bool

> round 3.1415
3 : Int
```

이 예제에서는 REPL이 값의 타입이 무엇인지 함께 알려줘요. `"hello"` 는 `String`이고, `3`은 `Int`라고 알려주죠. 복잡하지 않아요.

이번엔 값들이 다른 타입을 가진 리스트를 만들어 볼게요.

```elm
> [ "Alice", "Bob" ]
[ "Alice", "Bob" ] : List String

> [ 1.0, 8.6, 42.1 ]
[ 1.0, 8.6, 42.1 ] : List Float

> []
[] : List a
```

첫번째 경우엔 `List`가 `String` 값을 가지죠. 두번째 `List`엔 `Float`를 가지고요. 세번째 경우엔 빈 리스트 인데, 이 경우엔 어떤 종류의 값들을 가지는 지 사실 알 수가 없죠. List a의 의미는 "난 내가 리스트인 걸 알아 하지만 그 어떤 것으로든 채울수 있어."에요. 소문자 `a`는 타입 변수\(type variable\)라고 부르는데, 이 의미는 특정 유형으로 고정시키지 않겠다는 거에요. 즉, 어떻게 사용하느냐에 따라 타입이 바뀔 수 있다는 거죠.

## 함수\(Functions\)

자 이제 몇몇 함수의 타입을 봐볼게요.

```elm
> import String
> String.length
<function> : String -> Int
```

`String.length` 함수는 `String -> Int` 타입을 가져요. 이 의미는 `String` 매개변수를 받아서 정수형으로 반환한다는 의미에요. 자 매개변수를 넘겨보죠.

```elm
> String.length "Supercalifragilisticexpialidocious"
34 : Int
```

초기의 표현식이 어떻게 `Int` 타입의 결과가 나왔는지 이해하는 게 중요해요. `String -> Int` 함수는 `String` 을 매개변수로 받고, 결과는 `Int` 인거죠.

`String` 이 아니면 어떻게 될까요?

```elm
> String.length [1,2,3]
-- error!

> String.length True
-- error!
```

`String -> Int` 함수는 _꼭_ `String` 매개변수여야 합니다!

### 익명 함수\(Anonymous Functions\)

Elm은 _익명 함수_로 불리는 특징이 있어요. 다음과 같이 이름이 없는 함수를 만들 수 있죠.

```elm
> \n -> n / 2
<function> : Float -> Float
```

백슬래쉬\(backslash\)와 화살표\(arrow\)사이는 함수에 매개변수들을 열거해요. 그리고 화살표 오른쪽 부분은 매개변수를 이용해 어떤 동작을 할 지 적는거죠. 이 예제에서는, "난 몇개의 인자를 취할 거고, 이 인자를 `n` 이라 부를거야. 그리고 난 이걸 2로 나눌래."라고 말하는 거에요.

익명 함수를 직접 사용할 수 있어요. 아래는 `128`을 매개변수로 두고, 익명 함수를 사용한 예제에요.

We can use anonymous functions directly. Here is us using our anonymous function with `128` as the argument:

```elm
> (\n -> n / 2) 128
64 : Float
```

`Float -> Float` 함수여서, `Float`를 매개변수로 주고, 결과는 또다른 `Float`이 돼요.

> **알고 넘어가기:** 백슬래시로 시작하는 이유는 lambda `λ` 처럼 보이기 때문이에요. \(억지스러워 보이는 건 착각\) 제대로 고려하지 않고 만든 것 같지만, Elm과 같은 뿌리의 언어들이 갖는 역사속에서 만들어 진거에요.
>
> 또한 `(\n -> n / 2) 128`을 작성할 때, 익명 함수가 괄호로 둘러 쌓여 있다는 게 중요해요. Elm은 화살표 뒷부분을 다 읽어들이고, 괄호가 함수의 끝을 알려주는 기준점이 되는거에요.

### 이름을 갖는 함수\(Named Function\)

같은 방법으로 값에 이름을 줄 수도 있어요. 익명함수에 이름을 줄수도 있죠. 유연하죠!

```elm
> oneHundredAndTwentyEight = 128.0
128 : Float

> half = \n -> n / 2
<function> : Float -> Float

> half oneHundredAndTwentyEight
64 : Float
```

결국엔, 명명되지 않은 것과 같이 동작해요. 여러분이 `Float -> Float` 함수를 썼다면, `Float` 를 넘겨주고 다른 `Float`을 최종적으로 받죠.

모든 함수들이 이렇게 정의 되어지는 건, 사실 비밀이 숨어있기 때문인데요! 다음을 보시면 아시겠지만 사실 익명 함수에 이름만 주는 것 뿐이죠.

```elm
> half n = n / 2
<function> : Float -> Float
```

다음과 같은 형태로도 생각할 수 있어요.

```elm
> half = \n -> n / 2
<function> : Float -> Float
```

함수의 매개변수가 얼마든지 상관 없이 쓸 수 있어요. _복수_의 매개변수를 사용해서 작성해볼게요.

```elm
> divide x y = x / y
<function> : Float -> Float -> Float

> divide 3 2
1.5 : Float
```

되는 것 같긴 한데, 왜 `divide`타입에 화살표가 두개 일까요?! 일단 "모든 매개변수는 화살표로 나눠지고, 마지막은 함수의 결과"라고 생각하시면 편해요.  `divide`는 두개의 매개변수를 받고 Float를 리턴하는 거죠.

왜 `divide`의 화살표가 두개인지 이해를 제대로 하기 위해선, 익명함수가 어떻게 변형되어 정의 되는 지 아는 게 도움이 되요.

```elm
> divide x y = x / y
<function> : Float -> Float -> Float

> divide x = \y -> x / y
<function> : Float -> Float -> Float

> divide = \x -> (\y -> x / y)
<function> : Float -> Float -> Float
```

위에 세개는 모두 동일해요. 그냥 매개변수를 하나씩 익명변수로 바꿔본 건데요. `divide 3 2` 와 같은 같은 표현식은 사실 다음과 같은 과정을 거쳐서 동작해요.

```elm
  divide 3 2
  (divide 3) 2                 -- Step 1 - 암시적으로 괄호가 추가됨
  ((\x -> (\y -> x / y)) 3) 2  -- Step 2 - `divide`를 확장
  (\y -> 3 / y) 2              -- Step 3 - x 를 3으로 변경
  3 / 2                        -- Step 4 - 3을 2로 변경
  1.5                          -- Step 5 - 그냥 수학적으로 계산
```

`divide`를 확장한 뒤, 매개변수를 하나씩 넘겨주어서, 각각 `x`와 `y`를 바꿔 치는 거죠.

자 타입이 동작하는 방법에 대해서 더 자세히 알아 볼게요. `step 3`  의 코드를 따라 쳐보면,

```elm
> (\y -> 3 / y)
<function> : Float -> Float
```

`half`처럼 `Float -> Float` 함수죠. 좀 더 복잡한 `step 2` 도 봐보죠.

```elm
> (\x -> (\y -> x / y))
<function> : Float -> Float -> Float
```

자, `\x -> ...` 같은 형태는 `Float -> ...`으로 동작하는 걸 알고 있죠. 또한 `(\y -> x / y)`도 `Float -> Float` 타입을 가지겠죠.

여러분이 만약 실제로 괄호를 쓴다면, `Float -> (Float -> Float)`이 될거에요. 여기서 여러분이 매개변수를 한개씩 넘기면, 즉 `x`가 매개변수로 변경 되었을 때 실제론 이미 다른 함수가 되어버리는 거에요.

이건 Elm에 존재하는 함수에서도 마찬가지에요.

```elm
> import String
> String.repeat
<function> : Int -> String -> String
```

여러분이 매개변수를 한개씩 넘기기 때문에 이건 사실 `Int -> (String -> String)`이죠.

Elm에선 모두 함수가 이런식으로 동작하기 때문에, 여러분은 매개변수를 한번에 모두 넘겨줄 필요가 없어요. 다음과 같이 쓸 수 있다는 거죠.

```elm
> divide 128
<function> : Float -> Float

> String.repeat 3
<function> : String -> String
```

이건 부분 적용\(partial application\)이라고 부르는데요. [`|>` 연산자](http://package.elm-lang.org/packages/elm-lang/core/latest/Basics#|>)를 이용해서 함수를 연결하여 사용할 수 있어요. 이게 함수 타입이 많은 화살표를 가진 이유에요!

## Type Annotations

So far we have just let Elm figure out the types, but it also lets you write a _type annotation_ on the line above a definition if you want. So when you are writing code, you can say things like this:

```elm
half : Float -> Float
half n =
  n / 2

divide : Float -> Float -> Float
divide x y =
  x / y

askVegeta : Int -> String
askVegeta powerLevel =
  if powerLevel > 9000 then
    "It's over 9000!!!"

  else
    "It is " ++ toString powerLevel ++ "."
```

People can make mistakes in type annotations, so what happens if they say the wrong thing? Well, the compiler does not make mistakes, so it still figures out the type on its own. It then checks that your annotation matches the real answer. In other words, the compiler will always verify that all the annotations you add are correct.

> **Note:** Some folks feel that it is odd that the type annotation goes on the line above the actual definition. The reasoning is that it should be easy and noninvasive to add a type annotation _later_. This way you can turn a sloppy prototype into higher-quality code just by adding lines.



