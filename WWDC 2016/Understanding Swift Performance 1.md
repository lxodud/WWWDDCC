# Understanding Swift Performance 1

---

[Understanding Swift Performance - WWDC16 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2016/416/)

![](https://i.imgur.com/3lbgg66.png)

![](https://i.imgur.com/qBJcnao.png)

개발자로서 Swift는 탐색할 수 있는 광범위하고 강력한 디자인 공간을 제공함!

Swift는 코드 재사용 및 역동성을 위한 다양한 퍼스트 클래스 타입과 다양한 메커니즘을 가지고 있음

그럼 이 많은 디자인 공간에서 작업에 적합한 도구를 선택하려면 어떻게 해야할까??

먼저 Swift의 다양한 추상화 메커니즘의 모델링 의미를 고려해보자

![](https://i.imgur.com/rdVKWqa.png)

값 VS 참조 ??

추상화가 얼마나 동적으로 필요한가??

성능을 고려하여 디자인 공간을 좁혀보자

성능 영향을 고려하면 선택을 좁히기 좋다!

![](https://i.imgur.com/CjS0wgG.png)

Swift의 추상화 메커니즘의 성능 영향을 이해하는 가장 좋은 방법은 기본 구현을 이해하는 것이다.

![](https://i.imgur.com/A2zgkOm.png)

추상화 메커니즘 옵션을 평가할 때 고려하는 다양한 차원들임

- 오버헤드에 대한 멘탈 모델을 심화하기 위해 구조체와 클래스를 사용하여 일부 코드를 추적할 것이다.
- 그리고 배운 내용을 적용해서 Swift 코드를 정리하고 속도를 높이는 방법을 살펴보자!
- 프로토콜 지향 프로그래밍의 성능도 평가할꺼임
- 프로토콜, 제네릭 같은 고급 Swift 기능의 구현을 살펴보고 모델링 및 성능 영향을 더 잘 이해해보자

뭐할건지 요약: Swift가 사용자를 대신해서 컴파일하고 실행하는 메모리 표현과 생성된 코드 표현을 살펴볼꺼임

시작~

![](https://i.imgur.com/2vfG4hh.png)

![](https://i.imgur.com/Mdw1E9G.png)

추상화를 구축하고 추상화 메커니즘을 선택할 때

- 내 인스턴스가 스택이나 힙에 할당되는지??
- 인스턴스를 전달할 때 참조 카운팅 오버헤드가 얼마나 될지??
- 인스턴스에서 메서드를 호출하면 정적 또는 동적으로 디스패치 되는지??

자문해야한다.

빠른 Swift 코드를 작성하려면 활용하지 않는 역동성과 런타임에 대한 비용을 지불하지 않아야 한다.

그리고 더 나은 성능을 위해 서로 다른 차원 간에 언제 어떻게 거래할 수 있는지 배워야한다!

할당부터 시작
![](https://i.imgur.com/liSe9JA.png)

Swift는 사용자를 대신해서 자동으로 메모리를 할당 및 할당 해제함

스택에 할당하는 메모리의 일부

- 스택은 간단한 데이터 구조임
- 스택의 끝으로 밀고 스택의 끝에서 튀어나올 수 있음 (LIFO)
- 스택의 끝에만 추가하거나 제거할 수 있기 때문에 끝에 포인터를 유지하여 푸쉬, 팝을 구현할 수 있음
- 스택 끝에 있는 포인터를 스택 포인터라고 한다.
- 그리고 함수를 호출할 때 공간을 만들기 위해 스택 포인터를 약간 감소시키는 것만으로 필요한 메모리를 할당할 수 있다.
- 함수 실행이 끝나면 스택 포인터를 호출하기 전 위치로 증가시켜 메모리 해제를 간단하게 할 수 있음
![](https://i.imgur.com/gM2UDST.png)

스택보다 동적이지만 효율성이 떨어지는 힙

- 힙을 사용하면 스택에서 동적 수명으로 메모리를 할당할 수 없는 작업을 수행할 수 있다.
- 다만 이를 위해서는 보다 고급 데이터 구조가 필요하다.
- 힙에 메모리를 할당하려면 실제로 힙 데이터 구조를 검색해서 적절한 크기의 사용되지 않은 블록을 찾아야 한다.
- 작업이 끝나서 할당을 해제하려면 해당 메모리를 적절한 위치에 다시 삽입해야한다.
- 따라서 여기에는 스택에서와 같이 정수를 할당하는 것보다 더 많은 것이 관련되어있다.
- 그러나 이것이 반드시 힙 할당과 관련된 주요 비용인 것은 아니다.
- 여러 스레드가 동시에 힙에 메모리를 할당할 수 있기 때문에 힙은 잠금 또는 기타 동기화 메커니즘을 사용하여 무결성을 보호해야 한다. → 이게 꽤 큰 비용이다.
- 프로그램에서 힙에 메모리를 할당하는 시기와 위치에 조금만 더 신중을 가하면 성능을 극적으로 향상시킬 수 있다.
![](https://i.imgur.com/ITfXiGD.png)

![](https://i.imgur.com/Rvqntlk.png)

`x`, `y` 프로퍼티, `draw()` 메서드를 가진 `Point` 구조체가 있음

`Point` 인스턴스인 `point1`을 만들고 `point2`에 할당하여 복사본을 만들어줌

그리고 값 5를 `point2.x`에 할당한 다음 `point1`, `point2`를 사용함

![](https://i.imgur.com/iEYPPfI.png)


코드 실행을 시작하기 전에 `point1` 인스턴스와 `point2` 인스턴스를 위해 스택에 공간을 할당함

`point`는 구조체이기 때문에 `x`, `y` 프로퍼티는 스택의 line에 저장된다.

![](https://i.imgur.com/yUdtwvs.png)


`x`가 0이고 `y`가 0인 점을 구성하려고 할 때 스택에 이미 할당된 메모리를 초기화하는 것 뿐이다.

![](https://i.imgur.com/4j0nJAK.png)


`point1`을 `point2`에 할당할 때도, `point1`의 복사본을 만들고 이미 스택에 할당했던 `point2` 메모리를 다시 초기화하는 것이다.

`point1`과 `point2`는 독립적인 인스턴스이다.

즉, `point2.x`에 5라는 값을 할당하면 `point2.x`는 5이지만 `point1.x` 는 여전히 0이다.

이것을 `value semantics`라고 한다.

그런 다음 `point1`을 사용하고 `point2`를 사용하면 실행이 완료된다.

![](https://i.imgur.com/zj6c7zr.png)

따라서 스택 포인터를 다시 증가시켜 `point1`, `point2`에 대한 메모리 할당을 간단하게 해제할 수 있다.

![](https://i.imgur.com/4bDEUV7.png)

## 구조체 대신 클래스를 사용해보자

![](https://i.imgur.com/plAmlln.png)

이전과 마찬가지로 스택에 메모리를 할당하는 것이다.

그러나 `point`의 프로퍼티를 실제로 저장하는 대신 `point1`과 `point2`에 대한 참조를 위해 메모리를 할당할 것이다.

힙에 할당할 메모리에 대한 참조이다.

따라서 (0,0)인 `point`를 구성할 때 Swift는 힙을 잠그고 해당 데이터 구조(힙)에서 적절한 크기의 사용되지 않은 메모리 블록을 검색한다.

![](https://i.imgur.com/tIE0Pzt.png)

그런 다음, `x`가 0이고 `y`가 0인 메모리를 초기화할 수 있으며 힙의 해당 메모리에 대한 주소로 `point1` 참조를 초기화할 수 있다.

힙에 할당할 때 Swift는 실제로 클래스 포인트에 4 `words`의 스토리지를 할당했다.

이것은 우리 `point`가 구조체일 때 할당된 2개의 `words`와 대조적이다.

이것은 이제 `point`가 클래스이고, `x` 및 `y`에 대해 저장된 것 외에 Swift가 우리를 대신하여 관리할 두 `words`를 할당하기 때문이다.

힙 다이어그램에서 파란색 상자로 표시된다.

![](https://i.imgur.com/345M93S.png)

`point1`을 `point2`에 할당할 때 `point1`이 구조체였을 때처럼 `point`의 내용을 복사하지 않는다.

대신 참조를 복사한다.

따라서 `point1`과 `point2`는 실제로 힙에 있는 동일한 point 인스턴스를 참조한다.

즉, `point2.x`에 값 5를 할당하면 `point1.x`와 `point2.x` 모두 값이 5가 된다.

이것을 reference semantics라고 하며 의도하지 않은 상태 공유로 이어질 수 있다.

![](https://i.imgur.com/pCF3oWx.png)

그런 다음 `point1`을 사용하고 `point2`를 사용하면 Swift가 힙을 잠그고 사용하지 않는 블록을 적절한 위치로 다시 retraining하는 대신 이 메모리 할당을 해제한다. (사용하지 않은 블록들을 적절한 위치로 재삽입한다??)

![](https://i.imgur.com/QoqznnE.png)

그 다음 스택을 팝할 수 있다.

![](https://i.imgur.com/KcZktc2.png)

위에서 본 것처럼 클래스는 힙 할당이 필요하기 때문에 클래스가 구조체보다 구성하는 데 더 많은 비용이 든다!!!

클래스는 힙에 할당되고 reference semantic을 갖기 때문에 클래스에는 ID 및 간접 저장과 같은 몇 가지 강력한 특성이 있다.

그러나 추상화에 이러한 특성이 필요하지 않다면 구조체를 사용하는 것이 더 좋다!

그리고 구조체는 클래스처럼 의도하지 않은 상태 공유에 취약하지 않다.

이를 적용해서 일부 Swift 코드의 성능을 향상시키는 방법을 살펴보자

![](https://i.imgur.com/DfsP013.png)

메시징 앱의 예이다.

유저들이 문자를 보내면 그 문자 뒤에 예쁜 풍선 이미지를 그리고 싶다.

`makeBallon()` 함수는 이미지를 생성하고 다른 풍선의 구성 또는 다른 풍선의 전체 구성 공간을 지원한다.

예를 들어, 사진에 있는 풍선은 올바른 방향과 꼬리가 있는 파란색이다.

이제 `makeBalloon()` 함수는 할당 시작 및 사용자 스크롤 중에 자주 호출하기 때문에 엄청 빨라야한다.

![](https://i.imgur.com/9LevuR3.png)

그래서 캐싱 레이어를 추가했다.

따라서 주어진 구성에 대해 이 풍선 이미지를 두 번 이상 생성할 필요가 없다. (`cache`에 있으면 계속 생성 안해도뎀)

이 작업을 수행한 방법은 `color`, `orientation`, `tail`을 문자열인 `key`로 직렬화 하는 것이다.

여기서 마음에 들지 않는 것!

문자열은 이 키에 대해 특별히 강력한 타입이 아니다.

구성 공간을 나타내기위해 사용하고 있지만 해당 `key`에 강아지 이름을 쉽게 넣을 수 있다.

안전하지 않음

또한 `String`은 실제로 힙에 간접적으로 해당 문자의 내용을 저장하기 때문에 많은 것을 나타낼 수 있다.

(길이가 정해져 있지 않아서 힙에 저장됨,,,)

따라서 makeBallon 함수를 호출할 때마다 캐시 적중이 있더라고도힙 할당이 발생한다.

리팩토링 고고

![](https://i.imgur.com/lXBLDnb.png)


Swift는 구조체를 사용해서 `color`, `orientation`, `tail`의 구성 공간을 나타낼 수 있음!!

이것은 `String`보다 이 구성 공간을 나타내는 훨씬 안전한 방법이다.

![](https://i.imgur.com/GIuH5IE.png)

구조체는 Swift의 `first class type`이기 때문에 `dictionary`에서 키로 사용할 수 있다.

이제 makeBalloon 함수를 호출할 때 캐시 적중이 있는 경우 `Attribute` 구조체를 구성하는 데 힙 할당이 필요하지 않기 때문에 할당 오버헤드가 없다.

스택에 할당할 수 있다!!

이것은 훨씬 더 안전하고 훨씬 더 빠를 것이다!!

성능의 다음 차원인 reference counting으로 가보자~~

개꿀잼

![](https://i.imgur.com/xcfVKQp.png)

Swift는 힙에 할당된 메모리를 언제 할당해제하는 것이 안전한지 어떻게 알까?

이것은 Swift가 힙의 모든 인스턴스에 대한 총 참조 수를 유지한다는 것이다.

그리고 인스턴스 자체에 유지한다.

참조를 추가하거나 참조를 제거하면 해당 참조 카운트가 증가하거나 감소한다.

그 수가 0에 도달하면 Swift는 아무도 힙에서 인스턴스를 가리키지 않는다는 것을 알고 해당 메모리를 할당 해제하는 것이 안전하다.

![](https://i.imgur.com/tsydA3J.png)

참조 카운팅과 관련하여 염두해야 할 핵심 사항은 이것이 매우 빈번한 작업이고 실제로 정수를 증가 및 감소시키는 것보다 더 많은 것이 있다는 것이다!!!

증가 및 감소를 실행하기 위한 몇 가지 수준의 간접 참조가 있다.

![](https://i.imgur.com/c3JQ3ck.png)

더 중요한 것은 힙 할당과 마찬가지로 여러 스레드의 힙 인스턴스에 참조를 동시에 추가하거나 제거할 수 있기 때문에 고려해야 할 `threa safe`가 있다는 것이다.

실제로 참조 카운트는 atomically하게 증가 및 감소시켜야 한다. 

그리고 참조 카운팅 작업의 빈도로 인해 이 비용이 추가될 수 있다.

이제 예제로 돌아가서 Swift가 실제로 무엇을 하는지 보자!

![](https://i.imgur.com/izBqRzb.png)

생성된 의사 코드가 있음

`retain`은 `refCount`를 atomically하게 증가시킬 것이고

`release`는 `refCount`를 atomically하게 감소시킬 거임

이런 식으로 Swift는 힙의 `point`에 얼마나 많은 참조가 살아있는지 추적할 수 있다.

![](https://i.imgur.com/2iLu5Bw.png)

![](https://i.imgur.com/RSTek5h.png)

힙에 `point`를 구성한 후 해당 `point`에 대한 하나의 live reference가 있기 때문에 1의 참조 카운트로 초기화 된다는 것을 알 수 있다.

![](https://i.imgur.com/GXOB28f.png)

프로그램을 진행하고 `point1`을 `point2`에 할당하면 이제 두 개의 참조가 있으므로 Swift는 `point` 인스턴스의 참조 횟수를 atomically하게 증가시키는 호출을 추가했다.

![](https://i.imgur.com/XB44UjX.png)

계속 실행하면서 `point1` 사용이 끝나면 Swift는 참조 횟수를 감소시키는 호출을 추가했음

`point1`은 더 이상 실제로 살아있는 참조가 아니기 때문임

![](https://i.imgur.com/wLUFi9C.png)

`point2`도 마찬가지

![](https://i.imgur.com/q7oqZuI.png)

이 시점에서 point 인스턴스를 사용하는 참조가 없으므로 Swift는 힙을 잠그고 메모리 블록을 반환하는 것이 안전하다는 것을 알고 있다.

구조체는 어떨까?

![](https://i.imgur.com/1xx9YHb.png)

![](https://i.imgur.com/R5ltVJf.png)

point 구조체를 구성할 때 관련된 힙 할당이 없다.

복사할 때 관련된 힙 할당도 없음

따라서 point 구조체에 대한 참조 카운팅 오버헤드가 없다!!

더 복잡한 구조체는 어떨까?

![](https://i.imgur.com/XD403E3.png)

String 타입의 텍스트와 UIFont 타입의 글꼴이 포함된 Label 구조체가 있다.

String은 실제로 힙에 문자의 내용을 저장한다.

따라서 참조 카운트가 필요하다.

UIFont도 클래스이기 때문에 참조 카운트가 필요하다.

![](https://i.imgur.com/z6dh0Iq.png)

메모리 표현을 보면 레이블에 두 개의 참조가 있다.

![](https://i.imgur.com/yWASiu7.png)

그리고 복사본을 만들 때 실제로 두 개의 참조를 더 추가한다.

Swift가 이것을 추적하는 방식 — 이러한 힙 할당은 유지 및 해제에 대한 호출을 추가하는 것이다.

![](https://i.imgur.com/a4XZlFD.png)

![](https://i.imgur.com/uQpG8sh.png)

클래스가 힙에 할당되기 때문에 Swift는 해당 힙 할당의 수명을 관리해야 한다.

![](https://i.imgur.com/0NrAIhD.png)


구조체에 참조가 포함된 경우 reference counting 오버헤드도 지불하게 된다.

따라서 참조가 두 개 이상인 경우 클래스보다 참조 계산 오버헤드가 더 많이 retain된다.

<img width="1071" alt="스크린샷 2022-11-05 오전 12 20 35" src="https://user-images.githubusercontent.com/85005933/220004614-9d60e093-e0bb-4643-9c82-9caf2859cc93.png">


다른 예제에서 이걸 어떻게 적용하는지 보자~
<img width="1033" alt="스크린샷 2022-11-09 오전 2 03 59" src="https://user-images.githubusercontent.com/85005933/220004668-82439433-e7ee-4c76-afa2-196a94d0366d.png">


메시지앱인데

사용자들은 단순히 문자 메시지를 보내는데 만족하지 않음

서로에게 이미지와 같은 첨부 파일을 보내고 싶음

그래서 위 코드는 앱의 모델 객체 구조체임

디스크 내 데이터의 경로를 저장하는 fileURL

무작위로 생성된 고유식별자 uuid → 클라이언트와 서버 및 다른 클라이언트 장치에서 이 첨부 파일을 인식할 수 있음

JPG, PNG, GIF와 같은 첨부 파일이 나타내는 데이터 유형을 저장하는 mimeType

failable initializer는 여기서 중요하지 않음

모든 mimeType을 지원하지 않기 때문에 사용했음 (지원안하면 작업 중단)

<img width="1033" alt="스크린샷 2022-11-09 오전 2 09 34" src="https://user-images.githubusercontent.com/85005933/220004705-d5ada4f5-f2d4-417b-a078-a2f9ededecdc.png">

이 구조체의 메모리 표현을 실제로 보면 3가지 프로퍼티 모두 참조 카운팅 오버헤드를 발생시킴

개선해보자

<img width="1033" alt="스크린샷 2022-11-09 오전 2 10 33" src="https://user-images.githubusercontent.com/85005933/220004750-e0fd6001-a367-48d8-9204-7603998e25e0.png">

일단 uuid가 128비트의 무작위로 생성된 식별자여야함 근데 String이면 아무거나 집어넣을 수 있음

따라서 UUID라는 구조체 타입을 사용하면 정말 좋다고함

개선한점

String이었던 uuid 필드에 지불하는 참조 카운팅 오버헤드를 제거함!

그리고 여기에 아무것도 넣을 수 없기 때문에 훨씬 더 엄격한 안전이 확보됨 오직 uuid만 넣을 수 있음

<img width="1033" alt="스크린샷 2022-11-09 오전 2 13 31" src="https://user-images.githubusercontent.com/85005933/220004836-ee11d660-cc49-4c4f-82d7-ef4fc4018be2.png">



isMimeType 검사를 구현한 방법을 보자

Swift는 고정된 집합을 표현하기 위한 훌륭한 추상화 매커니즘을 가지고 있다!

enum 쓸듯 ㅋㅋ
<img width="1033" alt="스크린샷 2022-11-09 오전 2 14 36" src="https://user-images.githubusercontent.com/85005933/220004982-2e6dc854-2905-4006-88ba-cf4bee8610e8.png">

<img width="1033" alt="스크린샷 2022-11-09 오전 2 16 48" src="https://user-images.githubusercontent.com/85005933/220005022-514e8038-a291-428b-90c6-72c3a0c07297.png">

값 타입이라 힙에 할당할 필요가 없어짐~

<img width="1033" alt="스크린샷 2022-11-09 오전 2 17 18" src="https://user-images.githubusercontent.com/85005933/220005121-cd83251b-d303-406d-acca-b86d5dbab23e.png">

개선된 구조체를 보면 훨씬 더 type safe함 (UUID, MimeType)

참조 카운트 오버헤드를 많이 줄임!!

메서드 디스패치로 넘어가보자!

<img width="1033" alt="스크린샷 2022-11-09 오전 2 51 23" src="https://user-images.githubusercontent.com/85005933/220005170-1f0a7dbe-5df3-46e9-9835-3b5a1bd684b8.png">

런타임에 메서드를 호출할 때 Swift는 올바른 구현을 실행해야한다.

<img width="1033" alt="스크린샷 2022-11-09 오전 2 52 45" src="https://user-images.githubusercontent.com/85005933/220005210-83f14bec-b1b3-4471-a769-f6b61d953713.png">

컴파일 타임에 실행할 구현을 결정할 수 있는 경우 이를 정적 디스패치라고 한다.

그리고 런타임에 올바른 구현으로 바로 이동할 수 있다.

그리고 이것은 컴파일러가 실제로 어떤 구현이 실행될 것인지에 대한 가시성을 가질 수 있기 때문에 정말 좋다.

따라서 인라인과 같은 것을 포함하여 코드를 최적화할 수 있다.

동적 디스패치와 대조됨

<img width="1111" alt="스크린샷 2022-11-28 오전 10 51 02" src="https://user-images.githubusercontent.com/85005933/220005244-5b954701-7b72-41fb-8e6a-88de1d21b569.png">

동적 디스패치는 어떤 구현으로 이동할지 컴파일 타입에 결정할 수 없다.

그래서 런타임에 실제로 구현을 조회한 다음 해당 구현으로 건너뛸 것이다.

따라서 동적 디스패치는 그 자체로는 정적 디스패치보다 비용이 많이 들지 않는다.

간접적인 수준은 하나뿐이다.

참조 카운팅 및 힙 할당과 같은 스레드 동기화 오버헤드는 없다.

그러나 동적 디스패치는 컴파일러의 가시성을 차단하므로 컴파일러의 최적화를 막게된다.

<img width="1111" alt="스크린샷 2022-11-28 오전 10 57 18" src="https://user-images.githubusercontent.com/85005933/220005301-ba9ea8f8-cd9a-45fe-8d29-af10e8d35825.png">

inlining이 뭘까?

위 코드를 보자

x, y 프로퍼티와 draw 메서드를 포함하는 구조체가 있음

drawAPoint 메서드는 Point를 가져와서 draw를 호출한다.

그런 다음 프로그램 본문은 (0,0)인 Point를 구성하고 해당 구조체를 drawAPoint에 전달한다.

drawAPoint 함수와 point.draw 메서드는 모두 정적으로 전달된다.

이것이 의미하는 바는 컴파일러가 어떤 구현이 실행될지 정확히 알고 있으므로 실제로 우리의 drawAPoint를 가져와 이를 drawAPoint 구현으로 대체한다는 것이다.

<img width="1111" alt="스크린샷 2022-11-28 오전 11 00 33" src="https://user-images.githubusercontent.com/85005933/220005344-68347f4c-853e-4c61-8650-1d97d75286c5.png">

그런 다음 point.draw 메서드를 사용할 것이다. 정적 디스패치이기 때문에 point.draw의 실제 구현으로 대체할 수 있다. 따라서 런타임에 이 코드를 실행하면 포인트를 구성하고 구현을 실행할 수 있으며 작업이 완료된다.

![](https://velog.velcdn.com/images/xoxo0223/post/3beb62cd-4a42-4701-a3e2-9c67c2917234/image.png)

draw 메서드 호출없이 그냥 해당 메서드의 구현을 바로 실행할 수도 있게됌

여기서 두가지 정적 디스패치의 오버헤드와 호출 스택의 관련 설정 및 해제가 필요하지 않음

이것이 왜 정적 디스패치가 동적 디스패치보다 더 빠른지에 대한 것이다.

단일 정적 디스패치와 단일 동적 디스패치를 비교했을 때 큰 차이는 없지만 정적 디스패치의 전체 체인에 대한 가시성을 갖게 된다.

반면 동적 디스패치 체인은 매 단계마다 추론을 하지 않고 더 높은 수준에서 차단될 것이다.

따라서 컴파일러는 호출 스택 오버헤드가 없는 단일 구현처럼 정적 메서드 디스패치 체인을 축소할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/f6495803-cade-4d07-8907-509d410cc574/image.png)


왜 다이나믹 디스패치를 사용하는 건가?

그 이유 중 하나는 다형성과 같은 강력한 기능을 가능하게 하기 때문임

위 그림에서 Drawable 추상 수퍼 클래스가 있는 전통적인 객체 지향 프로그램을 보면 custom 구현으로 draw메서드를 재정의하는 Point 하위 클래스와 Line 하위 클래스를 정의할 수 있음

![](https://velog.velcdn.com/images/xoxo0223/post/3bada1a6-ecae-4ccd-8d56-0488a5934a3d/image.png)

그리고 다형적으로 drawables 배열을 생성할 수 있음

Line, Point를 포함할 수 있고 각각의 draw메서드를 호출할 수 있음

배열안에서 요소들에 대한 참조를 저장하기 때문에 모두 같은 크기일 것이다.

![](https://velog.velcdn.com/images/xoxo0223/post/a833f084-094f-4155-8546-d82bf87d7683/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/4c61c74b-4578-4be0-9fd9-c08df8cc66b0/image.png)


여기서 d.draw는 Point가 될 수도 있고 Line이 될 수도 있기 때문에 서로 다른 코드 경로이다.

그렇다면 어떤 것을 호출할지 어떻게 결정할까? 컴파일러는 해당 클래스의 타입 정보에 대한 포인터인 다른 필드를 클래스에 추가하고 정적 메모리에 저장한다.

그래서 우리가 draw 메서드를 호출할 때 컴파일러가 실제로 우리를 대신하여 생성하는 것은 실행할 올바른 구현에 대한 포인터를 포함하는 타입 및 정적 메모리에 대한 가상 메서드 테이블이라는 항목에 대한 타입을 통한 조회이다.

→ 컴파일러가 해당 class 정보에 대한 것을 정적 메모리에 저장하고, 실제로 draw 메서드를 호출할 때, 컴파일러가 실제로 생성하는 것은 타입 및 정적 메모리의 가상 메서드 테이블이라고 하는 것을 조회한다.

![](https://velog.velcdn.com/images/xoxo0223/post/ce5f2c5e-ed24-42c2-992f-1d524412691f/image.png)


그런 다음 실제 인스턴스를 암시적 자체 매개 변수로 전달한다.

![](https://velog.velcdn.com/images/xoxo0223/post/c8aaaae1-4fe5-4689-891e-c6bcdfc768e1/image.png)

클래스는 기본적으로 메서드를 동적으로 전달한다.

이것은 그 자체로는 큰 차이를 만들지 못하지만 메서드 체인 및 기타 사항과 관련하여 인라인과 같은 최적화를 방지할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/ef99e2d8-f1ff-4ee8-942d-76db8c12f7f8/image.png)

모든 클래스에 동적 디스패치가 필요한 것은 아님

클래스를 하위 클래스로 분류하지 않으려는 경우 final 키워드를 사용할 수 있음

컴파일러는 이를 읽고 해당 메서드를 정적으로 디스패치함

또한 컴파일러가 응용 프로그램에서 클래스를 서브클래싱하지 않을 것임을 추론하고 증명할 수 있는 경우 기회에 따라 이러한 동적 디스패치를 정적 디스패치로 전환한다.

![](https://velog.velcdn.com/images/xoxo0223/post/1658b085-5e96-4a1b-8bb6-039c3a5ef41b/image.png)
