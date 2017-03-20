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

`String.length` 함수는 `String -> Int` 타입을 가져요. 이 의미는 `String` 인자를 받아서 정수형으로 반환한다는 의미에요. 자 인자를 넘겨보죠.

The function `String.length` has type `String -> Int`. This means it _must_ take in a `String` argument, and it will definitely return an integer result. So let's try giving it an argument:

```elm
> String.length "Supercalifragilisticexpialidocious"
34 : Int
```

The important thing to understand here is how the type of the result `Int` is built up from the initial expression. We have a `String -> Int` function and give it a `String` argument. This results in an `Int`.

What happens when you do not give a `String` though?

```elm
> String.length [1,2,3]
-- error!

> String.length True
-- error!
```

A `String -> Int` function _must_ get a `String` argument!

### Anonymous Functions

Elm has a feature called _anonymous functions_. Basically, you can create a function without naming it, like this:

```elm
> \n -> n / 2
<function> : Float -> Float
```

Between the backslash and the arrow, you list the arguments of the function, and on the right of the arrow, you say what to do with those arguments. In this example, it is saying: I take in some argument I will call `n` and then I am going to divide it by two.

We can use anonymous functions directly. Here is us using our anonymous function with `128` as the argument:

```elm
> (\n -> n / 2) 128
64 : Float
```

We start with a `Float -> Float` function and give it a `Float` argument. The result is another `Float`.

> **Notes:** The backslash that starts an anonymous function is supposed to look like a lambda `λ` if you squint. This is a possibly ill-conceived wink to the intellectual history that led to languages like Elm.
>
> Also, when we wrote the expression `(\n -> n / 2) 128`, it is important that we put parentheses around the anonymous function. After the arrow, Elm is just going to keep reading code as long as it can. The parentheses put bounds on this, indicating where the function body ends.

### Named Functions

In the same way that we can name a value, we can name an anonymous function. So rebellious!

```elm
> oneHundredAndTwentyEight = 128.0
128 : Float

> half = \n -> n / 2
<function> : Float -> Float

> half oneHundredAndTwentyEight
64 : Float
```

In the end, it works just like when nothing was named. You have a `Float -> Float` function, you give it a `Float`, and you end up with another `Float`.

Here is the crazy secret though: this is how all functions are defined! You are just giving a name to an anonymous function. So when you see things like this:

```elm
> half n = n / 2
<function> : Float -> Float
```

You can think of it as a convenient shorthand for:

```elm
> half = \n -> n / 2
<function> : Float -> Float
```

This is true for all functions, no matter how many arguments they have. So now let's take that a step farther and think about what it means for functions with _multiple_ arguments:

```elm
> divide x y = x / y
<function> : Float -> Float -> Float

> divide 3 2
1.5 : Float
```

That seems fine, but why are there _two_ arrows in the type for `divide`?! To start out, it is fine to think that "all the arguments are separated by arrows, and whatever is last is the result of the function". So `divide` takes two arguments and returns a `Float`.

To really understand why there are two arrows in the type of `divide`, it helps to convert the definition to use anonymous functions.

```elm
> divide x y = x / y
<function> : Float -> Float -> Float

> divide x = \y -> x / y
<function> : Float -> Float -> Float

> divide = \x -> (\y -> x / y)
<function> : Float -> Float -> Float
```

All of these are totally equivalent. We just moved the arguments over, turning them into anonymous functions one at a time. So when we run an expression like `divide 3 2` we are actually doing a bunch of evaluation steps:

```elm
  divide 3 2
  (divide 3) 2                 -- Step 1 - Add the implicit parentheses
  ((\x -> (\y -> x / y)) 3) 2  -- Step 2 - Expand `divide`
  (\y -> 3 / y) 2              -- Step 3 - Replace x with 3
  3 / 2                        -- Step 4 - Replace y with 2
  1.5                          -- Step 5 - Do the math
```

After you expand `divide`, you actually provide the arguments one at a time. Replacing `x` and `y` are actually two different steps.

Let's break that down a bit more to see how the types work. In evaluation step \#3 we saw the following function:

```elm
> (\y -> 3 / y)
<function> : Float -> Float
```

It is a `Float -> Float` function, just like `half`. Now in step \#2 we saw a fancier function:

```elm
> (\x -> (\y -> x / y))
<function> : Float -> Float -> Float
```

Well, we are starting with `\x -> ...` so we know the type is going to be something like `Float -> ...`. We also know that `(\y -> x / y)` has type `Float -> Float`.

So if you actually wrote down all the parentheses in the type, it would instead say `Float -> (Float -> Float)`. You provide arguments one at a time. So when you replace `x`, the result is actually _another function_.

It is the same with all functions in Elm:

```elm
> import String
> String.repeat
<function> : Int -> String -> String
```

This is really `Int -> (String -> String)` because you are providing the arguments one at a time.

Because all functions in Elm work this way, you do not need to give all the arguments at once. It is possible to say things like this:

```elm
> divide 128
<function> : Float -> Float

> String.repeat 3
<function> : String -> String
```

This is called _partial application_. It lets us use [the `|>` operator](http://package.elm-lang.org/packages/elm-lang/core/latest/Basics#|>) to chain functions together in a nice way, and it is why function types have so many arrows!

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



