# 유니온 타입\(Union Types\)

많은 언어들 이상한 형태의 데이터를 처리하다가 문제가 발생하죠.내부에서 제공되는 타입들로  모든 것을 표현해야 하죠. 그 때문에 여러분 스스로 `null` 이나 논리값, 문자열 등등을 사용하다가 오류가 발생하기가 쉽죠.

Elm의 유니온 타입은 복잡한 자료를 좀 더 자연스럽게 다룰 수 있게 해줘요. 두가지 구체적인 예시를 살펴보면서 어떻게 유니온 타입을 사용하는 지 알아볼게요.

더 알아보기**:** 유니온 타입은 때때로 [태그가 지정된 \(tagged unions\)](https://en.wikipedia.org/wiki/Tagged_union)라고 불려요. 몇몇 커뮤니티에선 [ADT](https://en.wikipedia.org/wiki/Algebraic_data_type)라고 부르죠.

## 투두 리스트 분류하기\(Filtering a Todo List\)

> **문제:  **작업 관리 할  [투두 리스트\(todo iist\)](http://evancz.github.io/elm-todomvc/)를 만드는 중이에요. 세개의 뷰를 만들 건데요. 모든 작업들을 보여 주는 뷰, 진행중인 작업 보여주는 뷰, 완료된 작업만을 보여주는 뷰로 구성할 거에요. 어떻게 세개의 상태를 표현할 수 있을까요?

Elm에서 일반적이지 않은 형태의 데이터를 다룰때는, 유니언 타입을 사용하게 되요. 이 경우엔 세개의 값을 가지는 `Visibillity`를 만들거에요.

```elm
> type Visibility = All | Active | Completed

> All
All : Visibility

> Active
Active : Visibility

> Completed
Completed : Visibility
```

이제 우리는 세가지 경우를 정의 했고, `keep` 함수를 만들어 적당하게 작업을 걸러볼 거에요. 다음과 같이 작성하시면 되요.

```elm
type alias Task = { task : String, complete : Bool }

buy : Task
buy =
  { task = "Buy milk", complete = True }

drink : Task
drink =
  { task = "Drink milk", complete = False }

tasks : List Task
tasks =
  [ buy, drink ]


-- keep : Visibility -> List Task -> List Task

-- keep All tasks == [buy,drink]
-- keep Active tasks == [drink]
-- keep Complete tasks == [buy]
```

`keep` 함수는 첫번째 매개 변수에 따라서 작업을 다양하게 나누어요. `if`와 비슷한 `case` 표현식을 사용해서 작성해 볼게요.

```elm
keep : Visibility -> List Task -> List Task
keep visibility tasks =
  case visibility of
    All ->
      tasks

    Active ->
      List.filter (\task -> not task.complete) tasks

    Completed ->
      List.filter (\task -> task.complete) tasks
```

`case` 는 `visibility`의 구조를 보고, `All`이라면 그냥 모든 작업들을 보여주고, `Active`라면 진행중인 작업이면서 완료되지 않은 작업들, `Completed`라면 완료된 작업들만 보여줘요.

case 표현식의 장점은 각 가지들이 컴파일러에 의해 확인된다는 거에요. 이건 다음과 같은 장접을 같죠.

1. 만약 Compleet 같은 오타가 났을 때, 이에 대한 힌트를 얻을 수 있죠.
2. case 처리를 깜빡했을 때도 컴파일러가 알려줘요.

네번째 `Visibility` 값으로 Recent를 추가한다면, 컴파일러는 모든 `case` 표현식에서 `Visibility`값을 찾아 새로운 것이 추가되었다고 알려주겠죠! 이건 `Visibility`를 수정하거나 확장할 때, 기존 코드를 수정할 때 생기는 버그의 잠재 위험을 없앨 수 있다는 거에요.

> **생각해보기:** 자바스크립트에선 이런 문제가 어떻게 처리될 지 상상해보세요. 세개의 문자열? null이 될수도 있는 논리값? `keep` 함수는 어떻게 정의 될까요? 코드를 추가할 때 생길 문제를 대비하여 어떤 종류의 테스트 코드를 짜야할까요?

## 익명 사용자\(Anonymous Users\)

> **문제:** 채팅방이 있는데요. 몇몇은 로그인을 하여 사용하고 몇몇은 익명 사용자에요. 어떻게 사용자들을 표현해야 할까요?

자 다시 일반적이지 않은 형태의 자료네요. 유니언 타입을 사용해야겠어요. 이 경우에는 익명 유저와 이름이 있는 유저로 구분할게요.

```elm
> type User = Anonymous | Named String

> Anonymous
Anonymous : User

> Named
<function> : String -> User

> Named "AzureDiamond"
Named "AzureDiamond" : User

> Named "abraham-lincoln"
Named "abraham-lincoln" : User
```

`User` 타입을 만들고 각각 `Anonymous`와 `Named` 라고 이름을 붙입니다. 이제 여러분인 `User` 타입을 사용하려면 이 두개중 하나를 선택해서 사용해야 겠죠. 이제 모든 User의 값들은 다음과 같은 형식을 가진다고 보장할 수 있어요.

```elm
  Anonymous
  Named "AzureDiamond"
  Named "abraham-lincoln"
  Named "catface420"
  Named "Tom"
  ...
```

이제 우리는 사용자를 표현할 수 있어요. 자 이젠 사용자들의 포스트 옆에 표시될 사진들을 가져와 볼게요. 다시 한번 `User`타입을 이용한 `case` 표현식을 사용해야되요.

```elm
userPhoto : User -> String
userPhoto user =
  case user of
    Anonymous ->
      "anon.png"

    Named name ->
      "users/" ++ name ++ ".png"
```

위와 같이 `Anonymous` 일 때 보여줄 임시 사진과 `Named` 일때 가져올 사진 두가지 가능성이 있게 되요. 위 `case` 는 이전보다 간단하죠. 두번째 분기엔 `name` 이라는 소문자 변수가 있어요. 이 의미는 `Named "AzureDiamond"`와 같은 값이 있을 때 `name` 변수는 "AzureDiamond"가 되는 것이에요. 이런 걸 패턴 매칭\(pattern matching\)이라고 불러요.

자 이제 어느정도 유저가 있고 그들의 사진이 있는 채팅방을 상상해보세요.

```elm
activeUsers : List User
activeUsers =
  [ Anonymous, Named "catface420", Named "AzureDiamond", Anonymous ]

photos : List String
photos =
  List.map userPhoto activeUsers

-- [ "anon.png", "users/catface420.png", "users/AzureDiamond.png", "anon.png" ]
```

`User` 와 같은 타입을 만들면 좋은 점은, 코드 베이스에서 익명인 유저가 있을 있다는 경우를 "잊어버릴 수" 없다는 거에요. 유저를 사용하는 누구나 `case` 에서 이 정보를 알아낼 수 있어요. 또한 모든 `case` 에서 모든 가능성에 대해서 보장해 주죠!

> **더 생각해보기:** 다른 언어에서는 이런 문제를 어떻게 해결 할 수 있을지 생각해보세요. 문자열이 이면 빈 문자열이면 익명을 의미할까요? 문자열이 null이면 어떻게 할까요? 이런 모든 케이스에 대해서 테스팅하는 게 과연 가능할까요?

## 위젯 대시보드\(Widget Dashboard\)

> **문제:** 여러분은 서로 다른 위젯 세개를 갖는 대시보드를 만드는 중이에요. 하나는 최근 로그를 보여주고, 하나는 타임 플롯을 보여주고, 하나는 스캐터 플롯을 보여줄때, 이 위젯들을 어떻게 표현해야 할까요?

좋아요, 이제 좀 더 깊게 들어가 볼게요. Elm에선 각각의 상황의 문제를 개별적으로 생각하고 문제 해결을 시작해요.\(이상해 보이겠지만 여러분이 좀 더 경험이 많아지다면, 점점 작은 부분으로 나누고 재사용 가능한 Elm 프로그램을 만들어 나가게 될거에요.\) 그렇기 때문에 우리는 세 시나리오를 각각 만들거에요. `view` 함수 또한 각각 HTML이나 SVG와 같은 것으로 변환되겠죠.

```elm
type alias LogsInfo =
  { logs : List String
  }

type alias TimeInfo =
  { events : List (Time, Float)
  , yAxis : String
  }

type alias ScatterInfo =
  { points : List (Float, Float)
  , xAxis : String
  , yAxis : String
  }

-- viewLogs : LogsInfo -> Html msg
-- viewTime : TimeInfo -> Html msg
-- viewScatter : ScatterInfo -> Html msg
```

여기서 요점은 세가지 경우에 대해 모든 helper 함수가 각각 완벽하게 독립적으로 만들어졌다는 거에요. 누군가 나중에 "스캐터 플롯을 보여줄 멋진 방법이 필요해"라고 말했을 때 그냥 코드 일부를 사용하면 되죠.

하지만 진짜 문제는 "특정 경우에 이 각각의 것을 함께 쓸 수 있을까요?"에요.

다시 한번 말씀하지만 다른 타을 유니언 타입으로 집어 넣을 수 있어요!

```elm
> type Widget = Logs LogsInfo | TimePlot TimeInfo | ScatterPlot ScatterInfo

> Logs
<function> : LogsInfo -> Widget

> TimePlot
<function> : TimeInfo -> Widget

> ScatterPlot
<function> : ScatterInfo -> Widget
```

자  `Widget` 타입은 해당 생성자 함수를 이용한 곳에서만 사용할 수 있어요. 생성자가 태깅\(tagging\)되었다고 생각하시고, 런타임과는 분리되었다고 말할 수 있죠. 이제 다음과 같이 작성해서 위젯을 렌더 할 수 있어요.

```elm
view : Widget -> Html msg
view widget =
  case widget of
    Logs info ->
      viewLogs info

    TimePlot info ->
      viewTime info

    ScatterPlot info ->
      viewScatter info
```

이 방식의 좋은 점중 하나는 어떤 종류의 위젯을 지원하는 지에 대한 의문을 가질 필요가 없다는 거에요. 정확히 세개 뿐이죠. 만약 다른 누군가가 네번째를 추가한다면, `Widget` 타입을 수정하면 되요. 이 의미는 다른 팀의 누군가가 여러분의 코드를 망치는 것에 대해 놀랄 일이 사라진 다는 거에요.

> **알고 넘어가기:**
>
> * 각각의 문제를 먼저 해결하세요.
> * 유니언 타입을 이용해서 해결책을 모으세요.
> * 유니언 타입을 만들면 생성자가 많이 생겨요. Creating a union type generates a bunch of _constructors_.
> * 이 생성자들은 런타임에서 데이터를 구분할 수 있도록 태깅해요. 
> * `case` 표현식을 이용하면 태그를 기반으로 데이터를 구분할수 있어요.
>
> 위와 같은 방법으로 게임을 만든다면, 서로 다른 특성만 가진 사람들로만 이루어 져도 괜찮아요. Goombas가 한쪽 작업을 하고 있을 때 Koopa Troopas는 완전 다른 일을 해요. 하지만 각각 서로의 문제들이 해결되면, 유니언 타입을 이용해서 이들을 묶을 수 있어요.

## 연결 리스트\(Linked Lists\)

> **문제:** 여러분은 속도가 느려지면 터지는 버스에 같혀 있어요. 버스에 타고 있는 사람들을 구할 유일한 방법은 Elm에서 연결 리스트를 구현하는 것 뿐이에요. 어서 서두르세요. 연료가 떨어지고 있어요!

네네, 이번엔 문제가 좀 이상해 보이긴 하지만 그게 중요한 게 아니에요. 유니온 타입을 좀 더 급진적으로 사용해볼 거라는 게 중요해요!

[연결 리스트](https://en.wikipedia.org/wiki/Linked_list)는 값들이 연속적으로 있는 형태에요. 연결 리스트는 비어있거나, 값과 리스트가 더 있을 수도 있어요. 때문에 이번에 만드는 리스트도 비어있거나 값들이 있거나 할 거에요. 자 정수로 이루어진 리스트를 봅시다.

```elm
> type IntList = Empty | Node Int IntList

> Empty
Empty : IntList

> Node
<function> : Int -> IntList -> IntList

> Node 42 Empty
Node 42 Empty : IntList

> Node 64 (Node 128 Empty)
Node 64 (Node 128 Empty) : IntList
```

이번엔 두가지 새로운 것이데요. 보이는

1.  `Node` 생성자는 한개가 아닌 두개의 매개변수를 사용하지만 괜찮아요. 사실 매개변수는 원하는 만큼 사용할 수 있어요.
2. 유니언 타입은 재귀적이에요. 유니언 타입을 사용한다면 `IntList` 안에 또다른 `IntList`를 넣어도 괜찮아요.

만들어진 `IntList` 장점 중 하나는 검증된 연결 리스트만 빌드된다는 거에요. 모든 연결 리스트는 `Empty`로 시작하고, 새로운 값을 추가할 수 있는 방법은 `Node`를 사용하는 것 뿐이에요.

이건 리스트를 사용할 때도 좋아요. 리스트의 숫자 합계를 계산해 볼게요. 다른 유니언 타입과 마찬가지로, `case` 를 통해서 가능한 시나리오를 다룰 수 있어요.

```elm
sum : IntList -> Int
sum numbers =
  case numbers of
    Empty ->
      0

    Node n remainingNumbers ->
      n + sum remainingNumbers
```

만약 Empty값을 받았을 때 합계는 0이겠죠. 만약 Node라면 남아있는 값들의 합에 첫번째 요소를 더해줘요. 즉 `(sum (Node 1 (Node 2 (Node 3 Empty))))`와 같은 표현식으로 동작하는 거에요. 다음과 같이 계산되는 거죠.

```elm
  sum (Node 1 (Node 2 (Node 3 Empty)))
  1 + sum (Node 2 (Node 3 Empty))
  1 + (2 + sum (Node 3 Empty))
  1 + (2 + (3 + sum Empty))
  1 + (2 + (3 + 0))
  1 + (2 + 3)
  1 + 5
  6
```

각각의 라인들을 보시면, 한단계씩 계산되어지는 걸 알 수 있죠. `sum`을 호출하게 되면 값이 `Node` 인지 `Empty` 인지에 따로 변환되는 거에요.

> **더 알아보기:** 방금 처음으로 재귀함수를 작성해 보았어요! `sum`은 자기 자신을** **다시 호출하죠. 재귀 함수와 같은 사고방식을 갖는 것은 어렵지만 여러분들과 이 이상한 방법을 공유해보고 싶었어요. **그냥 이미 다 아는 척을 하세요.**
>
> 전 항상 `case`를 작성할 때 모든 경우들을 한번에 채우지 않고, 각각의 경우를 하나씩 처리해요. `sum`을 보면 `Emtpy ->` 를 먼저 0이겠구나 생각한 뒤 `Node n remainingNumbers ->` 의 경우를 살펴보고 생각해봐요. 숫자와 리스트 `sum` 함수가 완벽하게 동작한다는 걸 알게 되면, 이제 그냥 숫자를 추가해서 사용할 수 있게 되죠.

## 제네릭 데이터 구조\(Generic Data Structures\)

> **문제: **방금은 정수에 대해서만 동작하는 연결 리스트를 봤었는데요. 사실 반쪽짜리 리스트라고 할 수 있죠. 모드 종류의 값에 대응하는 연결 리스트는 어떻게 만들 수 있을까요?

자 정의 할 때 _타입 변수\(type variable\)_이 들어간다는 것 빼곤 모든게 비슷해요.

```elm
> type List a = Empty | Node a (List a)

> Empty
Empty : List a

> Node
<function> : a -> List a -> List a

> Node "hi" Empty
Node "hi" Empty : List String

> Node 1.618 (Node 6.283 Empty)
Node 1.618 (Node 6.283 Empty) : List Float
```



The fancy part comes in the `Node` constructor. Instead of pinning the data to `Int` and `IntList`, we say that it can hold `a` and `List a`. Basically, you can add a value as long as it is the same type of value as everything else in the list.

Everything else is the same. You pattern match on lists with `case` and you write recursive functions. The only difference is that our lists can hold anything now!

> **Exercise:** This is exactly how the `List` type in Elm works, so take a look at [the `List` library](http://package.elm-lang.org/packages/elm-lang/core/latest/List) and see if you can implement some of those functions yourself.

## Additional Examples

We have seen a couple scenarios, but the best way to get more comfortable is to use union types more! So here are two examples that are kind of fun.

### Binary Trees

[Binary trees](https://en.wikipedia.org/wiki/Binary_tree) are almost exactly the same as linked lists:

```elm
> type Tree a = Empty | Node a (Tree a) (Tree a)

> Node
<function> : a -> Tree a -> Tree a -> Tree a

> Node "hi" Empty Empty
Node "hi" Empty Empty : Tree String
```

A tree is either empty or it is a node with a value and two children. Check out [this example](http://elm-lang.org/examples/binary-tree) for more info on this. If you can do all of the exercises at the end of that link, consider yourself a capable user of union types!

### Languages

We can even model a programming language as data if we want to go really crazy! In this case, it is one that only deals with [Boolean algebra](https://en.wikipedia.org/wiki/Boolean_algebra#Operations):

```elm
type Boolean
    = T
    | F
    | Not Boolean
    | And Boolean Boolean
    | Or Boolean Boolean

true = Or T F
false = And T (Not T)
```

Once we have modeled the possible values we can define functions like `eval` which evaluates any `Boolean` to `True` or `False`. See [this example](http://elm-lang.org/examples/boolean-expressions) for more about representing boolean expressions.

