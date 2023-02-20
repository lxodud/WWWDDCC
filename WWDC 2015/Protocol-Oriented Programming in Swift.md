# Protocol-Oriented Programming in Swift

[Protocol-Oriented Programming in Swift - WWDC15 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2015/408/)

![](https://velog.velcdn.com/images/xoxo0223/post/54ab7b50-27b1-46bd-a311-bac909f507c0/image.png)


이 세션 동안은 프로그래밍에 대한 평소의 생각을 접어두는 시간이다.

여기서 하는건 굉장히 어려운데, 시간을 할애할 가치가 있다.

Swift 디자인의 핵심에 있는 테마에 대해 이야기하고 모든 것을 바꿀 수 있는 잠재력이 있는 프로그래밍 방법을 소개할꺼임

![](https://velog.velcdn.com/images/xoxo0223/post/5b71c60a-f47d-4ddd-8fc6-d42d5332ca49/image.png)


OOP is awesome.

class로 뭘할 수 있냐??

- Encapsulation
    - 연관된 데이터와 작업을 그룹화할 수 있다
- Access Control
    - 코드 내부와 외부를 구분하는 벽을 만들 수 있다.
    - 이것은 우리가 불변성을 유지할 수 있게 해주는 것이다.
- Abstraction
    - 클래스를 사용하여 window or commnication channel과 같은 관련된 아이디어를 나타낸다.
- Namespace
    - 소프트웨어가 성장함에 따라 충둘을 방지하는 데 도움이 되는 네임스페이스를 제공한다.
- Expressive Syntax
    - 놀라운 표현 구문을 가지고 있다.
    - 따라서 메서드 호출과 프로퍼티를 작성하고 함께 연결할 수 있다.
    - subscript를 만들 수 있다.
    - computed property를 만들 수 있다.
- Extensibility
    - 클래스는 확장성을 위해 열려있다.
    - 클래스 작성자가 내가 필요한 것을 빼면 나중에 추가할 수 있다.

이것들을 함께 사용하면 복잡성을 관리할 수 있음

이것이 프로그래밍의 주요 과제이다.

![](https://velog.velcdn.com/images/xoxo0223/post/2d339d11-2142-4fd1-bd20-597c88df5e41/image.png)


Swift에서 이름을 지정할 수 있는 모든 타입은 first class citizen이며 이러한 모든 기능을 활용할 수 있다.

근데 상속은 못하는데?

![](https://velog.velcdn.com/images/xoxo0223/post/e4083690-1d47-4310-a12e-89e10934e612/image.png)


이러한 구조를 통해 코드 공유와 세분화된 사용자 지정을 모두 가능하게 하는 방법에 대해 구체적으로 생각하게 되었다.

![](https://velog.velcdn.com/images/xoxo0223/post/6ce320ea-3dbb-4c47-8db9-d971adafffcb/image.png)


예를 들어 슈퍼클래스는 복잡한 논리로 실질적인 메서드를 정의할 수 있고 서브 클래스는 슈퍼클래스가 수행하는 모든 작업을 무료로 가져온다.

그들은 단지 그것을 상속함

![](https://velog.velcdn.com/images/xoxo0223/post/32706420-114e-4020-8979-a74dd3347e45/image.png)


진짜 awesome한 것은 슈퍼클래스 작성자가 해당 작업의 작은 부분을 하위클래스가 재정의할 수 있는 별도의 customization point를 분할하고 이 customization이 상속된 구현에 중첩될 때 발생한다.

이를 통해 개방형 유연성과 특정 변형을 가능하게 하는 동시에 어려운 논리를 재사용할 수 있다.

클래스 짱짱맨

근데 3가지 불만이있음

![](https://velog.velcdn.com/images/xoxo0223/post/34e4ca8f-473b-42b9-8536-d2c32eb5426d/image.png)


## 첫째, 자동 공유기능이 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/82467150-7334-4a63-b0c3-f80534cc0074/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/e409d6c5-5bfc-4605-b316-360aae5781b4/image.png)


A는 B에게 data 조각을 건네고 B는 대화가 끝났다고 생각한다.

원하는 데이터를 받았으니까 끝났다고 생각함

![](https://velog.velcdn.com/images/xoxo0223/post/0a16bcc6-afd0-4c94-85ec-2199597717cb/image.png)

A는 심각한 데이터에 질려서 Poines로 바까버림

B가 나중에 A로부터 얻은 이 데이터를 파헤칠 때까지 이것은 완전히 괜찮음

![](https://velog.velcdn.com/images/xoxo0223/post/ac3eb2a1-c87d-4ae0-822b-95b18ab91a26/image.png)


B가 이 데이터를 볼 때 문제가 발생함,,,

갑자기 Pony가 있어서,,

B는 A의 Pony가 아니라 데이터를 원한다.

이제 어떻게 되냐??

![](https://velog.velcdn.com/images/xoxo0223/post/95e51ebc-5b99-475f-bf0b-be37389abe11/image.png)


먼저, 코드의 버그를 없애기 위해 미친 듯이 모든 것을 복사하기 시작한다.

하지만 너무 많은 복사본을 만들고 있어  코드 속도가 느려진다.

그리고 디스패치 큐에서 무언가를 처리하고 스레드가 변경 가능한 상태를 공유하기 때문에 갑자기 경쟁 조건이 발생하여 불변성을 보호하기 위해 잠금을 추가하기 시작한다.

그러나 잠금은 코드 속도를 더 느리게 하고 교착 상태로 이어질 수도 있다!!

그리고 이 모든 것은 복잡성이 추가되며 그 효과는 한단어로 요약될 수 있다. 버그

이를 처리하기 위해 수년 동안 @property(copy) 및 coding convention과 같은 언어 기능의 조합을 적용해왔다.

![](https://velog.velcdn.com/images/xoxo0223/post/febff07e-f6a3-413f-a628-a61497261412/image.png)


Cocoa 문서에는 반복하는 동안 변경 가능한 컬렉션을 수정하는 것에 대한 경고가 있다.

이것은 모두 클래스에 내재된 mutable state의 암시적 공유 때문이다.

그러나 이것은 Swift에는 적용되지 않는다.

왜냐?

![](https://velog.velcdn.com/images/xoxo0223/post/c884ce83-d891-4ac1-9ca6-f0a8800cd681/image.png)


Swift의 컬렉션은 모두 value type이기 때문에 반복하는 컬렉션과 수정하는 컬렉션이 서로 다르다!

## 두번째 불만, 클래스 상속이 너무 거슬린다.

![](https://velog.velcdn.com/images/xoxo0223/post/619a236d-da81-482a-a6df-bb85d9eee7ac/image.png)


- 우선 일체형이다.
- 우리는 하나의 수퍼 클래스를 얻는다.
- 그리고 클래스 상속은 단일 상속이기 때문에 관련될 수 있는 모든 것이 함께 던져지면서 클래스가 부풀려진다.
- 또한 나중에 어떤 확장이 아니라 클래스를 정의하는 순간에 슈퍼클래스를 선택해야 한다.
- 다음으로 슈퍼클래스에 stored property가 있는 경우 이를 받아들여아한다.
    - 선택의 여지가 없음
    - 그리고 프로퍼티를 초기화해야한다.
    - “designated, convenience, required” 불변성을 깨트리지 않고 슈퍼 클래스와 상호작용하는 방법도 이해해야한다. → initailizer burden
- 클래스 작성자가 final을 사용하지 않고, 메서드가 재정의될 가능성을 고려하지 않고 메서드가 무엇을 할 것인지 알고 있는 것처럼 코드를 쓰는게 자연스러운 일이다.

이것이 Cocoa의 모든 곳에서 delegate 패턴을 사용하는 이유이다.

## 세번째 불만, 클래스는 타입 관계가 중요한 문제에 적합하지 않다.

비교와 같은 대칭 작업을 표현하기 위해 클래스를 사용해 본 적이 있다면 무슨 말인지 알것이다.

![](https://velog.velcdn.com/images/xoxo0223/post/217f4d16-e2f4-4147-927a-0ce609a42941/image.png)


예를 들어 일반화된 정렬이나 이진 검색을 작성하려면 두 요소를 비교할 수 있는 방법이 필요하다.

클래스로 짜면 위처럼 끝난다.

사실 클래스에서는 precedes의 바디를 작성해줘야 한다.

![](https://velog.velcdn.com/images/xoxo0223/post/a5b2df7c-f870-4b80-92a7-b4687e9ffdcf/image.png)



이제 precedes에 뭘 넣을까

우리는 아직 Ordered의 임의의 인스턴스에 대해 아무것도 모른다.

따라서 메서드가 하위 클래스에 의해 구현되지 않은 경우에는 트랩 외에는 실제로 할 수 있는 것이 없다.

이것이 우리가 타입 시스템과 싸우고 있다는 첫 번째 신호이다.
![](https://velog.velcdn.com/images/xoxo0223/post/cdd24f7f-4d57-4242-8860-b3ef0cbe0dd7/image.png)

우리는 이 문제를 제쳐두고 Ordered 구현의 각 하위클래스가 선행하는 한 괜찮을 것이라고 생각한다.

그래서 우리는 Ordered의 예를 구현한다.

![](https://velog.velcdn.com/images/xoxo0223/post/ff6513ee-7b8e-4e3d-a351-18ad0881ae25/image.png)




그것은 이중 값을 가지며 비교를 수행하기 위해 precedes를 재정의한다.

물론 작동하지 않는 경우는 제외한다.

![](https://velog.velcdn.com/images/xoxo0223/post/da91a726-2771-400d-9d0e-baad75b72d34/image.png)


위에서 other는 숫자가 아닌 임의의 Ordered이므로 other에 값 프로퍼티가 있는지 알 수 없다.

![](https://velog.velcdn.com/images/xoxo0223/post/a5352eb4-6929-41d5-b8d3-169f297b35a0/image.png)

실제로 텍스트 프로퍼티가 있는 레이블일 수도 있다.

따라서 올바른 유형에 도달하기 위해 다운 캐스팅해야 한다.

![](https://velog.velcdn.com/images/xoxo0223/post/37320ac4-d9f4-4f9e-b393-f80a5e21ccde/image.png)


other가 레이블로 판명되었다고 가정해보자, 우리는 함정을 만들 것이다.

이것은 우리가 슈퍼 클래스에서 본문을 작성할 때 겪었던 문제와 매우 흡사하고 이전보다 더 나은 답을 갖고 있지 않다.

이것이 static type safety hole이다.

왜 이런 일이 발생했을까?

이유는 클래스가 self 타입과 other 타입사이의 중요한 타입 관계를 표현하도록 허용하지 않기 때문이다.

![](https://velog.velcdn.com/images/xoxo0223/post/536ce076-c4b2-4ed3-a2e8-82ff11330a87/image.png)


사실 이것을 code smell로 사용할 수 있다.

따라서 코드에서 강제 다운 캐스트를 볼 때마다 일부 중요한 타입 관계가 소실되었다는 좋은 신호이며 종종 추상화에 클래스를 사용하기 때문이다.

![](https://velog.velcdn.com/images/xoxo0223/post/2f402ace-9b03-4123-8490-cf5dd3d38330/image.png)




이제 우리에게 필요한 것은 더 나은 추상화 메커니즘이다.

암시적 공유 또는 손실된 타입 관계를 받아들이도록 강요하지 않고

타입을 정의할 때 하나의 추상화를 선택하고 수행하도록 강제하지 않고

원치 않는 인스턴스 데이터나 관련된 초기화 복잡성을 받아들이도록 강요하지 않는 것이다.

마지막으로 내가 재정의 해야 하는 것에 대해 모호함을 남기지 않는 것이다.

프로토콜에 대한 얘기다.

프로토콜은 이러한 모든 장점을 가지고 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/9ca6380c-f9a6-4c71-9b6c-75d6a9a97976/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/7446a6e2-6390-411a-a09a-bfac2ee75307/image.png)

클래스로 시작하지 마라, 프로토콜로 시작해라

위의 예제를 프로토콜로 가보자

![](https://velog.velcdn.com/images/xoxo0223/post/5ac5d1da-6741-4c5f-b803-96c38d0bbdb1/image.png)


먼저 Ordered를 프로토콜로 바꾸고

Swift는 메서드에 바디를 넣을 수 없다고 말한다.

이는 동적 런타임 검사를 정적 검사로 바꾸려는 것을 의미하기 때문에 실제로 좋다.

![](https://velog.velcdn.com/images/xoxo0223/post/2c1058ae-18d5-4b7c-a8fe-785d8c885de7/image.png)

number → struct

override하지 않기 때문에 제거

더 나아졌지만 other가 여전히 임의의 Ordered이기 때문에 강제 다운캐스트가 필요하기 때문에 기본 static type safety hole을 해결하지 않았다.

![](https://velog.velcdn.com/images/xoxo0223/post/ef0c9c1b-0e0f-4bcf-a5cc-9dde62d177bc/image.png)


파라미터의 타입을 Number로 바꾸면 Swift는 프로토콜과 일치하지 않다고 말할것이다.

![](https://velog.velcdn.com/images/xoxo0223/post/ccd5eb10-b887-4f00-b9fd-078221525230/image.png)


이 문제를 해결하려면 프로토콜에 파라미터 타입인 Ordered를 Self로 교체해야한다.

이것을 Self-requirement라고 한다.

따라서 프로토콜에서 Self를 볼 때 해당 프로토콜을 준수할 타입인 모델 타입에 대한 placeholder이다.

이제 이 프로토콜을 사용하는 방법을 살펴보자

![](https://velog.velcdn.com/images/xoxo0223/post/b7755924-2799-4443-b31d-04ebc9718e26/image.png)


위 함수는 Ordered가 클래스일 때 작동했던 이진 검색이다.

그리고 우리가 Self-requirement를 구현하지 않아도 완벽하게 작동한다.

여기서 [Ordered]는 여러가지 타입을 다룰 것이라고 주장한다.

따라서 이 배열에는 Number와 Label이 혼합되어 포함할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/f2b760e7-d4f2-4d5c-8813-4559b0d4ac1b/image.png)


근데 우리는 프로토콜에 Self-requirement를 추가했기 때문에 컴파일러는 이것을 하나의 타입으로 만들도록 강제할 것이다.

![](https://velog.velcdn.com/images/xoxo0223/post/2aa6ab92-77c8-4c78-8542-6a685fc246b5/image.png)


이렇게 바꿔보자

이것은 “나는 단일 Ordered 타입 T의 동종 배열에서 작업하겠다” 라고 말한다.

이게 유연성이 손실되는 것과 같다고 생각할 수 있는데

사실 우리는 트래핑외에는 heterogeneous case(이질적인 경우)를 실제로 처리한 적이 없다.

따라서 homogeneous array(동종 배열)이 우리가 원하는 것이다!!

![](https://velog.velcdn.com/images/xoxo0223/post/0853a599-5f67-4f8e-9f92-0037f1dcc867/image.png)

Self Requirement를 사용했을 때랑 안했을 때 차이
