# [WWDC 2019] Advances in UI Data Source



[Advances in UI Data Sources - WWDC19 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2019/220)

![](https://velog.velcdn.com/images/xoxo0223/post/1b0fd272-e6b4-44bc-bc4d-bbde48fd5cef/image.png)


세션에서 얘기할 4가지

UITableView 및 CollectionView에서 UI 데이터소스와 어떻게 상호작용 하는가?

![](https://velog.velcdn.com/images/xoxo0223/post/1a6345d7-55f0-4a55-8181-fe9539fb24c4/image.png)


UICollectionView 데이터 소스의 구현 예시

섹션의 수, 각 섹션의 항목 수 그리고 콘텐츠가 렌더링되면 셀을 요청함

이걸 10년동안 사용했다함

![](https://velog.velcdn.com/images/xoxo0223/post/c1f2bc14-aad6-4c49-9e61-fcd6e2923153/image.png)

섹션의 항목 수만 제공하려는 경우 기존의 데이터 소스는 간단함

데이터 소스를 지원하기 위해 특정 종류의 데이터 구조를 사용할 필요가 없기 때문에 매우 유연하다고 함

![](https://velog.velcdn.com/images/xoxo0223/post/d89a1fcf-b70e-43f9-9508-0ef334cff5bc/image.png)


근데 실제 앱은 더 복잡함

그리고 이러한 데이터 소스는 앱 내부의 복잡한 컨트롤러에 의해 지원됨

그리고 이 컨트롤러는 Core Data와 상호작용할 수 있고 웹 서비스와 대화하는 등 다양한 일을 할 수 있음

![](https://velog.velcdn.com/images/xoxo0223/post/99a7b770-5ca1-4a76-a6b6-1e2b79188bbc/image.png)


UI 레이어와 컨트롤러 레이어 간의 대화를 보자

![](https://velog.velcdn.com/images/xoxo0223/post/8e23c990-1a83-469e-9c3a-06bd55f2ad40/image.png)

UI가 컨트롤러한테 콘텐츠를 렌더링할 때 셀을 제공하라고 함

![](https://velog.velcdn.com/images/xoxo0223/post/c9fea6b7-38d8-408e-88d5-196a6fdfa192/image.png)


더 복잡한 상황을 보자, 컨트롤러가 응답을 받는 웹서비스 요청을 가지고 있고 뭔가가 변경되서 UI한테 변경됬다고 말함

이제 이것을 UI 레이어에 대한 업데이트로 변경할 수 있음

여기에는 TableView 및 CollectionView에 대해 발생해야 하는 모든 변형이 포함됨

일괄 업데이트를 올바르게 구성하고 백업 저장소를 변경하는 방법 등 모든 것들이 있음

![](https://velog.velcdn.com/images/xoxo0223/post/0f1acda0-daef-4dd3-a17b-7fdbfcd4f9a4/image.png)


제대러 해도 위 같은 에러가 발생할 수 있음

삭제나 삽입 같은 동작을 수행할 때 발생하는 에러

reloadData를 호출해도 뎀 나쁘지 않다고함!

근데 reloadData를 호출하면 애니메이션 효과가 발생하지 않음

그리고 이게 사용자 경험을 손상시킴

![](https://velog.velcdn.com/images/xoxo0223/post/57bce4f8-9f81-431b-ad1b-7d563d433c2d/image.png)

여기서 가장 큰 문제는 데이터소스 역할을 하는 데이터 컨트롤러가 시간이 지남에 따라 변경되는 자체 버전의 truth를 가지고 있다는 것임 그리고 UI도 자기 버전의 truth를 가지고 있음 

둘의 truth가 달라질때가 문제임

따라서 UILayerCode는 이를 항상 동기화되도록 해야함 → 이게 어렵다함

따라서 현재 접근 방식은 오류가 발생하기 쉬움

중앙화된 truth가 없기 때문!!

![](https://velog.velcdn.com/images/xoxo0223/post/86d9d77b-c881-4d5e-8cc1-8617a06e91b1/image.png)


애플의 새로운 접근 방식

등장 Diffable Data Source

![](https://velog.velcdn.com/images/xoxo0223/post/6af22f2f-4ace-4d12-bbab-7c40768a8beb/image.png)


Diffable Data Source는 performBatchUpdates()가 없음

이와 함께 모든 충돌, 번거로움, 복잡성, 처리하고 싶지 않은 모든 것들이 제거되었다함

대신에 apply라는 단일 메서드가 있음

apply는 간단하고 자동이고 번거롭지 않은 diffing이다?

![](https://velog.velcdn.com/images/xoxo0223/post/1a70f5a5-0ada-481c-ab9f-be67ee139b18/image.png)


스냅샷이라고 부르는 완전히 새로운 구조로 이 작업을 한다.

스냅샷은 현재 UI 상태의 truth임 

그리고 스냅샷은 유니크한 section 또는 item의 식별자의 연결 또는 컬렉션임

그리고 indexPath가 아니라 identifier를 사용해서 업데이트함

![](https://velog.velcdn.com/images/xoxo0223/post/ae2d1b06-4833-40be-bba7-a8f1ba250356/image.png)


화면에 FOO, BAR, BIF가 표시되어 있음

컨트롤러가 바꼈다고 가정해보자

이제 적용하려는 새로운 스냅샷이 생겼음

![](https://velog.velcdn.com/images/xoxo0223/post/bdf3dcf0-6098-498a-b214-cdc59d0687c4/image.png)


BAR, FOO, BAZ를 사용해서 완전히 새로운 스냅샷을 구성한 것을 볼 수 있다.

그리고 방금 순서가 변경된 일부 항목과 새 항목이 들어온다.(BAR 랑 FOO가 바꼇고 BIF가 없어지고 BAZ가 생김)

개념 적으로 apply는 현재 상태에 대해 알고있고 UI 요소에 적용할 새 상태에 대해 알고 있음

![](https://velog.velcdn.com/images/xoxo0223/post/a8ad1e83-d5d4-408f-a0d7-333683c40453/image.png)


슈웃

다음 단계없이 바로 변경 끝~

![](https://velog.velcdn.com/images/xoxo0223/post/63f96f0a-cf7e-4a76-b2ee-7bf1e44e6df4/image.png)


모든 플랫폼에 걸쳐 4개의 클래스를 가지고 있음

iOS랑 TVoS는 위 2개 MAC은 밑에 1개

그리고 모든 플랫폼에서 NSDiffableDataSourceSnapshot이 담당함

이제 코드를 보자

![](https://velog.velcdn.com/images/xoxo0223/post/c1369467-4f40-4f7e-ae44-14fecd57e1f3/image.png)


다양한 예를 살펴보면서 반복되는 패턴을 볼 거임

간단한 3단계 프로세스임

새로운 변경 또는 데이터 세트를 전체 데이터 소스가 있는 CollectionView 또는 UITableView에 넣고싶을 때마다 스냅샷을 생성하기만 하면 됌

해당 업데이트 주기에 표시할 항목에 대한 descriptrion으로 해당 스냅샷을 채운다 그리고 스냅샷을 적용해서 변경 사항을 UI에 자동으로 커밋함

DiffableDataSource는 모든 비교를 처리하고 UI요소에 대한 변경 사항을 발행함

영상참고 데모 3개함

![](https://velog.velcdn.com/images/xoxo0223/post/34b64715-3e00-48d5-95e9-9b17730b6b5d/image.png)


이 API를 최대한 활용하는 방법에 대해서 좀 더 자세히 보자

첫째, 기본적으로 3단계만 있음

스냅샷을 만들고 필요에 따라 구성하고 적용하면된다.

![](https://velog.velcdn.com/images/xoxo0223/post/b24ed7a8-00ec-4adc-aacf-beedc69e7538/image.png)


apply만 호출하면 뎀

밑에 두개는 쓸 필요가 없음

![](https://velog.velcdn.com/images/xoxo0223/post/7f559e89-08ff-47fa-9ae7-9cd97204932f/image.png)


스냅샷을 만드는 방법에는 두 가지가 있음

가장 일반적인 방법은 빈 스냅샷을 만드는 것임

섹션, 항목에 대한 타입으로 스냅샷을 구성함

그리고 현재 상태에서 하나를 만들 수도 있음

이것은 아주 작은 것 하나를 수정해야 하는 특정 작업이 발생할 때 유용함

이것을 사용하면 사본을 하나 받기 때문에 마음대로 변경할 수 있고 데이터 소스에 영향을 미치지 않음

![](https://velog.velcdn.com/images/xoxo0223/post/30c49b5f-6b37-4348-a7c4-10d9957e8f50/image.png)


몇 개의 항목이 있는지, 몇 개의 섹션이 있는지, 섹션, 아이템의 identifier를 get하는 API가 있음

![](https://velog.velcdn.com/images/xoxo0223/post/340d749c-a492-4b6e-9648-b45c4abe76b2/image.png)


이제 indexPath는 사용하지 않음

삽입, 이동 및 삭제와 같은 작업도 수행할 수 있음

![](https://velog.velcdn.com/images/xoxo0223/post/e9d6ff09-de63-40aa-b369-1bdfe84d2761/image.png)


identifier에 대해서 얘기해보자

이들은 유니크해야함

대부분의 앱에는 모델 객체에 일종의 아이디 개념이 있기 때문에 큰 문제는 아님

Swift에서 이것은 hashable을 준수해야함

Swift의 많은 것들이 이 작업을 자동으로 수행함

enum타입은 자동으로 준수하고, 문자열, 정수 UUID 등 이 있음

![](https://velog.velcdn.com/images/xoxo0223/post/d74d0e1e-c0fe-49d0-b839-344a95d6fd46/image.png)


위 코드 처럼 Hashable 항목을 생성할 수 있음

![](https://velog.velcdn.com/images/xoxo0223/post/b563d592-1fde-4228-b577-85710b72ebe9/image.png)

CollectionView, TableView에는 수많은 IndexPath 기반 API가 있음 

특히 Delegate 메서드에 많이 있음

IndexPath를 이용해서 identifier로 변환할 수 있는 API가 있음 

초고속이라고 함!!

![](https://velog.velcdn.com/images/xoxo0223/post/1349d8f3-0836-44ef-beb0-ec4d84c3e3d9/image.png)

성능 차례

diff는 O(N) 선형 연산임

즉, Item이 많을 수록 오래걸림

선형 diff에 많은 수의 항목이 있는 경우 백그라운드 큐에서 apply 메서드를 호출하는 것이 안전하다? 좋다?

메인큐에서 오래 걸리면 안좋긴 함 사용자의 터치 이벤트가 느리게 반응gks다거나,,,

암튼 apply 메서드를 백그라운드 큐에서 호출하는 것은 괜찮다고 함

![](https://velog.velcdn.com/images/xoxo0223/post/c1ec73d1-5ea8-484b-8ec7-56e5b9ebc055/image.png)


하나만 경고한다고 함 

만약에 백그라운드 큐에서 apply를 호출하기로 결정했으면 일관적으로 apply를 백그라운드 큐에서 호출하라고 함

섞어서 호출하는건 좋지 않다!!!

하나의 방식으로만 고고
