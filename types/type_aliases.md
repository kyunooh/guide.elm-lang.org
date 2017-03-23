# 타입 별명\(Type Aliases\)

타입 별명은 그냥 여러분의 타입 어노테이션을 읽기 쉽게 만들어 준다고 생각하시면 되요.

여러분이 점점 더 복잡한 프로그램을 만들다 보면, 복잡한 데이터로 큰 작업들을 하게 되죠. 예를 들어, 강아지를 위한 트위터를 만들고 운영자를 뽑아야 할지도 모르죠. 여러분은 

As your programs get more complicated, you find yourself working with larger and more complex data. For example, maybe you are making twitter-for-dogs and you need to represent a user. And maybe you want a function that checks to see if a user has a bio or not. You might write a function like this:

```elm
hasBio : { name : String, bio : String, pic : String } -> Bool
hasBio user =
  String.length user.bio > 0
```

That type annotation is kind of a mess, and users do not even have that many details! Imagine if there were ten fields. Or if you had a function that took users as an argument and gave users as the result.

In cases like this, you should create a _type alias_ for your data:

```elm
type alias User =
  { name : String
  , bio : String
  , pic : String
  }
```

This is saying, wherever you see `User`, replace it by all this other stuff. So now we can rewrite our `hasBio` function in a much nicer way:

```elm
hasBio : User -> Bool
hasBio user =
  String.length user.bio > 0
```

Looks way better! It is important to emphasize that _these two definitions are exactly the same_. We just made an alias so we can say the same thing in fewer key strokes.

So if we write a function to add a bio, it would be like this:

```elm
addBio : String -> User -> User
addBio bio user =
  { user | bio = bio }
```

Imagine what that type annotation would look like if we did not have the `User` type alias. Bad!

Type aliases are not just about cosmetics though. They can help you think more clearly. When writing Elm programs, it is often best to _start_ with the type alias before writing a bunch of functions. I find it helps direct my progress in a way that ends up being more efficient overall. Suddenly you know exactly what kind of data you are working with. If you need to add stuff to it, the compiler will tell you about any existing code that is affected by it. I think most experienced Elm folks use a similar process when working with records especially.

> **Note:** When you create a type alias specifically for a record, it also generates a _record constructor_. So our `User` type alias will also generate this function:
>
> ```elm
> User : String -> String -> String -> User
> ```
>
> The arguments are in the order they appear in the type alias declaration. You may want to use this sometimes.



