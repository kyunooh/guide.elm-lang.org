# 상호 운용\(Interop\)

여러분의 언어를 성공적으로 사용하고 싶다면, 상호 운용성은 엄청나게 중요합니다.

이건 역사적인 사실로 증명되는데요. 왜 C++이 성공적으로 사용되었는 지 생각해보면 C 코드 베이스에서 이동하기 쉬었기 때문이에요. 만약 여러분이 JVM의 입장에서 생각해본다면 Scala와 Clojure가 가지는 장점은 Java의 기존 생태계를 이용할 수 있다는 거에요. 실무에선 아무리 좋은 언어라도, 기존 코드 베이스에 적용할 수 없다면, 사실 적용하기 어렵죠. 이건 브라우저에서도 마찬가지로 어려워요.

이번 단원은 여러분이 브라우저에서 상호 운영이 필요한 경우에 초점을 맞출거에요.

1. JSON을 이용한 외부의 서비스와 상호 작용하는 방법
2. 이미 존재하는 HTML 또는 리액트 앱에 Elm 프로그램을 사용하는 방법
3. 이미 존재하는 자바스크립트 코드와 같이 사용하는 방법

이런 상호 운용에 관해선 다음과 같은 원칙은 지켜야 해요.  
Each of these types of interop are guided by the self-imposed constraints that \(1\) there must be a clear way to introduce Elm gradually into diverse environments and \(2\) Elm should not have to sacrifice its core design principles. In other words, **Elm should be great **_**and**_** it should be possible to use Elm at work.**

## Advice on Introducing Elm

The correct path is to first use Elm in a small experiment. If the experiment goes bad, stop it! If it goes great, expand the experiment a bit more. Then just repeat this process until you are using Elm or not! History seems to suggest that there is no realistic way to translate an existing project into a new language all at once. You have to evolve gradually!

Every company I know of that introduced Elm into an existing codebase did it gradually. You need to make sure it is worth it. You probably need to do some pairing or mentorship to get your teammates comfortable. You may even want to use React as a stepping stone if you are on something before that. Basically, anything you can do to minimize risk and make the process feel gradual will improve your odds. Now none of this is as fun as just switching, but it has the great benefit of actually working out in practice.

