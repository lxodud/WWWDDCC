# Concurrent Programming With GCD

[Concurrent Programming With GCD in Swift 3 - WWDC16 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2016/720/)

새 프로젝트를 만들면 다음과 같은 모양을 가짐

![](https://velog.velcdn.com/images/xoxo0223/post/6bdc6d35-62ee-43d4-be1b-4b346b9fee61/image.png)

해당 응용 프로그램은 메인 스레드를 가져옴!

이 메인 스레드는 사용자 인터페이스를 구동하는 모든 코드를 실행하는 역할을 함

응용 프로그램에 코드를 추가하기 시작하면 응용 프로그램의 성능이 상당히 크게 변경됨

[](https://velog.velcdn.com/images/xoxo0223/post/7f8c4206-74b3-4e5f-bc5f-13cff886c6b9/image.png)

예를 들어, 데이터 변환, 이미지 처리같은 큰 작업을 메인 스레드에서 도입하면 UI가 크게 문제가 생김

macOS에서는 회전바퀴, iOS에서는 UI가 느려지거나 완전히 멈출 수도 있음

이런 문제를 어떻게 처리할까?

바로바로 어플리케이션에 동시성 개념을 도입하는 것으로 시작해야 함

![](https://velog.velcdn.com/images/xoxo0223/post/298eb861-5314-44b2-8b11-ffe871c1c5be/image.png)

동시성을 사용하면 어플리케이션의 여러 부분을 동시에 실행할 수 있음

시스템에서는 스레드를 생성하여 동시성을 달성함

CPU 코어는 주어진 시간에 스레드 중 하나를 실행할 수 있지만 어플리케이션에 동시성을 도입할 경우의 불이익은 스레드 안전성을 유지하기가 훨씬 더 어렵다는 것이다.

도입한 스레드는 다른 스레드에서 작업을 수행하는 동안 코드 불변성을 깨뜨릴 수 있음

apple은 어캐 도와주냐? 

GCD로

![](https://velog.velcdn.com/images/xoxo0223/post/825d018e-a7c3-45ec-af01-797aa3da585d/image.png)


GCD는 apple 플랫폼의 동시성 라이브러리

Apple 기기에서 작동하는 코드, 다중 스레드 코드를 작성하는 데 도움이 된다!

동시성을 지원하기 위해 스레드 자체 위에 몇 가지 추상화를 도입하는데

이게 Dispatch Queue, Run Loop이다.

Dispatch Queue는 작업 항목을 해당 Queue에 제출할 수 있도록 하는 친구임

Swift에서 이것(작업)은 클로저임

그리고 Dispatch는 적합한 스레드와 서비스를 불러옴

![](https://velog.velcdn.com/images/xoxo0223/post/28df807e-72d1-4d06-9dcc-0685ada19478/image.png)


Dispatch가 해당 스레드에 대한 모든 작업이 실행을 완료하면 작업자 스레드를 자체적으로 해제할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/cd6faf66-f15f-4cc5-a2c9-b9d53cc62bf9/image.png)


또한 개발자는 자신의 스레드를 생성할 수 있고 그 스레드에서 런 루프를 실행할 수도 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/23287100-3eff-4344-afc6-e1319ef7b184/image.png)

그리고 마지막으로, 메인 스레드를 얻을 수 있다.

메인 스레드는 메인 런 루프와 메인 큐를 모두 가져온다.

![](https://velog.velcdn.com/images/xoxo0223/post/67d62139-d568-4548-af0c-5cf1efaebc6b/image.png)

Dispatch Queue에는 작업을 제출할 수 있는 두 가지 주요 방법이 있다.

그 중 첫 번째는 비동기 실행이다.

![](https://velog.velcdn.com/images/xoxo0223/post/c01eedb3-c0c4-4789-a896-40abe9d9cd73/image.png)

위 사진처럼 여러 작업 항목을 Dispatch Queue에 대기시킬 수 있고, 다시 Dispatch 하면 해당 작업을 실행하기 위한 스레드가 나타난다.

Dispatch는 해당 Queue에서 항목을 하나씩 가져와 실행한다.

Queue의 모든 항목이 완료되면 시스템은 스레드를 회수한다.

![](https://velog.velcdn.com/images/xoxo0223/post/bc0d99ca-fba9-4236-8ccc-14638f30bc31/image.png)


두 번째 모드 동기 실행

![](https://velog.velcdn.com/images/xoxo0223/post/09bedd29-d9bb-46c2-b158-8db7116854c8/image.png)

위 그림은 이전과 동일한 설정이 있는 경우 비동기 작업이 있는 Dispatch Queue가 있다.

그러나 자신의 스레드를 가지고 있고 그 스레드는 Dispatch Queue에서 코드를 실행하고 그것이 동작할 때까지 기다리기를 원한다.

![](https://velog.velcdn.com/images/xoxo0223/post/da9fdc8b-b94d-44c6-8991-cda89fe405ca/image.png)


해당 작업을 Dispatch Queue에 제출하면 스레드는 차단된다. (실행을 요청한 항목이 완료될 때까지 대기한다.)

동기로 작업을 보냈으니까 기다리는 거임

그리고 Dispatch Queue에 비동기 작업을 더 추가할 수 있음 그러면 Dispatch는 해당 Queue의 항목을 처리하기 위해서 스레드를 불러옴

![](https://velog.velcdn.com/images/xoxo0223/post/e77b063a-8d0e-4a1f-bf71-4ad10d910564/image.png)


비동기 항목은 거기서 실행됨

![](https://velog.velcdn.com/images/xoxo0223/post/3bbbd124-40c6-4cd8-9bec-6d9300fab873/image.png)


Dispatch Queue는 대기 중인 스레드에 제어를 전달하고 해당 항목을 실행함

![](https://velog.velcdn.com/images/xoxo0223/post/344788cb-b50b-434f-8237-e16a6b78aab3/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/96f1fba8-3fd8-41b8-9c18-0433807e3eb8/image.png)


그 다음 Dispatch Queue의 제어가 작업자 스레드로 돌아간다.

![](https://velog.velcdn.com/images/xoxo0223/post/76952917-0b8e-44b2-92fd-c69cf3a5faf7/image.png)

그리고 해당 Queue의 나머지 항목을 수행하고 사용 중이던 스레드도 회수한다.

![](https://velog.velcdn.com/images/xoxo0223/post/1ef5ec19-a2d6-48d2-aa3c-572c32e65052/image.png)

위는 실제로 dispatch에 작업을 제출하는 방법임!!

이전에 겪었던 문제를 해결하는 데 이를 어떻게 사용할까?

우리가 하고자 하는 것은 메인 스레드에서 사용자 인터페이스를 차단하는 작업(데이터 변환, 이미지 처리)을 다른 Queue에서 보냄으로써 이를 수행한다.

![](https://velog.velcdn.com/images/xoxo0223/post/7f3eed19-9b4b-4fa6-9514-cd9c30135061/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/3b7375cf-beb8-4725-87f8-38ce5897f777/image.png)

위에서 아래처럼

이제 데이터를 변환하고 싶을 때 해당 데이터의 값을 다른 Queue의 변환 코드로 이동하고 변환한 다음 메인 스레드로 보낼 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/c0fa8af7-5dc5-40ed-a1f8-f5b4eb56eb03/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/40ae18c2-931c-4fef-bec5-730934520544/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/4b0a5b7e-cd09-4486-ba38-2c9b089185d4/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/976309ec-225f-48bb-8f02-d71e308c9cbe/image.png)


위 사진은 데이터 변환 흐름: 메인 → 변환 코드있는 곳 → 변환완료해서 메인으로 보내기

이를 통해서 메인 스레드가 이벤트를 처리하는 동안 해당 작업을 수행할 수 있다.

실제 코드로 보면 아래와 같음

![](https://velog.velcdn.com/images/xoxo0223/post/f262d588-4224-4284-9c95-abae0db52868/image.png)


작업을 보낼 Dispatch Queue를 만듬

레이블이 필요한데, 이 레이블은 디버깅할 때 볼수 있음

Dispatch Queue는 작업을 FIFO 순서로 실행시킴

그 다음 Dispatch Queue에서 async 메서드를 사용해서 해당 대기열에 작업을 제출할 수 있음

이제 코드로 크기 조정 작업을 다른 대기열에 제출했고, 이를 다시 메인 스레드로 가져오는 방법은 아래와 같음

![](https://velog.velcdn.com/images/xoxo0223/post/28d239a0-dbab-4696-a0a7-f138c75f071a/image.png)


Dispatch Main Queue는 메인 스레드 자체에서 실행하는 모든 항목에 서비스를 제공함

즉, DispatchQueue.main을 호출한 다음 queue에서 async를 호출하면 해당 코드가 실행되고 거기에서 UI업데이트를 할 수 있음

이게 코드를 제어하고 다른 스레드에 넣는 방법이다.

근데 약간의 비용이 듬

어플리케이션에서 동시성을 제어해야함

Dispatch가 사용하는 스레드 풀은 장치의 모든 호출을 사용하기 위해 달성하는 동시성을 제한함

그러나 해당 스레드를 차단할 때 어플리케이션의 다른 부분을 기다리거나 시스템 호출을 기다리는 경우 차단ㄴ된 작업자 스레드로 인해 더 많은 작업자 스레드가 생성될 수 있음

Dispatch는 코드 실행을 계속할 수 있는 새 스레드를 제공하여 합당한 동시성을 제공하려 하기 때문임

즉, 코드를 실행하는 데 사용할 적절한 수의 Dispatch Queue를 선택하는 것이 매우 중요하다.

그렇지 않으면 하나의 스레드를 차단할 수 있다. 그럼 다른 스레드가 올라오고, 다른 스레드를 차단하는 식임

(뭔소린지 모르겠음) 

이러한 패턴을 Thread Explosion이라고 부르는 것임

<aside>
💡 Thread Explosion
OS가 함께 관리해야 하는 running 스레드 개수가 너무 많아지는 상황임
이는 성능저하를 유발하고 경우에 따라 Deadlock이 발생할 수 있음
스레드는 값싼 시스템 자원이 아님
스레드는 OS에 의해 관리되고 만약 관리해야하는 running 스레드 개수가 너무 많아져 임계점을 넘으면 메모리 오버헤드와 지나친 컨텍스트 스위칭으로 인한 스케줄링 오버헤드 등이 발생할 수 있음

GCD에서 thread explosion을 유발하는 예시는
Concurrent Queue에 많은 async 작업을 요청하는 경우 이 스레드들이 block되거나 코어가 이미 작업 중이라 처리되지 못하고 기다림(다른 큐로 sync 작업을 요청하거나, system call을 기다리는 등등)
많은 요청들이 처리되지 못해 running 스레드 개수가 매우 많아짐

해결 방법은 많은 스레드들이 block 되지 않도록 주의하거나, 세마포어 등으로 할당 개수를 제한함
[참고1](https://velog.io/@yohanblessyou/%EC%9E%91%EC%84%B1%EC%A4%91-iOS-Concurrency-GCD-%EC%8B%AC%ED%99%94Thread-Safe), [참고2](https://www.swiftpal.io/articles/how-to-avoid-thread-explosion-and-deadlock-with-gcd-in-swift)

</aside>

![](https://velog.velcdn.com/images/xoxo0223/post/7500c03b-5141-4e02-b721-e92021258945/image.png)


아래 영상에서 자세하게 다룸!!

[Building Responsive and Efficient Apps with GCD - WWDC15 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2015/718/)

### 이제 실제로 어플리케이션에 어떻게 적용할까??

이미지 변환, 데이터베이스 등등

해당 영역을 가져와서 별개의 하위 시스템으로 분할한 다음 각각의 Dispatch Queue을 사용하여 지원하려고 한다.

![](https://velog.velcdn.com/images/xoxo0223/post/412e8f9a-68a4-443d-a7fa-58b28977635b/image.png)


이렇게 하면 각 하위 시스템에 Queue가 너무 많고 스레드가 너무 많다는 문제를 겪지 않고 독립적으로 작업을 실행할 수 있는 Queue가 제공된다.

여기에서 한 블록을 다른 블록으로, 그런 다음 다른 큐로, 그리고 다시 메인 큐로 비동기화할 수 있다.

이거랑 비슷게 작업을 그룹화하고 작업이 완료되기를 기다리는 친구가 있음

![](https://velog.velcdn.com/images/xoxo0223/post/a6362947-c6e8-42ca-b02a-e965f513bef7/image.png)
Grouping을 Dispatch가 도와줌

UI 인터페이스가 세 개의 개별 작업 항목을 생성하면 Dispatch Group을 만들 수 있음

Dispatch Group은 작업을 추적하는 데 도움이 된다.

DispatchGroup 객체를 생성하기만 하면 됌!!

![](https://velog.velcdn.com/images/xoxo0223/post/cfa4c63f-365a-4d68-8655-54d714e9aacb/image.png)

이제 DispatchQueue에 작업을 보낼 때 async 메서드의 파라미터로 group을 추가할 수 있음

해당 그룹에 더 많은 작업을 추가할 수 있고 다른 Queue에 추가하고 동일한 그룹과 연결할 수 있다.

그룹에 작업을 제출할 때마다 그룹은 완료될 것으로 예상되는 항목의 카운터를 증가시킨다.

![](https://velog.velcdn.com/images/xoxo0223/post/ae9ca920-dff5-4e12-ac78-e2bf4990b1e5/image.png)

작업이 모두 제출되고 모든 작업이 완료되면 그룹에 알림을 요청할 수 있고 선택한 Queue에 알려줄 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/b9118f3b-3520-4619-ba68-82b58e9afd92/image.png)


이제 작업이 하나씩 실행되고 완료될 때마다 그룹의 카운트가 감소함

![](https://velog.velcdn.com/images/xoxo0223/post/5071e9b9-ef18-4b33-88df-6a6dddd99338/image.png)


마지막 작업 항목이 완료되면 그룹이 계속 진행하여 요청한 Queue에 알림 블록을 보낸다.

위 사진에서 보면 Main Queue에 보내고 메인 스레드에서 실행될 것이다.

## 이제 3번째 패턴

지금 까지는 비동기식 실행이고 세 번째는 동기식 실행을 처리하는 거임

동기 실행을 사용하여 하위 시스템 간에 상태를 직렬화할 수 있다.

DispatchQueue는 기본적으로 직렬임

그리고 이것을 상호 배제 속성에 사용할 수 있음

- 상호 배제(mutual exclusion): 한 프로세스가 임계 구역에 진입했다면 다른 프로세스는 임계 구역에 들어올 수 없다.

즉, 해당 대기열에 동기적으로 작업을 제출하면 해당 대기열에서 하위 시스템이 실행되고 이 작업이 동시에 실행되지 않는다는 것이다.

이것을 사용해서 다른 위치에서 하위 시스템의 프로퍼티에 액세스하는 스레드 세이프에 대한 매우 간단한 액세스를 구축할 수 있음

![](https://velog.velcdn.com/images/xoxo0223/post/b1d78186-51fe-4407-a6c2-13009d4cd185/image.png)


예를 들어, 위 사진처럼 queue.sync를 호출하여 값을 반환할 수 있다.

이렇게 되면 queue에서 해당 값을 캡처한 다음 해당 작업이 완료되면 이를 반환한다.

그러나 이러한 패턴을 도입하기 시작할 때 주의해야 한다.

왜냐? 서브시스템 간에 잠금 순서 그래프 도입을 시작하기 때문이다.

그게 무슨 뜻이냐?

이전에 사용했던 하위 시스템이 있고 한 위치에서 다른 위치로 동기화한 다음 다른 위치로 동기화하고 마지막으로 첫 번째 위치로 다시 동기화하는 경우이다.

![](https://velog.velcdn.com/images/xoxo0223/post/e8b7456c-48d1-4a74-9173-64d42488cbb6/image.png)


이러면 데드락이 발생함

뒤에서 추가적으로 얘기할 꺼임

이제 어떻게 서브시스템 내부에서 dispatch 사용을 구조화할까?

Dispatch를 사용하여 제출한 작업을 분류할 수 있고, 그렇게 하려면 QoS를 사용해야함

QoS는 dispatch를 위해 제출하는 작업의 명시적 분류를 제공하는 클래스임

따라서 개발자는 dispatch를 위해 제출하는 코드의 의도를 나타낼 수 있다.

그리고 dispatch는 코드를 실행하는 방법에 영향을 주기 위해 그것을 사용할 수 있다.

즉, 코드는 다른 CPU 우선순위, 다른 IO 스케줄링 우선 순위 등으로 실행될 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/27aae587-c526-4f80-a620-2287022b48c7/image.png)


실제로 어떻게 쓰냐?

QoS 클래스를 async 메서드의 파라미터로 전달할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/8ad2c4db-d46b-463b-a67a-79e42634fae3/image.png)


그리고 위 그림처럼 더 높은 QoS로 dispatch queue에 작업을 보내면 dispatch가 생성된 우선 순위 반전을 해결하는 데 도움이 된다.

즉, Dispatch Queue의 앞에 있는 항목을 더 높은 QoS로 올려서 더 빠르게 실행하고, 실제로 예상한 대로 빠르게 항목을 실행할 수 있다.

그러나 이것은 작업이 라인을 뛰어넘는 데 도움이 되지 않는다.

여기에서 하는 일은 모든 작업을 우리 앞에 있는 것으로 높이는 것뿐이므로 제출한 작업 만큼 빠르게 실행된다.

특정 QoS 클래스가 있는 디스패치 대기열을 만들 수도 있다.

예를 들어, 항상 백그라운드에서 실행하려는 작업이 있는 경우 모든 작업을 백그라운드로 실행하는 Queue를 만들 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/a23aa180-f8d1-4ce4-b976-d3516ca03562/image.png)


Dispatch Queue에서 비동기화하면 비동기화되는 지점에서 실행 컨텍스트를 캡처한다.

이제 실행 컨텍스트는 QoS와 같은 것을 의미한다.

또한 현재 가지고 있는 로그인 컨텍스트를 의미한다.

이것에 대한 더 많은 제어를 원한다면 DispatchWorkItem을 사용하여 실행 방법을 더 많이 제어할 수 있는 항목을 만들 수 있다.

즉, 해당 항목을 만들고 나중에 사용할 수 있도록 저장할 수 있으며, 마지막으로 실행할 때 항목을 만들 때의 속성과 함께 dispatch에 제출한다.

![](https://velog.velcdn.com/images/xoxo0223/post/617e7da4-4d5e-4322-addd-531314db80c0/image.png)


DispatchWorkItem에 매우 유용한 또 다른 부분이 있다.

이건 완료되기를 기다리는 거임

wait 메서드를 사용하여 진행하기 전에 해당 작업 항목을 완료해야 함을 dispatch에 표시할 수 있다.

Dispatch는 QoS까지 작업 우선 순위를 높여 응답한다.

따라서 dispatch는 작업 항목을 완료하기 위해 올려야 하는 Queue를 알고 있다.

그리고 세마포어 및 그룹 대기는 이 소유권 정보를 저장하지 않기 때문에 매우 중요하다.

즉, 세마포어를 기다리는 경우 세마포어 신호 앞에 있는 항목이 더 빨리 실행되지 않는다.

### 이제 객체의 관점에서 그것이 의미하는 바에 대해 아라보자,,

![](https://velog.velcdn.com/images/xoxo0223/post/dc3f315a-46bc-4bad-9ff6-796722b037da/image.png)

동기화는 Swift에서 언어의 일부가 아니다.

전역 변수는 atomic임

클래스의 프로퍼티, lazy 프로퍼티는 atomic하지 않음

- atomic
    
    • `atomic`하다는 것은 프로그래밍에서 데이터의 변경이 동시에 일어난 것처럼 보이게 하는 것을 의미한다. 데이터의 값을 변경하는 것은 항상 변경하는 시간이 필요하다. `atomic`한 데이터에 변경이 이뤄질 때는 변경하는 시간에 lock을 건다. 그래서 데이터를 변경하는 시간이 없어보이게(변경시에 아무런 작업이 일어나지 않으므로) 만든다.
    
    • 그래서 property가 `atomic`하다는 것은 멀티 쓰레드 환경에서 데이터가 반드시 변경 전, 변경 후의 상황에서만 접근되도록 하는 것을 보장한다. 즉, 데이터의 변경 중에는 접근이 불가능하다. 바꿔 말하면, `atomic` property는 항상 serial하게 접근된다.
    
    • `atomic` property라는 것은 `atomic` setter를 갖는 것을 의미한다. 즉, 해당 property의 setter가 동작하고 있을 때는 `atomic` property의 접근이 불가능하다.
    
    ObjectiveC Property는 기본적으로 `atomic`으로 선언된다. 다만, `atomic` property는 property 접근을 block하는 로직을 갖고 있기 때문에 성능이 느린 경향이 있다. 그래서 많은 ObjectiveC Property에는 `nonatomic`이 쓰여 있다.
    
    - Swift는 Thread-Safe를 고려하고 디자인한 언어가 아니기 때문에 모든 property는 non`atomic`이며 별도로 `atomic`도 지정할 수 없다.(GCD를 통해 적절히 컨트롤 해야한다.)
    
    [Thread Safe와 Swift Class · Issue #16 · hcn1519/TILMemo](https://github.com/hcn1519/TILMemo/issues/16#:~:text=5%20Feb%202019-,Atomic/non%20Atomic,-atomic%20%2D%20%EC%A4%91%EB%8B%A8%EB%90%98%EC%A7%80%20%EC%95%8A%EB%8A%94)
    
    [Swift Tip: Atomic Variables](https://www.objc.io/blog/2018/12/18/atomic-variables/)
    
    [Atomic vs. Non Atomic Properties Crash Course](https://medium.com/@YogevSitton/atomic-vs-non-atomic-properties-crash-course-d11c23f4366c)
    

이것이 의미하는 바는 초기화 컨텍스트에서 이러한 속성을 동시에 호출하는 경우 lazy initializer는 두번 실행될 수 있다는 것이다.

그래서 동기화를 해야한다!!!

**There is no such thing as a benign race**
이것이 의미하는 바는 동기화 지점을 잊어버리면 충돌이 발생하거나 앱 사용자의 데이터가 손상된다는 것이다.

동기화를 위해 무엇을 사용하냐?

전통적으로 lock을 사용한다.

![](https://velog.velcdn.com/images/xoxo0223/post/c525af9a-d143-48be-aa84-2a0ffd6f3fc7/image.png)


그리고 Swift에서는 전체 Darwin 모듈을 마음대로 사용할 수 있기 때문에 구조체 기반의 전통적인 C lock을 실제로 볼 수 있다.

그러나 Swift는 학습된 모든 것이 이동할 수 있으며 뮤텍스 또는 lock과 함께 작동하지 않는다고 가정한다.

따라서 Swift에서는 이러한 종류의 lock을 사용하지 않는 것이 좋다.

뮤텍스락 안쓰는게 좋다는 말인 듯

![](https://velog.velcdn.com/images/xoxo0223/post/400fdd0a-5448-4d77-b19e-657a3da22675/image.png)


그러나 전통적인 lock을 원할 경우 사용할 수 있는 것은 Foundation.Lock이다.

왜냐하면 기존의 구조체 기반 C잠금과 달리 클래스이고 앞서 언급한 문제가 발생하지 않기 때문이다.

![](https://velog.velcdn.com/images/xoxo0223/post/e46108fe-7ef4-4c13-9aa9-b28059145e16/image.png)



더 작고 C에 있는 lock 처럼 보이는 것을 원하면 Objective-C를 호출하고 lock이 ivar인 Objective-C에 기본 클래스를 도입해야 한다.

그런 다음 lock, unlock 메서드를 노출하고 이 클래스를 하위클래스화할 때 Swift에서 호출할 수 있도록 필요한 경우 잠금을 시도한다.

우리가 도입한 새로운 API입니다.해적의 침입에 취약하지 않습니다.우리가 복제한 회전 잠금과 달리 회전하지 않습니다. 다시 살아나는 것이 가장 중요합니다.

즉, 이것은 GCD 이야기이므로 동기화 목적으로 디스패치 대기열을 사용하는 것이 좋습니다.

라고함 뭔 소린지 모름

암튼 Dispatch Queue를 사용하는 게 좋다고함

![](https://velog.velcdn.com/images/xoxo0223/post/e29559ae-40d0-4aef-b4e7-0fe8f6ad626b/image.png)


오용하기 훨씬 어렵고 더 견고하다.

코드는 범위가 지정된 방식으로 실행되므로 잠금을 해제하는 것을 잊어서는 안된다.

다른 하나는 Queue가 디버깅 도구에서 Xcode의 런타임과 실제로 훨씬 더 잘 통합된다는 것이다.

그럼 Queue를 사용해서 동기화 하는 방법은 무엇일까?

automic property를 구현하는 문제를 보자

![](https://velog.velcdn.com/images/xoxo0223/post/5ba21f10-56c3-41f4-9ece-2e13f44a7ca0/image.png)



위 코드에는 안전한 방법으로 접근하고자 하는 내부 상태를 가진 객체가 있다.

우리는 동기화를 위해 Queue를 사용할 것이다.

getter와 setter를 작성해보자

getter는 동기화를 통해 내부 상태를 반환하는 것이다.

![](https://velog.velcdn.com/images/xoxo0223/post/ebb799d8-2c0b-45ca-8996-a41d49e5253c/image.png)

그것은 우리에게 상호 배제를 제공한다.

setter는 간단함

![](https://velog.velcdn.com/images/xoxo0223/post/4b675fb7-b95c-42b9-a07a-41743dcf6ff6/image.png)


새로운 상태와 동기화 및 대기열의 기타 보호를 설정하기만 하면 된다.

이 패턴은 매우 간단하고 실제로 변형해서 훨씬 더 복잡한 제품으로 확장할 수 있다.

Queue가 디버깅 도구와 더 잘 통합된다고 말했음

그리고 더 많은 기능을 가지고 있음

사전 조건을 표현할 수 있음

![](https://velog.velcdn.com/images/xoxo0223/post/529f2491-c079-4ee2-bb18-4fdf26ceeb84/image.png)


그것은 당신이 당신의 코드에 그 주어진 큐에 정말로 매달릴 필요가 있는 불변이 있다는 것을 표현할 수 있게 해주며, 당신은 그렇게 했습니다.

해당 대기열에 있다는 디스패치 전제 조건입니다.

때로는 그 반대가 실제로 유용합니다.

해당 대기열과 동기화할 수 있다는 것을 알고 대기열에 있지 않다는 전제 조건을 이렇게 표현하기 때문에 주어진 코드 조각이 해당 대기열에서 실행되지 않도록 하고 싶습니다.

뭔소린지 모르겠음

이것은 동기화에 관한 것이다. 상태를 동기화하는 것

동기화가 필요하지 않은 전달 값이 중단되는 방식으로 어플리케이션을 구성할 수 있다면 좋겠지만

실제로는 단순하고 명백한 하위 시스템에서 액세스할 일부 객체가 필요하다.

이것이 의미하는 바는 모든 하위 시스템에 이러한 객체에 대한 참조가 있고 이를 제거하는 것이 실제로 어려울 수 있다는 것이다.

![](https://velog.velcdn.com/images/xoxo0223/post/14a84226-bc5c-4175-8d4e-00f38afb0dd9/image.png)


4단계 상태 임무 안내

1. Single threded setup
    - 설정은 객체를 만들고 목적에 필요한 프로퍼티를 제공하는 것이다.
2. activate the concurrent state machine
    - 이 객체를 활성화 하려고 한다.
    - 이것이 의미하는 바는 실제로 이 객체를 다른 하위 시스템에 알릴 수 있다는 것이다.
3. invalidate the concurrent state machine
    - 무효화는 모든 부분, 모든 하위 시스템이 이 객체가 사라지고 있다는 사실을 확인하여
4. 할당이 해제되도록 하는 것이다.

이것은 매우 추상적이기 때문에 구체적인 예로 보자!!

두 가지 하위 시스템에 집중해보자

![](https://velog.velcdn.com/images/xoxo0223/post/9e00e6c0-9b69-45cd-a547-d416e102b2a8/image.png)


먼저 title bar와 같은 학목을 처리하는 UI가 있다.

그리고 데이터 변환 하위 시스템의 경우 일부 작업을 수행하기 시작할 때 사용자에게 시각적 표시를 제공하도록 하위 시스템에서 일종의 상태 변경을 관찰할 수 있다고 가정한다.

데이터 변환 중에는 systemStarted를 통해서 indicator 돌아가고 끝나면 시각적 표시가 사라짐

그러면 BusyController를 어떻게 구현할까???

첫 번째는 설정이다.

![](https://velog.velcdn.com/images/xoxo0223/post/6470da82-5431-41bc-8b80-e3ba09e98f9f/image.png)


설정은 코드, 애니메이션 등에 대해 원하는 프로퍼티를 선택하는 것이다.

이건 개발자한테 달림

그런 다음 해당 객체를 사용하기 시작하고 그것이 활성화이다.

![](https://velog.velcdn.com/images/xoxo0223/post/d23de1ca-bad1-4545-84af-494d86e5f46c/image.png)


이것이 의미하는 바는 우리가 이러한 상태 알림, 상태 변경 알림 수신을 시작하기를 원하므로 해당 하위 시스템에 등록하고 알림이 기본 Queue에서 수신되도록 요청하는 것이다.

우리는 거기에서 로직을 처리하고자 하는 UI를 만들고 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/95068f29-e388-4e27-9517-61d6fe03fbea/image.png)

시각적 표시가 필요하지 않거나 데이터 변환 하위 시스템을 사용하지 않는 앱의 일부가 있으며 해당 컨트롤러의 리소스를 회수하고 싶고 제고하고 싶은 부분이 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/ddbcce8b-4256-46b4-ba87-6588e7ce70b6/image.png)


메인 스레드가 이 BusyController를 실제로 소유하는 유일한 하위 시스템이므로 없애겠다고 말하고 하위 시스템에서 deinit하고 unregister한 다음 최고가 되기를 바란다.

이건 동작을 안함 그 이유 2가지

BusyController는 UI, 기본 Queue 및 UI의 참조이다.

![](https://velog.velcdn.com/images/xoxo0223/post/48fdc0de-9b8a-4a95-a00e-94f733768499/image.png)


그러나 데이터 변환 하위 시스템에 등록할 때 이 객체로 참조를 가져왔을 가능성이 매우 높다.

즉, 기본 스레드가 가지고 있는 참조를 제거해도 여전히 하나가 남아있기 때문에 deinit이 실행되지 않는다.

retain cycle

![](https://velog.velcdn.com/images/xoxo0223/post/7bbe6d44-5179-4b52-9603-d94c4fec47aa/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/28872354-090a-4095-adf0-c3b2dc80d2cc/image.png)


이를 해결하는 방법이 뭘까

약한 참조

![](https://velog.velcdn.com/images/xoxo0223/post/4e727c92-242e-4bda-810e-2a717638682a/image.png)


그러나 이것이 앱의 실제 모습이 아니기 때문에 끝이 아니다.

그래프 객체는 훨씬 더 복잡하다.

Octopus 객체와 같이 참조 및 다른 객체의 무리를 보유하는 객체를 갖는 것은 드문 일이 아니다.

![](https://velog.velcdn.com/images/xoxo0223/post/e7cda830-726a-41b0-97c7-67993c34e047/image.png)

계속해서 메인 스레드에서 해당 참조를 제거할 것이다.

그리고 이전과 달리 이것은 버려진 메모리가 아니다.

이 Octopus 객체는 이 참조가 있다는 것을 알고 있기 때문이다.

그러나 데이터 변환 하위 시스템의 컨텍스트에서 해당 Octopus 객체를 제거하면 BusyController에 있는 참조를 제거할 것이다.

이 참조는 deinit이 하는 일이기 때문에 재량에 따라 실행된다.

그런 다음 문제가 있다. 그렇게 하려면 해당 데이터 구조체를 소유한 Dispatch Queue와 동기화해야 할 가능성이 높기 때문이다.

![](https://velog.velcdn.com/images/xoxo0223/post/fca6e59a-927a-4149-8829-6ba22b5a54a3/image.png)


예상대로 교착 상태에 빠지게 된다.

암튼 deinit에서 등록을 취소하고 싶지 않다.

어캐할까

명시적 함수 호출로 처리하여 이를 수정한다.

![](https://velog.velcdn.com/images/xoxo0223/post/1c51d01d-8958-4f58-98f2-4916c4efc487/image.png)


그리고 이 무효화에 따라 등록 시 이를 수행한다.

또한 전제 조건이 있으므로 이 객체, BusyController는 실제로 메인 스레드에서 관리해야 하고 API 사용자가 제대로 수행하는지 확인하고 싶기 때문에 이를 사용한다.

따라서 이것이 메인 스레드 또는 메인 Queue에서만 발생한다는 전제 조건을 원할 것이다.

하지만 이게 다가 아니다.

마지막 문제가 있다.

이 모든 것이 메인 스레드에서 발생한다. 이 데이터 변환 하위 시스템이 여전히 상태 전환을 전송하고 무효화 하는 시점에 여젼히 발생하고 있는 일부가 있을 수 있다.

어떻게 해결할까?

무효화를 실제 상태로 추적하려고 한다.

![](https://velog.velcdn.com/images/xoxo0223/post/90c1dee2-d0b3-4337-bad9-0f3575d44664/image.png)


예를 들어

객체의 불로 무효화를 추적하려고 하고 언제 수행했는지 기억한다.

동시에 더 많은 전제조건을 추가하고 객체가 할당 해제되기 전에 적절하게 무효화되었는지 확인하고 시행한다.

버그를 찾는 데 도움이 될 것이다.

이제 상태 전환에 대한 알림을 처리하는 코드에서 객체가 무효화 되었음을 관찰하고 실제로 알림을 떨어트리고 열린 방식으로 UI를 업데이트할 수 있기 때문이다.


![](https://velog.velcdn.com/images/xoxo0223/post/ef69f720-2b42-49dc-a07e-cc7bcb671a7f/image.png)

겁나 어려움

## GCD Object Lifecycle

첫번째 단계는 설정이다.


![](https://velog.velcdn.com/images/xoxo0223/post/2d997494-6111-44a0-a96b-da8eee150998/image.png)

Dispatch object에 대한 설정은 객체를 빌드할 때 수행할 수 있는 모든 작업과 전달할 수 있는 모든 속성이다.

DispatchSource는 모든 속성을 모니터링 하는 친구

소스에도 핸들러가 있고 이벤트 핸들러는 특히 모니터링 리소스가 실행될 때 실행되는 코드이며 이는 보류중인 이벤트이다.

객체를 설정하고 사용할 준비가 되면 객체를 사용하고 활성화하려고 한다.


![](https://velog.velcdn.com/images/xoxo0223/post/24546966-6790-4ecb-b3e8-ae21435b8126/image.png)

많은 dispatch object는 그룹이나 queue와 같은 명시적인 무효화를 실제로 필요로 하지 않는다.

왜냐하면 그것들은 사용을 중단한다는 사실에 의해 비활성화되기 때문이다.

그러나 소스랑은 상당히 다르고 소스에는 명시적 무효화가 있다.

소스에서 취소하면 예상한 대로 수행된다.

즉, 모니터링 중인 항목에 대한 이벤트 가져오기가 중지된다.

![](https://velog.velcdn.com/images/xoxo0223/post/ce4f06b9-9ac7-4b8a-917f-0557440007b0/image.png)![](https://velog.velcdn.com/images/xoxo0223/post/a47d4b12-b956-493c-bd5e-72663be8d6b2/image.png)



그것만이 아님

소스에 취소 핸들러를 설정하면 취소 시간에 queue의 대상에서 실행된다는 것이다.

실제로 프레임 메모리 닫기와 같이 모니터링 중인 리소스를 제거하려는 위치이다.

마지막으로 소스에 대한 취소는 핸들러가 파괴될 때이다.

핸들러는 클로져임

주제를 통해 캡처하거나 소스 자체를 캡처한다.

reading cycle의 일부가 될 수 있다.

취소를 호출하면 해당 reading cycle을 중단할 수 있다.

그렇기 때문에 항상 소스를 취소하는 것이 매우 중요하다.

![](https://velog.velcdn.com/images/xoxo0223/post/507d99e9-3f93-494f-bf32-c69d59aefaaa/image.png)

