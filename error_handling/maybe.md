Maybe

Maybe를 정의하면서 시작하는 게 제일 좋아요. [여기](../types/union_types.md)에서 본 예제와 마찬가지로 모두 유니언 타입이에요. 다음과 같이 정의되죠.

```elm
> type Maybe a = Nothing | Just a

> Nothing
Nothing : Maybe a

> Just
<function> : a -> Maybe a

> Just "hello"
Just "hello" : Maybe String

> Just 1.618
Just 1.618 : Maybe Float
```

`Maybe` 값을 정의 하고 싶으시다면 `Nothing`과 `Just` 를 이용해 만드시면 되요. 즉 데이터를 처리하려면 `case` 표현을 사용해야 된다는 의미에요. 그러면 컴파일러는 이 두가지 가능성에 대해서 모두 보장할 수 있게 되는거죠.

`Maybe` 값을 볼 수 있는 두가지 경우가 있어요.

## 옵셔널 필드\(Optional Fields\)

소셜네트워킹 웹사이트를 운영중이라고 상상해보세요. 사람, 우등을 연결시키면, 사람들은 과장하면서 떠들겠죠. 사실 저희의 최종 목표는 [CIA를 위해 데이터를 최대한 많이 모으는 것](http://www.theonion.com/video/cias-facebook-program-dramatically-cut-agencys-cos-19753)이에요. 때문에 나중엔 사용하기 쉽도록, 사람들이 서로 공유하는 기능을 추가 할 거에요.

자 그럼 일단 간단한 사용자 모델로 시작해 볼게요. 사용자 모델은 이름이 있고, 나이를 옵셔널 필드로 만들었어요.

```elm
type alias User =
  { name : String
  , age : Maybe Int
  }
```

자 이제 Sue가 로그인을 했고, 생일을 제공하지 않기로 했어요.

```elm
sue : User
sue =
  { name = "Sue", age = Nothing }
```

슬프게도 그녀의 생일은 친구들이 축하 해줄수 없을 거에요. 나중에 Tom은 가입할 때 나이를 제공했어요.

```elm
tom : User
tom =
  { name = "Tom", age = Just 24 }
```

이제 그는

Great, that will be nice on his birthday. But more importantly, Tom is part of a valuable demographic! The advertisers will be pleased.

Alright, so now that we have some users, how can we market alcohol to them without breaking any laws? People would probably be mad if we market to people under 21, so let's check for that:

```elm
canBuyAlcohol : User -> Bool
canBuyAlcohol user =
  case user.age of
    Nothing ->
      False

    Just age ->
      age >= 21
```

Now the cool thing is that we are forced to use a `case` to pattern match on the users age. It is actually impossible to write code where you forget that users may not have an age. Elm can make sure of it. Now we can advertise alcohol confident that we are not influencing minors directly! Only their older peers.

## Partial Functions

Sometimes you want a function that gives an answer sometimes, but just does not in other cases.

Let's say Mountain Dew wants to do some ad buys for people ages 13 to 18. Honestly, it is better to start kids on Mountain Dew younger than that, but it is illegal for kids under 13 to be on our site.

So let's say we want to write a function that will tell us a user's age, but only if they are between 13 and 18:

```elm
getTeenAge : User -> Maybe Int
getTeenAge user =
  case user.age of
    Nothing ->
      Nothing

    Just age ->
      if 13 <= age && age <= 18 then
        Just age

      else
        Nothing
```

Again, we are reminded that users may not have an age, but if they do, we only want to return it if it is between 13 and 18. Now Elm can guarantee that anyone who calls `getTeenAge` will have to handle the possibility that the age is out of range.

This gets pretty cool when you start combining it with library functions like [`List.filterMap`](http://package.elm-lang.org/packages/elm-lang/core/latest/List#filterMap) that help you process more data. For example, maybe we want to figure out the distribution of ages between 13 and 18. We could do it like this:

```elm
> alice = User "Alice" (Just 14)
... : User

> bob = User "Bob" (Just 16)
... : User

> users = [ sue, tom, alice, bob ]
... : List User

> List.filterMap getTeenAge users
[14,16] : List Int
```

We end up with only the ages we care about. Now we can feed our `List Int` into a function that figures out the distributions of each number.

