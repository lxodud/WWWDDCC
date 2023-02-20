# Meet async/await in Swift

[Meet async/await in Swift - WWDC21 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2021/10132/)

<aside>
💡 awaitable: await 키워드를 통해서 실행이 완료되기 전에 다른 작업으로 전환이 가능한 동작

</aside>

`UIKit` 은 `UIImage`에서 썸네일을 형성하는 기능이 있고 해당 작업을 완료하기 위해 동기 및 비동기 함수를 제공합니다.

![](https://i.imgur.com/OSdvs1E.png)

`fetchThumbnail` 함수가 `prepareThumbnail(UIKit이 제공하는 동기 함수)` 을  호출하면 완료될 때 까지 스레드가 다른 작업을 수행할 수 없습니다.

반면에, 비동기버전인 `prepareTHumbnail(of:completionHandler:)` 을 호출하면 실행하는 동안 스레드가 다른 작업을 수행할 수 있습니다.

![](https://i.imgur.com/Nn3ZHzG.jpg)


비동기 함수는 몇 가지 다른 방법으로 작업이 완료되었음을 알려줍니다.

- `completion handler`
- `delegate callbacks`
- 많은 것들이 비동기로 표시되고 값만 반환

비동기 함수의 공통점은 함수를 호출하면 스레드가 빠르게 차단 해제되어 작업을 시작한다. 따라서 장기간 실행되는 작업이 완료되는 동안 스레드가 다른 작업을 수행할 수 있습니다.

<img src = "https://i.imgur.com/7TeWOgG.png" width = "200">



해당 앱에는 `item list` 가 있고 각 행에는 서버에서 저장된 이미지의 축소판(썸네일)이 표시됩니다.

해당 목록에 썸네일을 준비할 때 `fetchThumbnail` 메소드가 호출됩니다.

**문자열을 UIImage로 변환하는 단계**

1. `thumbnailURLRequest` 메소드가 문자열에서 `URLRequest`를 생성
2. `URLSession`의 `dataTask` 메소드가 해당 `request`에 대한 데이터를 가져옴
3. `UIImage(data:)`가 해당 데이터에서 이미지를 만들고 `UIImage`의 `prepareThumbnail`메소드가 원본 이미지에서 축소판을 렌더링함

![](https://i.imgur.com/5BIlkQH.jpg)


각 단계에 작업들은 이전 작업의 결과에 영향을 받기 때문에 순서대로 수행해야 합니다.

1번, 3번에 `UIImage(data:)` 두 작업은 값을 빠르게 반환하므로 동기적으로 호출 되는 것이 좋습니다.

나머지 두 가지 작업(2, 4번)은 시간이 오래 걸리므로 비동기적으로 호출 되어야합니다.

기존의 비동기식으로 만든 `fetchThumbnail` 메소드 (`completion handler`를 이용) 동작은 다음과 같습니다.

- `fetchThumbnail` 매소드는 첫 번째 매개변수로 문자열, 호출자에게 출력을 다시 제공하는데 사용되는 `completion handler`을 사용합니다.

- `fetchThumbnail` 이 호출되면 먼저 `thumbnailURLRequest` 을 호출하고 동기식이라 `completion handler`이 필요하지 않습니다.

- 다음으로 `URLSession.shared.dataTask`를 호출하여 `URLRequest`와 `completion handler`를 전달합니다.
    - 비동기 작업을 시작하기 위해 재개되어야 하는 `URLSessionDataTask`를 동기적으로 생성합니다.
        - `URLSessionDataTask`: 서버에서 메모리로 데이터를 검색하는 `HTTP GET` 요청에 사용하는 테스크
        - 모든 작업은 기본적으로 일시정지(suspended) 상태로 시작하고 resume()을 호출해야 data task가 시작된다.
        - 동기적으로 task.resume()을 해준다는 뜻 같음

- `FetchTumbnail`로 리턴되고 스레드는 다른 작업을 수행합니다. (시간이 오래걸리는 작업이고 그 동안 스레드를 차단하고 싶지 않기 때문에 중요하다.)

- 위 작업에서 이미지가 다운로드 완료되거나 문제가 발생하는데 어느 쪽이든 `dataTask`에 전달된 `completion handler`가 데이터, 응답, 오류를 `optional value`와 함께 호출됩니다.

- 문제가 발생하면 completion handler를 호출해서 오류를 전달하고 정상적으로 작동하면 `UIImage(data:)` 를 사용해 데이터에서 이미지를 만듭니다. (동기식 이기 때문에 직선-코드)

- 이미지가 생성되지 않으면 그냥 끝이고 이미지가 생성되면 `prepareThumbnail` 메소드를 호출하고 `completion handler`를 전달합니다.

- `prepareThumbnail` 메소드가 썸네일을 준비완료하면 이미지와 함께 `completion handler`를 호출하고 그렇지 않으면 `nil`이 호출됩니다.

![](https://i.imgur.com/DLe8O9c.jpg)

위 코드에는 문제점이 있습니다.


> ☠️ `guard else return` 구문에 `completion handler` 호출을 하지 않았습니다.


> ☠️ 이는 `fetchThumbnail`의 호출자가 `fetchThumbnail`이 작업을 실패했을 때 아무런 알림을 받지 못하고 행이 업데이트 되지 않는 문제가 발생합니다.

</aside>

호출자에게 알림을 주는 것이 매우 중요하고 함수를 통한 모든 경로는 이를 알려야 합니다.

그렇게 하기 위해서는 오류가 발생하면 `completion handler`를 호출하고 오류를 전달해야 합니다.

![](https://i.imgur.com/sNcnMGy.jpg)

일반 함수는 오류를 `throw` 해야합니다.


> 📢 `Swift`는 함수를 통해 실행이 어떻게 진행되는 간에 값이 반환되지 않으면 오류가 발생하도록 합니다. (Void랑은 다름)


문제가 발생했을 때 `completion handler` 내에서 오류를 `throw` 할 수 없음 (Swift 에서는 강제할 방법이 없다.)

그래서 `guard` 문에서 `return` 했을 때 컴파일 오류가 발생하지 않았음

![](https://i.imgur.com/KyMDhZb.jpg)

버그가 발생할 수 있는 5개가 포함된 20줄의 코드로 구현 완료함

`result type`을 사용하여 조금 더 안전하게 만들 수 있음

> `result type`: `Generic Enumeration`로 선언되어 있고, 경우에 따른 연관값을 포함하여, 성공과 실패를 나타내는 값입니다.

![](https://i.imgur.com/7iqpMwe.jpg)

조금 더 안전하긴 하지만 코드가 더럽고 길게 만듬

### 기존 비동기 방식의 문제점

- `completion handler` 호출을 잊을 수 있다.
- `Swift`의 일반적인 `error handling` 매커니즘을 사용할 수 없다. (오류 처리가 힘듬)
- 코드가 더러움 (이해하기 힘들다?)
- 순서를 제어하기 힘듬

### `async/await`를 사용하면 위 문제점을 해결할 수 있다.

**`async/await` 사용법**

`compeltion handler` 대신에 함수에 `async` 키워드를 붙임

`throws` 키워드가 있으면 그 앞에 작성하고 그렇지 않으면 화살표 앞에 작성합니다.

![](https://i.imgur.com/BFtqqak.png)

이미지가 성공적으로 변환되면 썸네일이 전달되고 ( → UIImage)

오류가 발생하면 오류를 던집니다.(`Error Handling`)

`**async/await`로 구현한 `fetchThumbnail` 동작**

- `fetchThumbnail`이 호출되면 이전과 마찬가지로 `thunmnailURLRequest`를 호출하여 시작함
    ![](https://i.imgur.com/gLf2FEM.png)

    
    - `thunmnailURLRequest` 는 동기식이라 스레드가 차단되어 작업을 수행
- `URLSession.shared`에서 `data(for: request)`를 호출하여 데이터 다운로드
    ![](https://i.imgur.com/dQq06ZX.png)

    
    - 위에서 사용한 `dataTask`와 마찬가지로 비동기식이지만 `awaitable` 이다.
    - 따라서 호출된  후 스레드를 차단 해제하여 빠르게 일시중단된다. 그러면 스레드는 다른 작업을 자유롭게 수행할 수 있다.
    - `data` 메소드가 `throws`로 표시되어 있기 때문에 try를 붙인다.
    - 이전의 버전에서는 오류를 확인하고 `completion handler`로 호출해야 했는데 `awaitable` 버전에서는 `try` 키워드로 요악된다.
    

    > 📢 `throws async expression`을 다룰 때는 `try await`를 붙여라
    

    
- 데이터 다운로드가 완료되면 데이터 메소드가 다시 시작되어 `fetchThumbnail`로 돌아간다.
- 다음으로 `fetchThumbnail`은 다운로드한 데이터에서 `UIImage`를 생성하려고 시도한다.
- 성공하면 `thumbnail property`에 엑세스하여 해당 이미지에 대한 썸네일이 렌더링된다.
- 
    ![](https://i.imgur.com/GKMYLrY.jpg)

    
    - `thumbnail property` 도 awaitable이기 때문에 다른 작업을 자유롭게 수행할 수 있다.
- 썸네일이 렌더링되면 `fetchThumbnail`이 이를 반환 or 오류 발생가 발생한다.
    - 썸네일이 렌더링 되지 않으면 Swift는 오류를 발생하거나 값을 반환하도록 하므로 `completion handler` 처럼 조용히 넘어가지 않음

위 6줄이 끝이고 `completion handler`버전의 함수와 동일한 작업을 수행하고 모두 직선코드이다.

코드에서 두 번째 줄에 함수 호출이 없이 `await` 키워드가 붙어있는데 이것은 `thumbnail property`가 비동기이기 때문이고 함수 뿐만 아니라 `properties`, `initializers`도 비동기식일 수 있다. 

### 비동기식 `thumbnail property`

위에서 사용한 `thumbnail property`는 `SDK`의 일부가 아니고 Robert가 추가한 것이다.
![](https://i.imgur.com/iqRehI9.png)


`CGSize`를 형성하고 `byPreparingThumbnail(ofSize)`에 전달한 결과를 기다린다. (`byPreparingThumbnail(ofSize)` → `awaitable` 버전)

### **비동기식 `property`를 사용할 때 주의할 점**

- get이 있어야한다.
    - async 표시할 때 필요
    - `Swift 5.5` 부터 `property getter`도 `throw` 할 수 있음
- set은 있으면 안된다.
    - read-only properties만 async 가능
    

`**await`를 사용할 수 있는 또 다른 장소는 `in for loop`**

![](https://i.imgur.com/U2KW0fD.png)


비동기 시퀀스는 요소를 비동기적으로 `vend?`하는 것이기 때문에 다음 항목을 가져오는 것은 비동기임을 나타내는 위치에 `await` 키워드를 표시한다.

동작은 함수가 비동기 시퀀스를 반복하면서 다음 요소를 기다리는 동안 스레드의 차단을 해제하고 루프의 본문에 다음 요소를 사용하거나 요소가 남아있지 않은 경우 루프 뒤에 다시 시작할 수 있다.

`“Meet AsyncSequence”` session에서 추가설명

[Meet AsyncSequence - WWDC21 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2021/10058/)

많은 비동기 작업을 병렬로 실행하는데 관심이 있다면 `"Structured concurrency in Swift"` 세션

[Explore structured concurrency in Swift - WWDC21 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2021/10134/)

암튼 `await`를 사용하는 곳이 많다.

키워드는 비동기 함수가 거기에서 일시 중단될 수 있음을 나타낸다.

비동기 함수가 일시 중단된다는 것은 무엇을 의미할까?

### 비동기 함수가 일시중단된다는 것의 의미

먼저 함수를 호출할 때 어떤일이 발생하는지 알아야한다.

함수를 호출할 때 함수가 실행중인 스레드의 제어를 해당 함수에 넘긴다.

![](https://i.imgur.com/lUnTr4b.jpg)


그림에서 `fetchThumbnail`이 `thumbnailURLRequest`에게 스레드의 제어를 넘기고 

`thumbnailURLRequest` 가 일반 함수이기 때문에 스레드는 완료될 때까지 해당 작업을 수행하는데 완전히 사용된다.

해당 함수는 값을 반환하거나 오류를 발생시켜 완료되고 제어를 다시 넘겨준다.

이것이 일반 함수가 스레드 제어를 포기할 수 있는 유일한 방법이다.

### 일반 함수 호출할 때 발생하는 일

- 스레드제어를 일반 함수에 전달
- 스레드가 일반 함수가 완료될 때 까지 완전히 사용됨
- 일반 함수가 완료되면 값 or 오류와 스레드 제어를 다시 넘겨줌
- 스레드 제어를 포기할 수 있는 유일한 방법

**비동기 함수(`async` 키워드가 붙은 함수)인 경우**

- 일반 함수처럼 완료되면 제어를 다시 넘겨줌
- 그러나 다른 방식으로 스레드 제어를 포기할 수 도있는데 이게 일시중단임

![](https://i.imgur.com/DkeExVK.png)

일반 함수와 마찬가지로 `fetchThumbnail`이 `data(for: request)` (비동기 함수)를 호출하면 스레드에 대한 제어 권한이 부여된다.

비동기 함수가 실행되는 일시 중단될 수 있는데 이때 스레드의 제어를 다시 `fetchThumbnail`에 주는게 아니라 시스템에 주고 `fetchThumbnail`도 일시중단된다.

일시중단 되었다는 것은 시스템에 할 일이 많다는 것을 알고 있고 중요한것을 결정해라 라고 말해주는 것

굉장히 협조적이다.

따라서 함수가 일시중단되면 시스템은 스레드를 사용해서 다른 작업을 수행할 수 있다.

어느 시점에서는 시스템이 지금 수행해야 할 가장 중요한 작업이 일시중단된 비동기 함수라고 결정하고 그것을 재개할것이다.

그럼 다시 비동기 함수가 스레드를 제어하고 작업을 계속 수행할 수 있다.

다시 일시중단 될 수도 있다.(필요한 만큼 일시중단 가능)

일시중단할 필요가 전혀 없을 수도 있음

일시 중지하지 않았거나 함수가 종료되면 값 또는 오류와 함께 스레드 제어를 다시 넘겨준다.

### 비동기 함수 호출할 때 발생하는 일

- 스레드 제어를 비동기 함수에 전달
- 일시 중단될 상황(시스템에 할일이 많다)이면 시스템에 스레드 제어를 전달
- 시스템이 작업을 수행하다 비동기 함수가 가장 중요한 작업이라 결정하면 재개
- 비동기 함수가 스레드를 제어하면서 작업 수행 (이때 다시 일시 중단할 수 있음, 무조건 일시 중단되는건 아님)
- 작업이 끝나면 값 or 오류를 원래 함수에 스레드제어와 함께 전달

### `fetchThumbnail` 함수를 통해 알아보기

`fetchThumbnail`이 `async data` 메소드를 호출하면 스레드에서 실행을 중지한다.

스레드 제어를 시스템에 주고 시스템에 `data`메소드에 대한 작업을 예약 이때 해당 작업이 즉시 시작되지 않고 스레드는 다른 용도로 사용될 수 있다.

→ fetchThumbnail이 호출된 이후에 사용자가 다른 버튼을 탭한 상황

![](https://i.imgur.com/shdlKmC.jpg)

이때 시스템은 대기 중인 작업 전에 버튼이랑 연결된 작업을 자유롭게 실행할 수 있음 (스레드 제어권한을 넘겨 받았기 때문)

![](https://i.imgur.com/VEVQ2QQ.png)

작업이 완료되면 `data` 메소드가 제개될 수도 있고 다른 작업을 실행할 수도 있다.

마지막으로 `data` 메소드가 완료되면 `fetchThymbnail`로 돌아간다.

함수가 일시 중단된 동안 다른 작업을 수행할 수 있기 때문에 `await` 키워드로 비동기 호출을 표시해야 한다.

**함수가 일시 중단되면 앱 상태가 크게 변경될 수 있다는 점을 알아야 한다.**

함수가 일시 중지될 수 있으며, 함수 행 사이에 일시 중지 되는동안 다른 일이 발생할 수 있고 그 이상으로는 함수는 완전히 다른 스레드에서 제개될 수 있다.

`"Protect mutable state with Swift actors"` 세션을 참고

[Protect mutable state with Swift actors - WWDC21 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2021/10133/)

### `async/await`에 대해 기억해야 할 몇 가지 중요한 사항

1. 함수를 `async`로 표시하면 일시 중단하도록 허용하고 일시 중단 하면 호출자도 일시 중단된다. 따라서 호출자도 비동기식이어야 한다.
2. 비동기 함수에서 한 번 또는 여러 번 일시 중단될 수 있는 위치를 지적하기 위해 `await` 키워드가 사용된다.
3. 비동기 기능이 일시 중단되는 동안 스레드는 차단되지 않는다. 따라서 시스템은 다른 작업을 자유롭게 예약할 수 있다. (일시 중단되는 동안 앱의 상태가 크게 변경될 수 있다.)
4. 비동기 함수가 다시 시작되면 호출한 비동기 함수의 결과가 원래 함수로 다시 반환되고 중단된 곳에서 바로 실행이 계속된다
