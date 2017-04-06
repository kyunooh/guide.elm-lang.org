# Maybe

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

그는 생일에 축하받을 수 있을 거에요. 하지만 더 중요한 건, 톰이 인구 통계를 낼 때 속하는 사람중 한명이란 거죠! 광고주들이 분명 좋아할 거에요.

좋아요, 이제 우린 어느정도 사용자를 확보했어요. 자 어떻게하면 법을 위반하지 않고 술을 판매할 수 있을까요? 21살 이하의 사람들에게 술을 판다면 사람들은 화를 낼 거에요. 자 다음과 같이 확인해 보죠.

```elm
canBuyAlcohol : User -> Bool
canBuyAlcohol user =
  case user.age of
    Nothing ->
      False

    Just age ->
      age >= 21
```

자 이제  `case` 를 이용해 사용자 나이에 대해 패턴 매칭을 할 수 있어요. Elm에선 "유저의 나이가 만약 없을 수도 있다"라는 생각을 하면서 코드를 짤 필요가 없는 거죠. 미성년자들에겐 절대 직접 영향을 끼치지 않을 거에요! 술을 마실 수 있는 사람한테만 광고가 가겠죠.

## 부분 함수\(Partial Functions\)

때로는 답변을 주는 함수를 원하지만, 아닌 경우도 있죠.

마운틴 듀가 13살에서 18살 사이의 사람들에 구매 광고를 한다고 가정해 볼게요. 솔직히 더 어린 아이들이 들어와도 상관 없지만 13살 미만의 어린이가 들어오는 건 불법이에요.

자 이제 나이를 받는 함수를 작성할 건데요. 대신 13살에서 18살 사이여야만 해요.

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

다시 한번 상기시켜 드리자면, 사용자들의 나이 항목이 없을 수도 있습니다. 그리고 나이 항목이 있다면 13살에서 18살 사이에 사이만 받아 보고 싶은 거에요. 누가 getTeenAge를 호출하는 지 상관없이, Elm은 범위를 벗어나는 모든 가능성에 대해서 다루고 있다고 보장할 수 있어요.

이건[`List.filterMap`](https://www.gitbook.com/book/kyunooh/elm/edit#)와 같은 라이브러리의 함수를 사용할 때, 데이터를 다루기 편하도록 도와줘요. 예를 들어 13살에서 18살 사이에 있는 사용자들의 나이만 가져오고 싶다면, 다음과 같이 할 수 있죠.

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

우리가 신경쓰고 있는 나이만 추출 되었네요. 이제 `List Int` 를 함수에 집어넣어서 각 나이에 따른 분포도를 알아내거나 할 수도 있겠죠!

