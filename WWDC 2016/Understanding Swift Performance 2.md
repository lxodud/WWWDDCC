# [WWDC 2016] Understanding Swift Performance 2
[Understanding Swift Performance - WWDC16 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2016/416/)

23:30 ~

Swift 코드를 읽고 작성할 때마다 이 인스턴스가 스택에 할당될까? 아니면 힙에 할당될까?, 이 인스턴스를 전달할 대 오버헤드를 포함하는 참조가 얼마나 되는지라고 생각해야한다.

이 인스턴스에서 메서드를 호출하면 정적 또는 동적으로 디스패치 되는가? 필요하지 않은 역동성에 비용을 지불한다면 성능이 저하될 것이다.

Swift에서는 구조체를 더 많이 활용할 수 있다.

구조체를 사용하여 다형성 코드를 작성하는 방법은 무엇인가?

→ 프로토콜 지향 프로그래밍

![](https://i.imgur.com/ckXe7sp.png)

프로토콜 타입의 변수가 저장되고 복사되는 방법과 메서드 디스패치가 작동하는 방법을 살펴보자

![](https://i.imgur.com/A8bSkJU.png)

draw 메서드를 선언하는 Drawable 프로토콜이 있다.

그리고 프로토콜을 준수하는 값 타입 Point, Line이 있음

![](https://i.imgur.com/1lLYJ6Y.png)

해당 프로토콜을 채택하는 클래스도 가질 수 있음

그러나 클래스와 함께 제공되는 reference semantic이 의도하지 않은 공유로 인해 사용하지 않기로 결정했다함

![](https://i.imgur.com/wFAlOpJ.png)

위 프로그램은 여전히 다형성이 있음

Drawable 프로토콜 타입의 배열에 Point 타입과 Line 타입의 값을 모두 저장할 수 있음

그러나 이전과 비교하면 한 가지 달라졌다고함

![](https://i.imgur.com/VXvuUy5.png)

Line이랑 Point는 이전에 본(앞에서 나온거) 메커니즘인 V-Table 디스패치를 수행하는 데 필요한 공통 상속 관계를 공유하지 않는다.

그러면 Swift는 어떻게 올바른 메서드로 디스패치할까?

![](https://i.imgur.com/H3PNrn9.png)

이 질문에 대한 답은 Protocol Witness Table이라는 테이블 기반 메커니즘이다.

![](https://i.imgur.com/KiJyVIc.png)

어플리케이션에서 프로토콜을 구현하는 타입당 테이블 하나가 있다.

![](https://i.imgur.com/Ph2zINm.png)

그리고 해당 테이블의 항목은 타입의 구현에 연결된다.

![](https://i.imgur.com/oD7Nn9u.png)

![](https://i.imgur.com/VcgSpMk.png)

그래서 배열의 요소에서 테이블로 어떻게 이동하는가? 그리고 의문점이 하나 더 있음

이제 Line및 Point 값 타입이 있다.

Line은 4 words가 필요하다. Point는 2 words가 필요하다.

크기가 같지 않다!!

그러나 배열은 고정된 오프셋에 요소를 균일하게 저장하려고 한다.

이게 어떻게 작동하는가?

이 질문에 대한 답은 Swift가 Existential Container라는 특별한 스토리지 레이아웃을 사용한다는 것이다.

![](https://i.imgur.com/5yVQqgu.png)

해당 컨테이너의 처음 세 단어는 valueBuffer용으로 예약되어 있다.

![](https://i.imgur.com/DJ0d5kv.png)

2 words만 필요한 Point 같은 작은 타입이 이 valueBuffer에 적합하다.

그럼 Line은 4 words가 필요한데 어캐하냐??

![](https://i.imgur.com/OSxT4eG.png)

이 경우 Swift는 힙에 메모리를 할당하고 거기에 값을 저장하고 Existential Container에 메모리에 대한 포인터를 저장한다.

Line과 Point 사이에 차이가 있음을 확인헸고 Existential Conatiner는 이 차이를 관리해야 한다.

어떻게 할까??!

[](https://i.imgur.com/3wL1B4q.png)

다시 테이블 기반 메커니즘이다. 이 경우 이를 Value Witness Table 이라고 한다.

VWT는 value의 수명을 관리하고 프로그램의 타입별로 하나의 테이블이 있다.

이제 로컬 변수의 수명을 살펴보고 이 테이블이 어떻게 작동하는지 살펴보자

![](https://i.imgur.com/q0vrOPI.png)
프로토콜 타입의 로컬 변수 수명이 시작될 때 Swift는 해당 테이블 내에서 할당 함수를 호출한다.

![](https://i.imgur.com/DN2n1Un.png)

이 함수는 이제 Line Value Witness Table에 있으므로 힙에 메모리를 할당하고 Existential Container의 valueBuffer 내부에 해당 메모리에 대한 포인터를 저장한다

![](https://i.imgur.com/t9AxSym.png)

다음으로 Swift는 지역 변수를 Existential Container로 초기화하는 할당 소스의 값을 복사해야한다.

즉, 우리는 여기에 Line을 가지고 있고 우리의 value witness table의 copy함수는 그것을 힙에 할당된 valueBuffer로 복사할 것이다.

![](https://i.imgur.com/Jih7KPm.png)

프로그램이 계속 되고 지역 변수의 수명이 다했다.
그러면 Swift는 destruct 함수를 호출하여 우리 타입에 포함될 수 있는 값에 대한 reference count를 줄인다.

![](https://i.imgur.com/EHyU4gb.png)

그리고 마지막으로 Swift는 할당해제 함수를 호출하여 힙에 할당된 메모리를 해제한다.

Point는 valueBuffer에 바로 넣을 듯

Swift가 일반적으로 다른 종류의 값을 처리하는 방법에 대한 메커니즘을 보았다.

VWT 테이블에 어떻게 도달할까?

![](https://i.imgur.com/Zc3u3Fb.png)

Existential Container에는 VWT에 대한 참조가 있다

마지막으로 PWT에는 어떻게 도달하는가?

![](https://i.imgur.com/Yzqmqqo.png) 

그것도 Existential Container에 참조가 있다.

지금까지가 Swift가 프로토콜 타입의 값을 관리하는 메커니즘이다.

작동중인 Existential Container를 보기 위해 예제를 살펴보자

![](https://i.imgur.com/MaHmEUT.png)

위 예제에는 프로토콜 타입 파라미터를 로컬로 가져오고 그것에 대해 draw 메서드를 실행하는 함수가 있다.

그런 다음 Drawable 타입의 로컬 변수를 생성하고 Point로 초기화한다.
그리고 이 로컬 변수를 drawACopy 함수 호출에 인수로 전달한다.

Swift 컴파일러가 생성하는 코드를 설명하기 위해 이 예제 아래에 Swift를 의사 코드 표기법으로 사용하겠다.

![](https://i.imgur.com/nCIlKIC.png)

Existential Container의 경우 valueBuffer에 대한 3words 저장소와 VWT, PWT에 대한 참조가 있는 구조체가 있다.

![](https://i.imgur.com/z5xTMIj.png)

drawACopy 함수 호출이 실행되면 인수를 받아 해당 함수에 전달한다.

생성된 코드에서 우리는 Swift가 인수의 Existential Container를 해당 함수에 전달하는 것을 볼 수 있다.

![](https://i.imgur.com/CfFNslz.png)

함수가 실행을 시작하면 해당 매개변수에 대한 로컬 변수를 만들고 여기에 인수를 할당한다.

![](https://i.imgur.com/UpB9NgG.png)

따라서 생성된 코드에서 Swift는 Stack에 Existential Container를 할당한다

![](https://i.imgur.com/oPj6aUg.png)

다음으로 인수 Existential Container에서 VWT, PWT를 읽고 로컬 Existential Container의 필드를 초기화한다.

![](https://i.imgur.com/4ns6cOb.png)

다음으로 value witness 함수를 호출하여 필요한 경우 buffer를 할당하고 값을 복사한다.
이 예제에서는 동적 힙 할당이 필요하지 않도록 point를 전달했다.
이 함수는 인수의 값을 로컬 Existential Container의 valueBuffer로 복사한다.

Line을 전달했다면?

![](https://i.imgur.com/qbIiWRE.png)

이 함수를 buffer를 할당하고 거기에 값을 복사한다. (힙에 할당)

![](https://i.imgur.com/qwOXuIe.png)

다음으로 draw 메서드가 실행되고 Swift는 Existential Container의 필드에서 PWT를 찾고, 해당 테이블의 고정 오프셋에서 draw 메서드를 찾고 구현으로 이동한다

여기서 또 다른 value witness 호출인 projectBuffer가 있다.

draw 메서드는 값의 주소를 입력으로 예상한다.

![](https://i.imgur.com/WNuEZfZ.png)

그리고 값이 인라인 버퍼에 맞는 작은 값인지(Point) 여부에 따라 이 주소가 Existential Container의 주소이거나 

![](https://i.imgur.com/hC7fqdy.png)

인라인 valueBuffer에 맞지 않는 큰 값이 있는 경우(Line) Heap에 할당된 메모리의 주소가일 것이다.

그래서 이 value witness 함수는 타입에 따라서 이 차이를 추상화한다.

일단 draw 메서드는 인자로 값의 주소를 받을거임 근데 Point는 값이 valueBuffer 주소고 Line은 힙 주소인데 이 차이를 추상화하는게 projectBuffer라는 소리같음

![](https://i.imgur.com/Gp98Wkq.png)

draw 메서드가 실행되고 완료되며 이제 함수가 끝났다. 이는 파라미터에 대해 생성된 로컬 변수가 스코프를 벗어남을 의미한다.

![](https://i.imgur.com/UA9Ilsu.png)

따라서 Swift는 값을 파괴하기 위해 value witness 함수를 호출한다. 이 함수는 값에 참조가 있는 경우 참조 횟수를 줄이고 

![](https://i.imgur.com/qiDuT3M.png)

버퍼가 할당된 경우 버퍼를 할당 해제한다.

![](https://i.imgur.com/sGiXXWB.png)
함수 실행이 완료되고 스택에 생성된 local existential container가 제거됩니다.

여기서 얻어갔으면 하는 한 가지는 Line, Point와 같은 구조체를 프로토콜과 함께 결합해서 동적 동작, 동작 다형성을 얻을 수 있도록 하는 것입니다.
Drawable 프로토콜 타입의 배열에 Line과 Point를 지정할 수 있습니다.

만약 이러한 동적인 기능이 필요하다면, 앞에서 보여준 클래스를 사용하는 것과 비교해서 좋은 비용입니다. 왜냐하면 클래스도 V-테이블을 통과하며, 참조 계수(reference counting)의 추가 오버헤드가 있기 때문입니다.

지역 변수가 어떻게 복사되는 지와 프로토콜 타입에서 메서드 디스패치가 어떻게 동작하는지 보았고 이제 Stored Property를 봅시다.

![](https://i.imgur.com/OmzJ18B.png)

위 코드에서는 프로토콜의 첫 번째와 두 번째 stored property인 drawable 프로토콜 타입을 포함하는 쌍이 있습니다

Swift는 어떻게 두 개의 저장 프로퍼티를 저장할까요?

![](https://i.imgur.com/tZ1nuFA.png)

이들은 enclosing struct의 인라인으로 저장됩니다.
따라서, 우리가 pair를 할당할 때, Swift는 해당 pair의 저장에 필요한 두 개의 existential container를 enclosing struct의 인라인으로 저장합니다.

![](https://i.imgur.com/HN1nvuq.png)
그런 다음 Line과 Point 쌍을 초기화하고 이전에 살펴본 것처럼 Line은 힙에 버퍼를 할당하고 Point는 inline valueBuffer에 맞고, existential container의 inline에 저장될 수 있습니다.

이제 이 표현을 통해 나중에 다른 타입의 값을 저장할 수 있습니다.

![](https://i.imgur.com/hlqwoy6.png)

second property에 Line을 저장할 수 있습니다. (정상 동작함)
두 개의 힙 할당을 하긴함!!

힙 할당 비용을 설명해보겠습니다.

![](https://i.imgur.com/mcg8eRJ.png)
Line을 만들고 pair를 만들어서 Line으로 초기화했습니다

![](https://i.imgur.com/CT8R9V1.png)
따라서 두 개의 힙 할당이 있습니다

![](https://i.imgur.com/gaxw0bI.png)
그 다음 pair의 복사본을 다시 만들고 스택에 두 개의 existential container와 두 개의 힙 할당을 만듭니다.

"Kyle은 우리에게 heap 할당이 비싸다고 말했습니다.
그렇다면, 4개의 heap 할당? Hmm." 이렇게 말할 수 있습니다. 
이것에 대해서 우리는 무언가를 할 수 있을까요? 

![](https://i.imgur.com/3iAVkR0.png)

existential container는 세 개의 word를 담을 수 있다는 것을 기억하세요. 그리고 reference는 기본적으로 한 개의 word이므로, 그 세 개의 word에 reference를 저장할 수 있습니다.

따라서, 만약 우리가 클래스로 우리의 Line을 구현한다면, 클래스는 reference semantics이므로 참조에 의해 저장됩니다. 이 참조는 valueBuffer에 맞을 것입니다.

![](https://i.imgur.com/1ZMjbgf.png)
Pair에서 첫 번째 참조를 두 번째 필드로 복사할 때, 참조만 복사되고 우리가 지불하는 비용은 추가 reference counting 증가뿐입니다.

지금 당신은, "잠깐만요. reference semantics가 가져오는 의도하지 않은 상태 공유에 대해 방금 들은 것이 아닌가요?" 라고 할 수 있습니다.

![](https://i.imgur.com/NsKx4mQ.png)

 그래서, 만약 우리가 우리의 쌍에서 두 번째 필드를 통해 x1 필드에 저장한다면, 첫 번째 필드는 그 변경 사항을 관찰할 수 있습니다. 이것은 우리가 원하는 것이 아닙니다. 우리는 value semantics를 원합니다
 
 이 문제를 해결할 수 있는 COW라는 기술이 있습니다.
 
 ![](https://i.imgur.com/VtF7wkm.png)
 
 따라서 클래스에 쓰기 전에 reference count를 확인합니다.
 
 우리는 같은 인스턴스에 대해 하나 이상의 참조가 있을 때 참조 계수가 1, 2, 3, 4, 5보다 클 수 있다는 것을 들었습니다.
 
 따라서 이 경우 인스턴스에 쓰기 전에 인스턴스를 복사한 다음 해당 복사본을 씁니다. 이렇게 하면 상태가 분리됩니다.
 
 ![](https://i.imgur.com/BdwdvwR.png)
 
 Line 내부에 저장소를 직접 구현하는 대신 Line 구조체의 모든 필드가 있는 LineStorage라는 클래스를 만듭니다.
 
 그런 다음 Line 구조체는 이 저장소를 참조합니다.
 그리고 값을 읽고 싶을 때마다 해당 스토리지 내부의 값을 읽으면 된다.
 
 그러나 값을 수정하거나 변경하려면 먼저 참조 횟수를 확인합니다.
 
 1보다 큰가? 이것이 여기서 isUniquelyReferenced 호출이 달성하는 것입니다.
이 호출이 하는 유일한 일은 참조 횟수를 확인하는 것입니다.

![](https://i.imgur.com/eLrG9De.png)

1보다 크거나 같은가? 그리고 참조 횟수가 1보다 큰 경우 Line Storage의 복사본을 만들고 이를 변경합니다.

여기까지가 구조체와 클래스를 결합, COW를 사용하여 간접 저장소를 얻는 방법이었습니다. (개어렵넹)
