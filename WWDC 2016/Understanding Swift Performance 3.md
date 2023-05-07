# 제네릭 vs Existential 타입

![](https://velog.velcdn.com/images/xoxo0223/post/4b125511-83fb-4dd2-a04c-3c9437853cbb/image.png)

제네릭 코드로 고고

이야기의 마지막 부분에서 제네릭 타입의 변수가 저장되고 복사되는 방법과 메서드 디스패치가 변수와 함께 작동하는 방법을 살펴봅시다~~

![](https://velog.velcdn.com/images/xoxo0223/post/e0384db8-0a96-4a94-a5f1-799b8c5c7f2b/image.png)


제네릭 함수를 사용하여 구현된 코드를 봅시다
이제 DrawACopy 메서드는 제네릭 파라미터 제약 조건을 Drawable로 사용하고 프로그램의 나머지 부분은 이전과 동일하게 유지됩니다.

![](https://velog.velcdn.com/images/xoxo0223/post/44ff9eeb-2f53-460c-8ff4-105131ad06ca/image.png)


프로토콜 타입과 비교할 때 무엇이 다른가요?
제네릭 코드는 파라메트릭 다형성이라고도 하는 보다 정적인 형태의 다형성을 지원합니다.

![](https://velog.velcdn.com/images/xoxo0223/post/3ec27b0d-a287-4199-8d99-44cf458b73e8/image.png)


호출 컨텍스트당 하나의 타입
이게 무슨 뜻인가? 예시로 알아봅시다.

위 코드를 보면 함수 foo가 있습니다. 이 함수는 제네릭 매개변수인 T의 제약조건은 drawable이고 이 매개변수를 함수 bar로 전달한다고 합니다.



![](https://velog.velcdn.com/images/xoxo0223/post/e40d5840-9214-4219-8d78-9a45eeab1ba1/image.png)


우리는 Point를 만들고 이 Point를 함수 foo에 전달합니다.
이 함수가 실행되면 Swift는 제네릭 타입 T를 이 호출 측에서 사용되는 타입(위 코드에서는 Point)에 바인딩합니다
함수 foo가 이 바인딩으로 실행되고 bar 함수 호출에 도달되면 지역 변수는 방금 바인딩된 타입 즉, Point를 가집니다.

![](https://velog.velcdn.com/images/xoxo0223/post/b490bb41-1b8d-4253-911f-4639c0c922c3/image.png)


따라서 다시 이 호출 컨텍스트의 제네릭 매개변수 T는 Point 타입을 통해 바인딩됩니다.
보는것처럼 타입은 매개변수를 따라 콜 체인 아래로 대체됩니다.

이것이 정적 형태의 다형성, 파라메트릭 다형성이 의미하는 바입니다.

이제 Swift가 내부적으로 이를 구현하는 방법을 살펴봅시다.
drawACopy 함수로 다시 돌아감

![](https://velog.velcdn.com/images/xoxo0223/post/5ef7848e-7fb4-4e05-b847-2f55bd0fd7cc/image.png)


이 예에서는 Point를 전달합니다.
프로토콜 타입을 사용할 때와 마찬가지로 하나의 공유 구현이 있습니다.
그리고 이 공유 구현은 이전에 프로토콜 타입에서 봤던 코드와 매우 비슷해 보일 것 입니다.

일반적으로 해당 기능 내부의 작업을 수행하기 위해 protocol/value witness table을 사용합니다.

그러나 호출 컨텍스트당 하나의 타입이 있기 때문에 Swift는 여기에서 existential container를 사용하지는 않습니다.

![](https://velog.velcdn.com/images/xoxo0223/post/778cdaea-b463-49e4-9d48-ea35eb440faa/image.png)


대신, 함수에 대한 추가 인수로 이 호출 사이트에서 사용되는 타입인 Point의 value/protocol witness table을 모두 전달할 수 있습니다.

![](https://velog.velcdn.com/images/xoxo0223/post/5db61b8e-eac5-4f1a-b94e-86bc827533b8/image.png)


그런 다음 해당 함수를 실행하는 동안 매개변수에 대한 local 변수를 만들 때 Swift는 value witness table을 사용하여 잠재적으로 필요한 버퍼를 힙에 할당하고 할당 소스에서 destination으로 복사를 실행합니다.

![](https://velog.velcdn.com/images/xoxo0223/post/44596313-9800-47d2-bc32-4a2cb85e548b/image.png)


local 매개변수에 대해 draw 메서드를 실행할 떄도 마찬가지로 전달된 protocol witness table을 사용하고 테이블에서 고정 오프셋의 draw 메서드를 찾아 구현으로 이동합니다.

![](https://velog.velcdn.com/images/xoxo0223/post/d659dc92-c5d4-4da3-b11f-294a938b5dcd/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/546b6f13-3518-41a0-a6f1-14a876ec7f7e/image.png)



Existential container가 없는데 Swift는 local 매개변수에 필요한 메모리를 어떻게 할당할까요?

![](https://velog.velcdn.com/images/xoxo0223/post/f9908faf-35c4-46f5-a07f-784e81428662/image.png)


스택에 valueBuffer를 할당합니다.
valueBuffer는 3 words입니다. 

![](https://velog.velcdn.com/images/xoxo0223/post/91943c7e-2947-4d81-ad41-8d96503ddcec/image.png)


Point와 같은 작은 값은 valueBuffer에 맞습니다.

![](https://velog.velcdn.com/images/xoxo0223/post/f9f107ab-594c-4801-81de-a2b7eeea4094/image.png)

Line과 같은 큰 값은 다시 힙에 저장되며 local existential container 내부에 해당 메모리에 대한 포인터를 저장합니다.
그리고 이 모든 것은 value witness table의 사용을 위해 관리됩니다.

![](https://velog.velcdn.com/images/xoxo0223/post/2797e079-617c-489d-a961-567500c81966/image.png)


이제 우리는 이게 더 빠른가? 좋은가? 프로토콜 타입을 사용하지 않으 ㄹ수 있을까라는 생각을 할 수 있습니다.
이러한 정적인 형태의 다형성은 제네릭의 특수화(specialization of generics)라는 컴파일러 최적화를 가능하게 합니다.

한번 봅시다.

![](https://velog.velcdn.com/images/xoxo0223/post/b0a87a41-250b-4158-9834-fcaaf8027f69/image.png)

다시 제네릭 매개변수를 받는 drawACopy 함수가 있고 Point를 해당 함수를 호출할 때 전달합니다.

![](https://velog.velcdn.com/images/xoxo0223/post/eaaaa8e6-fe9d-4bcd-9666-0d1c4d07d9d7/image.png)



그리고 정적 다형성이 있으므로 호출 사이트에 하나의 타입이 있습니다.


![](https://velog.velcdn.com/images/xoxo0223/post/df97777d-157d-4c1c-84c8-7d8719c2a356/image.png)


Swift는 해당 타입을 사용하여 함수의 제네릭 매개변수를 대체하고 해당 타입에 특정한 해당 함수 버전을 생성합니다.


![](https://velog.velcdn.com/images/xoxo0223/post/d7e0061f-58ad-4f32-baa5-67f3982b207d/image.png)

따라서 Point 타입의 매개변수를 사용하는 Point 함수의 drawACopy가 있으며 해당 함수 내부의 코드는 다시 해당 타입에 고유합니다.


![](https://velog.velcdn.com/images/xoxo0223/post/51a57853-98a6-45bf-972f-3899e9a64f26/image.png)


Swift는 프로그램의 호출 사이트에서 사용되는 타입별로 버전을 생성합니다.
따라서 Line에서 drawACopy 함수를 호출하면 Line 버전의 함수를 생성합니다.

이로 인해서 코드 크기가 크게 증가하지 않을까?

하지만 사용할 수 없는 정적 타입 정보로 인해 aggressive compiler optimization이 가능한 Swift는 실제로 여기서 코드 크기를 줄일 수도 있습니다.

![](https://velog.velcdn.com/images/xoxo0223/post/3ca4f75c-180c-4278-b96b-2547604c8b89/image.png)


예를 들어 아래처럼 Point 버전의 메서드를 인라인할 수 있습니다.
그런 다음 훨씬 더 많은 컨텍스가 있으므로 코드를 추가로 최적화합니다.

![](https://velog.velcdn.com/images/xoxo0223/post/0c117f7f-c8a1-4905-ae87-6ad982c7d856/image.png)

한줄 로 줄어들 수 있고 앞에서 본 것처럼 draw 구현으로 더 줄일 수 있습니다.

![](https://velog.velcdn.com/images/xoxo0223/post/0d6586e6-51e0-4220-ab7b-6fdc4a8e36df/image.png)


이제 Point 메서드의 drawACopy가 더 이상 참조되지 않으므로 컴파일러도 이를 제거하고 Line 예제에 대해 유사한 최적화를 수행합니다.
따라서 이 컴파일러 최적화가 반드시 코드 크기를 증가시키는 것은 아닙니다. (발생할 수도 있지만 반드시 그런 것은 아님)

여기까지 specialization이 어떻게 작동하는지 살펴보았고 이제 언제 발생하는지 작은 예를 살펴봅시다.

![](https://velog.velcdn.com/images/xoxo0223/post/edec30f3-0a74-40e4-9708-52ef87894a66/image.png)

Point를 정의한 다음 해당 타입의 지역 변수를 만듭니다.

Point로 초기화한 다음 해당 Point를 drawACopy 함수의 인수로 전달합니다.

![](https://velog.velcdn.com/images/xoxo0223/post/3a39f471-8aca-4aeb-803c-644bc854ccd8/image.png)


이제 이 코드를 특수화하기 위해 Swift는 이 호출 사이트에서 타입을 유추할 수 있어야 합니다.

로컬 변수를 보고 초기화로 돌아가서 Point로 초기화되었음을 확인할 수 있기 때문에 타입을 유추할 수 있습니다.

Swift는 또한 특수화 중에 사용되는 타입과 함수(사용 가능한 제네릭 함수 자체)모두에 대한 정의가 있어야 합니다.
-> 위 코드의 경우 모두 하나의 파일에 정의되어 있음

여기서 전체 모듈 최적화(whole module optimization)가 최적화 기회를 크게 향상시킬 수 있습니다.
왜 그런지 봅시다.

![](https://velog.velcdn.com/images/xoxo0223/post/22bb82d5-6e4a-4e42-92c9-0d29c442deb5/image.png)


Point의 정의를 별도의 파일로 옮겼다고 가정해봅시다.
이제 이 두 파일을 별도로 컴파일하면 UsePoint 파일을 컴파일하려고 하면 컴파일러가 이 두 파일을 별도로 컴파일했기 때문에 Point 정의를 더 이상 사용할 수 없습니다.

![](https://velog.velcdn.com/images/xoxo0223/post/2f9e7566-432a-4098-ba9e-5972747afd42/image.png)


그러나 전체 모듈 최적화를 통해 컴파일러는 두 파일을 하나의 단위로 함꼐 컴파일하고 Point 파일의 정의에 대한 통찰력을 갖게 되며 최적화가 발생할 수 있습니다. -> whole module optimization을 하면 한 파일에 정의되어 있지 않아도 위에서 얘기하는 최적화가 가능하다는 말인 듯

최적화의 기회를 향상시키기 때문에 Xcode8에서 whole module optimization이 기본적으로 활성화 되어 있다고 합니다.

![](https://velog.velcdn.com/images/xoxo0223/post/6875032b-2952-4125-869b-bdb8dafd9408/image.png)


아까 나온 한 쌍의 Drawable 프로토콜 타입이 있는 Pair 구조체가 있습니다.

![](https://velog.velcdn.com/images/xoxo0223/post/c5588bf4-3d5a-4ff8-b956-7f96b5bd3e80/image.png)


Pair를 만들고 싶을 때마다 실제로 같은 타입의 쌍(Line 쌍 또는 Point 쌍)을 만들고 싶었습니다.

![](https://velog.velcdn.com/images/xoxo0223/post/3e9c6c01-0089-41d0-8c4f-31c3ae3ea128/image.png)



Line Pair의 스토리지 표현에는 두 개의 힙 할당이 필요했다는 것을 기억하세요
이 프로그램을 보았을 때 우리는 여기서 제네릭 타입을 사용할 수 있다는 것을 깨달았습니다.

![](https://velog.velcdn.com/images/xoxo0223/post/72991649-aee8-458c-aacc-2b9810625319/image.png)



따라서 Pair를 제네릭으로 정의한 다음 해당 제네릭 타입의 첫 번째 및 두 번째 프로퍼티에 이 제네릭 타입이 있는 경우 컴파일러는 실제로 동일한 타입의 Pair만 생성하도록 강제할 수 있습니다. (first, second를 동일한 타입으로 강제함) 

그리고 프로그램 후반우헤 Line에 Point를 저장할 수도 없습니다.
우리가 원했던 것입니다.
성능 측면에서는 어떨까요?

![](https://velog.velcdn.com/images/xoxo0223/post/3efaa4ea-eff1-4b66-8ce9-067fafade558/image.png)


여기서는 store properties가 제네릭 타입입니다.
타입은 런타임에 변경할 수 없다고 말했습니다.

![](https://velog.velcdn.com/images/xoxo0223/post/f7527745-f398-453d-8ad4-bb1856c4c4d4/image.png)

Swift가 둘러싸는 타입의 스토리지를 인라인으로 할당할 수 있다는 것입니다.

![](https://velog.velcdn.com/images/xoxo0223/post/8bb2cc67-3055-40a4-9911-7651d1cc061b/image.png)

따라서 한 쌍의 Line을 생성할 때 Line에 대한 메모리는 실제로 둘러싸는 Pair의 인라인에 할당됩니다.
추가 힙할당이 필요하지 않습니다.

그러나 해당 프로퍼티에 다른 타입의 값을 저장할 수는 없습니다.

특수화를 통해서 컴파일러가 타입별 버전을 생성하기 때문에 stack에 inline으로 저장해버릴 수 있게 되고 따라서 pwt, vwt existential container가 다 필요없게 되는 것 같음
stack에 inline으로 저장해버리니까 Line도 그냥 힙 할당없이 저장할 수 있는 거고,,,


![](https://velog.velcdn.com/images/xoxo0223/post/35996ba5-02ca-4427-9112-48ce7bf6f391/image.png)

우리는 VWT, PWT를 사용하여 특수화되지 않은 코드가 작동하는 방식과
 컴파일러가 제네릭 함수의 타입별 버전을 생성하는 코드를 특수화하는 방법을 살펴보았습니다.
 
 먼저 구조체를 포함하는 특수화된 제네릭 코드를 살펴보고 성능을 살펴봅시다.
 
![](https://velog.velcdn.com/images/xoxo0223/post/50728932-c63e-4fb4-aefd-7a43b138b0fa/image.png)


1. 값을 복사할 때 힙 할당이 필요하지 않습니다.
2. 참조 카운트가 없습니다.
3. 추가 컴파일러 최적화를 가능하게 하고 실행시간을 줄이는 정적 메서드 디스패치 기능이 있습니다!!

![](https://velog.velcdn.com/images/xoxo0223/post/cd4da062-1f81-4d59-bb35-a3cf1d00d0fa/image.png)


클래스 타입과 비교해보면
1. 힙할당
2. 참조 카운트
3. V-Table을 통한 동적 디스패치 등이 있습니다.

![](https://velog.velcdn.com/images/xoxo0223/post/0eda405b-b7d1-491d-9529-cc7cd35b19ae/image.png)

그 다음으로 작은 값을 포함하는 특수화되지 않은 일반 코드를 살펴봅시다.
1. valueBuffer에 맞기 때문에 힙 할당이 없습니다.
2. 값에 참조가 포핟뫼어 있지 않으면 참조 카운팅이 없습니다.
3. witness table을 사용하여 모든 잠재적 호출 사이트에서 하나의 구현을 공유하게 됩니다.

![](https://velog.velcdn.com/images/xoxo0223/post/52ac8a0a-add0-400b-8213-9fab5c9a15c0/image.png)

큰 값을 사용하는 경우
1. 힙 할당이 발생합니다.
2. 참조가 포함된 경우 참조 카운트가 있습니다.
3. 다시 동적 디스패치의 기능을 얻습니다. 즉, 코드 전체에서 하나의 일반 구현을 공유할 수 있습니다.

드디어 마지막 요약

![](https://velog.velcdn.com/images/xoxo0223/post/b3f35234-4b02-44dd-b1f3-639ce02992a2/image.png)

동적 런타임 타입 요구사항이 가장 적은 응용 프로그램의 엔티티에 적합한 추상화를 선택하세요. 이렇게 하면 정적 타입 검사가 활성화되고 컴파일러는 컴파일 타임에 프로그램이 올바른지 확인할 수 있으며, 추가로 컴파일러는 코드를 최적화하기 위한 더 많은 정보를 가지므로 더 빠른 코드를 얻을 수 있습니다.
- 구조체 및 열거형과 같은 값 타입을 사용하여 프로그램의 엔티티를 표현할 수 있는 경우 value semantics를 얻을 수 있습니다. 이는 의도하지 않은 상태 공유가 없고 최적화 가능한 코드를 얻을 수 있씁니다.
- 클래스를 사용해야 하는 경우 참조 카운팅 비용을 줄이는 몇 가지 기술을 보여주었습니다.
- 만약 프로그램의 일부가 정적 형태의 다형성을 활용하여 표현될 수 있다면, 제네릭 코드와 값 타입을 결합하여 매우 빠른 코드를 얻을 수 있으면서도 해당 코드의 구현을 공유할 수 있습니다.
- 동적 다형성이 필요한 경우 프로토콜 타입을 값 타입과 결합하여 얻을 수 있습니다. 클래스를 보다 비교적 빠른 코드를 얻을 수 있지만 여전히 value semantics를 유지할 수 있습니다.
그리고 프로토콜 타입 또는 제네릭 타입 내에서 큰 값을 복사하기 때문에 힙 할당 문제가 발생하는 경우 COW와 함꼐 간접 저장소를 사용하여 이 문제를 해결할 수 있습니다.

어렵다 어려워,,,,
