# 타입 별명\(Type Aliases\)

타입 별명은 그냥 여러분의 타입 어노테이션을 읽기 쉽게 만들어 준다고 생각하시면 되요.

여러분이 점점 더 복잡한 프로그램을 만들다 보면, 복잡한 데이터로 큰 작업들을 하게 되죠. 예를 들어, 강아지를 위한 트위터를 만든 뒤 유저의 정보를  보여줘야 할지도 모르죠. 이때 유저의 약력이 있는 지 없는 지 확인하려면 다음과 같이 작성하겠죠.

```elm
hasBio : { name : String, bio : String, pic : String } -> Bool
hasBio user =
  String.length user.bio > 0
```

위 타임 어노테이션은 굉장히 난잡해 보여요. 많은 정보를 담고 있지도 않은데 말이죠! 만약 10개의 필드가 있다고 상상해보세요. 또는 유저의 매개 변수를 사용해서 다시 유저를 결과로 준다고 생각해보세요.

이런 경우에 바로 타입 별명을 사용하면 되요.

```elm
type alias User =
  { name : String
  , bio : String
  , pic : String
  }
```

이 의미는 `User`가 나타날 때마다 해당 내용으로 바꿔쳐 진다는 거에요. 자 이제 `hasBio` 함수를 좀 더 멋지게 바꿔 볼게요.

```elm
hasBio : User -> Bool
hasBio user =
  String.length user.bio > 0
```

아까보다 더 보기 편하죠! 강조하고 싶은 점은 두 선언이 완벽하게 똑같다는 거에요. 별명을 만들면 훨씬 더 적은 키 스트로크로 같은 동작을 시킬 수 있는 거죠.

또 여러분이 약력을 추가 하고 싶으면 다음과 같이 하면 되요.

```elm
addBio : String -> User -> User
addBio bio user =
  { user | bio = bio }
```

만약 User 타입 별칭이 없는 상태로 타입 어노테이션을 작성해봤다고 생각해보세요. 분명히 난잡하죠!

타입 별직은 그냥 

Type aliases are not just about cosmetics though. They can help you think more clearly. When writing Elm programs, it is often best to _start_ with the type alias before writing a bunch of functions. I find it helps direct my progress in a way that ends up being more efficient overall. Suddenly you know exactly what kind of data you are working with. If you need to add stuff to it, the compiler will tell you about any existing code that is affected by it. I think most experienced Elm folks use a similar process when working with records especially.

> **Note:** When you create a type alias specifically for a record, it also generates a _record constructor_. So our `User` type alias will also generate this function:
>
> ```elm
> User : String -> String -> String -> User
> ```
>
> The arguments are in the order they appear in the type alias declaration. You may want to use this sometimes.



