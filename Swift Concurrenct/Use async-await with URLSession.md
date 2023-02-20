# Use async/await with URLSession

[Use async/await with URLSession - WWDC21 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2021/10095/)

## **`completionHandler`를 사용하는 버전**


![](https://velog.velcdn.com/images/xoxo0223/post/2577ad2d-f1b4-4cfb-a917-a41bb01eccbb/image.png)

제어 흐름

- 데이터 작업을 생성하고 요청을 보낸다. 작업이 완료되면 `completion handler`로 이동하여 응답을 확인하고, 이미지를 생성

![](https://velog.velcdn.com/images/xoxo0223/post/f31a719e-6f07-498b-a2d0-ec39a8424659/image.png)


- 가장 바깥쪽 계층: 함수 호출자의 스레드에서 실행
- `URLSessionTask completionHandler`: `session`의 `delegate queue`에서 실행
- `completionHandler(UIImage(data: data))`: `main queue`에서 실행

<aside>
☠️ 데이터 레이스와 같은 스레드 문제가 발생할 수 있다.

</aside>

![](https://velog.velcdn.com/images/xoxo0223/post/978cc70e-76de-479b-9423-b7acf32a0d32/image.png)


completionHandler에 대한 호출이 main queue로 일관되게 전송되지 않음

조기 반환을 하지 않아서 오류가 발생했을 때 completionHandler를 두 번 호출할 수 있다.

## **`async/await`를 사용하는 버전**

![](https://velog.velcdn.com/images/xoxo0223/post/31652bf2-2c6e-4576-ab04-cd664e2ee238/image.png)



제어 흐름이 위에서 아래로 선형적임

동일한 동시성 컨텍스트에서 실행되므로 스레드 문제를 걱정할 필요가 없음


![](https://velog.velcdn.com/images/xoxo0223/post/85d034f4-5759-420d-a5f0-4187a9a41320/image.png)

async/await로 만든 data 메소드를 사용 

차단 없이 현재 실행컨텍스트를 일시 중지하고 성공적으로 완료되면 데이터와 응답을 반환하거나 오류를 발생시킨다.

오류가 발생했을 때 throw 키워드를 사용할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/98608d8e-349f-4dd2-aaf0-4bbc432605fe/image.png)


네트워크에서 데이터를 가져오는 방법

![](https://velog.velcdn.com/images/xoxo0223/post/e164d7d6-a0cd-42a6-83b7-1adbf247b5e5/image.png)


데이터를 업로드하거나 파일을 업로드하는 방법

httpMethod의 default 값은 “GET”이고 업로드를 지원하지 않으므로 “POST”로 설정해야한다.

![](https://velog.velcdn.com/images/xoxo0223/post/c13af519-16c0-4a3a-8af7-ff1bac59d582/image.png)


다운로드 방법은 응답 본문을 메모리가 아닌 파일로 저장한다.

새로운 다운로드 방법은 파일을 자동으로 삭제하지 않으므로 직접 삭제해야한다.

![](https://velog.velcdn.com/images/xoxo0223/post/fa73764d-57a4-40cf-9cf1-39bc2ef14bf9/image.png)


Swift 동시성 취소는 URLSession 비동기 메소드에서 작동한다.

Task.Handle를 사용하여 현재 실행중인 작업을 취소할 수 있다.

데이터, 업로드, 다운로드 방법은 전체 응답 본문이 도착할 때까지 기다렸다가 다시시작한다.

응답 본문을 점진적으로 받고 싶다면 URLSession.bytes 메소드를 이용


![](https://velog.velcdn.com/images/xoxo0223/post/e1920b8e-f5d9-4889-ab01-069ee5e7209f/image.png)

응답 헤더가 수신되면 반환하고 응답 본문을 AsyncSequence로 전달한다.

<aside>
❓ 엔드포인트는 서비스를 사용가능하도록 하는 서비스에서 제공하는 커뮤니케이션 채널의 한쪽 끝. 요청을 받아 응답을 제공하는 서비스를 사용할 수 있는 지점을 의미

</aside>

실시간으로 좋아요 개수를 업데이트

async sequence API를 사용하여 엔드포인트의 응답을 사용

![](https://velog.velcdn.com/images/xoxo0223/post/c3b060f0-8707-45c4-886f-4d4036364466/image.png)

OnAppearHandler 함수는 collection view가 나타날 때 호출되는 작업이다.

URLSession.bytes API를 호출하여 엔드포인트에서 데이터를 가져온다.

여기서 반환되는 bytes는 URLSession.AsyncBytes 타입이다.

![](https://velog.velcdn.com/images/xoxo0223/post/749017b2-3820-402a-bf4c-ba6ac96d8749/image.png)


AsyncBytes에 lines method를 이용해서 데이터를 수신할 때 라인별로 응답을 소비

![](https://velog.velcdn.com/images/xoxo0223/post/ffd4086f-3bef-42ba-aa05-c4309c2c215f/image.png)


updateFavoriteCount를 호출하여 UI를 업데이트

새로운 비동기 방식은 모든 메소드에 추가 인자(delegate)를 사용하여 데이터 업로드, 다운로드, 바이트 작업과 관련된 delegate메세지를 처리하는 객체를 제공한다.  

![](https://velog.velcdn.com/images/xoxo0223/post/1051eee0-029f-4fc1-8c5c-b3776f7d52a6/image.png)
