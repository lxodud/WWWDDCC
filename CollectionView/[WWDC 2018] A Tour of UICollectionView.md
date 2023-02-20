# [WWDC 2018] A Tour of UICollectionView

[A Tour of UICollectionView - WWDC18 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2018/225)

![](https://velog.velcdn.com/images/xoxo0223/post/f0aef5cc-5da0-4074-90db-9678c7a3c648/image.png)


세션에서 할꺼

앱을 빌드할꺼고 

이때 UICollectionView의 많은 기능을 활용할 거임

그리고 앱을 빌드하는 동안 레이아웃, 업데이트 및 애니메이션을 포함하여 UICollectionView와 관련된 다양한 주제를 다룰 것임

![](https://velog.velcdn.com/images/xoxo0223/post/8fc592ca-665a-468b-bb6a-f0d68c246851/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/1fd8ea37-a261-4123-aeef-73810f7a43ac/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/14b5cbe0-2e33-4941-8520-74c9608b469d/image.png)

코드를 작성하기 전에 CollectionView에 대해 이해해야 하는 세 가지 핵심 개념을 다룬다.

레이아웃, 데이터 소스, 델리게이트

![](https://velog.velcdn.com/images/xoxo0223/post/f65628da-8972-4382-b67d-97dd3423797c/image.png)


먼저 레이아웃

UITableView와 UICollectionView는 매우 비슷한 API들을 가지고 있지만 Layout 개념이 큰 차이점임

이를 통해서 CollectionView는 콘텐츠 자체와 별도로 콘텐츠의 시각적 배열을 추상화할 수 있다.

레이아웃은 콘텐츠가 표시되는 위치에 관한 것이다.

이제 각각 개별 항목은 UICollectionVIew 레이아웃 속성에 의해 바운드, 중앙 및 프레임과 같은 속성에 대해 지정된다.

UICollectionView의 하위 클래스를 만들어서 디자인에 포함할 수도 있다.

사용자가 화면의 콘텐츠를 스크롤할 때 레이아웃은 변경할 수 없는 것으로 간주된다.

이를 변경해야 하는 경우 무효화 메커니즘을 사용하면 되는데 나중에 설명할 꺼임

레이아웃이 별도의 추상화되는 것에 대한 한 가지 좋은 점은 레이아웃 간에 이동할 때 한 레이아웃에서 다른 레이아웃으로 전환할 수 있고 훌륭한 애니메이션 효과를 얻을 수 있으며 레이아웃 A는 레이아웃 B에 대해 아무것도 알 필요가 없다는 것임

레이아웃이 어떻게 될 것인지 선언하고 전환이 발생함

CollectionViewLayout은 추상 클래스이기 때문에 직접 사용하는 용도가 아님 하위 클래스를 만들어서 사용해야 하거나 애플이 제공하는 클래스를 사용해야함

그게 바로 UICollectionViewFlowLayout임

![](https://velog.velcdn.com/images/xoxo0223/post/0f0362eb-17b5-47d3-b7ab-e152d7190d8f/image.png)


Flow가 뭔가?

라인 기반 레이아웃 시스템이기 때문에 받을 수 있는 다양한 디자인을 광범위하게 다룰 수 있음

그럼 라인 베이스가 뭔가?

![](https://velog.velcdn.com/images/xoxo0223/post/4187dea1-4624-4b04-a96b-8e045d26baa2/image.png)


라인 기반 시스템을 설명하기 가장 좋은 방법은 예를 들어서 설명하는 거임

위 그림에서 보면 세로로 스크롤되는 컬렉션 뷰가 있고 이 콘텐츠를 배치할 때 flow layout이 수행하는 작업을 모방할 꺼임

시작~

![](https://velog.velcdn.com/images/xoxo0223/post/d2ed8c05-751e-405a-9755-83c00948d1f5/image.png)


첫 번째 아이템이 있음 위쪽 가장자리에서 시작해서 선을 따라 항목을 배치하기 시작함

![](https://velog.velcdn.com/images/xoxo0223/post/4d163094-6ef3-49f5-b731-e7b6e3dc13f7/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/6579801b-a400-4d39-9729-909da84b3d56/image.png)

요로코롬

선은 스크롤 축과 직교함

수직으로 스크롤하고 있기 때문에 라인은 수평임

해당 줄의 공간을 채웠기 때문에 이제 다음 줄로 이동하여 콘텐츠 배치를 계속 함

![](https://velog.velcdn.com/images/xoxo0223/post/bdd74e89-75a8-440a-b90c-a5f9a172badf/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/7956e9af-cd71-4aff-951d-9df2c14c2a70/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/b241e2fc-1744-437e-82a0-bb2ead60f404/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/4af5a4c0-22c7-4d0e-ae5f-ce7fb9e16c3e/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/2c1484c9-b328-451f-99a9-e8b18867523a/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/5266859f-37f7-4c73-8759-dfade8b338c5/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/d1496282-0aeb-4aff-9b40-d10651852971/image.png)

flowLayout을 커스텀하는 몇 가지 정의에 대해 이야기해보자.

![](https://velog.velcdn.com/images/xoxo0223/post/1b204c09-6eb2-43c1-b37f-cfae5dfbb3c5/image.png)

첫 번째는 줄 간격의 개념임

위 그림처럼 줄 간격은 수평산 사이의 간격이 된다.

![](https://velog.velcdn.com/images/xoxo0223/post/8f3731f4-88a3-492a-a47c-151cf08c2a9d/image.png)


항목 간 간격은 레이아웃 선을 따라 항목 사이의 간격임

FlowLayout에는 두 속성 모두에 대한 최소값을 지정할 수 있는 두 가지 프로퍼티가 있음

이번엔 가로로 스크롤되는 놈을 보자

![](https://velog.velcdn.com/images/xoxo0223/post/7f811f3a-f94a-40bb-bfbc-c76584c358fb/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/f433c103-0928-4d46-9b39-9d88b5691987/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/06a44738-0d22-493d-9f73-a5d76a69051c/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/108b746f-d72c-499b-9c0b-5d04631baf46/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/c63fdf34-b201-4184-9beb-2c1c13c4c3f0/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/23554f72-b85b-4f45-85f4-fe61dde0c103/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/1c713686-8025-4052-9834-4f58b6f00e55/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/6b206229-dce8-41a1-96be-cf5a72ce01f3/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/c629d178-f0d1-4fcc-aed9-5a37e1f55d00/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/80485f75-a241-4b73-9ab4-130d7e092207/image.png)


요로코롬 아이템을 배치할꺼임

선은 뭐다? 스크롤과 직교하는 방향

선 방향으로 항목들을 채워나감 ㅇㅇㅇ

![](https://velog.velcdn.com/images/xoxo0223/post/7afd688d-8546-434c-a053-df627f5bee72/image.png)

이제 수직 레이아웃 라인이 생김

![](https://velog.velcdn.com/images/xoxo0223/post/a545fa97-d479-4757-9103-783675cab875/image.png)

이 방향에서 줄 간격

![](https://velog.velcdn.com/images/xoxo0223/post/250d9c5d-07fe-4528-9cb6-121d97e15e96/image.png)

아이템 간격임

flow layoudt으로 작업할 때 기억하는 것이 중요하다.

![](https://velog.velcdn.com/images/xoxo0223/post/229187db-5867-449a-ae6f-6462989d849f/image.png)

이제 데이터 소스 고고

레이아웃이 콘텐츠가 있는 위치에 관한 거라면 데이터 소스는 콘텐츠 그 자체임

위 그림에서 첫 번째 메서드는 collectionView의 섹션의 수 (선택적임) 제공하지 않으면 1개를 의미함

두 번째는 섹션에 있는 항목의 수를 줌, 각각의 개별 섹션에 있는 항목의 수를 알려주는 친구

마지막은 사용자에게 표시할 실제 콘텐츠를 제공하는 메서드임



![](https://velog.velcdn.com/images/xoxo0223/post/e3d50ebd-be81-4c29-b764-81bad89d136b/image.png)


마지막으로 델리게이트임

델리게이트의 사용은 선택사항임

collectionView는 UIScrollView의 하위 클래스임

따라서 ScrollView 수퍼 클래스에서 제공하는 것과 동일한 Delegate를 사용하지만 이를 확장함

스크롤 동작을 수정해야 하는 경우 동일한 Delegate에 대해서도 수행할 수 있고 사용자가 콘텐츠와 상호작용할 때 강조 표시 및 선택에 대한 세밀한 제어와 같은 기능을 제공하는 UICollectionViewDelegate 메서드를 사용할 수도 있음

![](https://velog.velcdn.com/images/xoxo0223/post/8601f9da-e008-4228-9db3-f7e20a290b10/image.png)



영상 참고~~

UIVollectionViewLayoutsPrepare 메서드는 레이아웃이 무효화될 때마다 호출되고, UICollectionFlowLayout의 경우 CollectionView의 크기 범위가 변경될 대마다 레이아웃이 무효화 된다.

앱이 휴대폰에서 회전하거나 iPad에서 앱 크기가 조정되는 경우 

![](https://velog.velcdn.com/images/xoxo0223/post/a1097261-8152-4e98-8d3c-e56107a36407/image.png)


FlowLayout 개꿀

![](https://velog.velcdn.com/images/xoxo0223/post/c02955f9-a9a9-4073-9e62-489bcc008c6b/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/ea0d26c7-9ae2-4bf6-8569-5cba6a4d92a4/image.png)



위 그림과 같은 형태의 레이아웃이 필요할 때 FlowLayout의 사용이 적절한가?

![](https://velog.velcdn.com/images/xoxo0223/post/0358908e-d8ff-46ac-aee8-ec9dd4161cd6/image.png)


FlowLayout은 라인 기반이기 때문에 큰 왼쪽 한목을 배치하고 공간이 있는 곳에 다음 항목을 배치하고 없으면 다음 라인으로 건너뛸 것임

근데 우리는 수직 스택으로 그림 두 개를 쌓아야 함

이것은 실제로 FlowLayout에서 작동하지 않을 것임

왜냐하면 라인 기반 레이아웃 이기 때문에

![](https://velog.velcdn.com/images/xoxo0223/post/19d455f4-eb81-4c84-82e6-93c33411c85b/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/2e2b6a15-49b4-40d1-85cc-4929c0362de9/image.png)



이 경우에는 고유한 커스텀 레이아웃을 만들 꺼임

여기서 다루어야할 네 가지 기본 메서드가 있고 추가 메서드를 하나 더 소개한다고 함

4가지 메서드 레고레고

![](https://velog.velcdn.com/images/xoxo0223/post/ffec4588-bd5d-47c5-8754-cbf674cb58cc/image.png)


첫 번째는 CollectionView의 콘텐츠 크기이다.

UIScrollView의 기능 중 하나는 넓은 콘텐츠 영역을 볼 수 있는 가시 영역이 있고 그 안에서 콘텐츠를 이동하는 훌륭한 iOS 경험을 얻을 수 있다는 거라함

CollectionView는 ScrollView에 자신의 콘텐츠 크기를 알려야함

![](https://velog.velcdn.com/images/xoxo0223/post/6c0ca8e8-4e5d-4f69-852d-cb4d1be171db/image.png)



다음은 레이아웃 속성을 제공하는 2가지 메서드임

첫 번째 메서드는 사용자가 콘텐츠를 스크롤하거나 처음으로 표시할 때 화면에 표시하는 데 필요한 것을 알아야할 때 CollectionView에 의해 주기적으로 호출함

이 쿼리는 기하학적 영역에 의한 거라함 뭔 소리여

두 번째 메서드는 싱글 아이템? 해당 셀이 있는 지정된 인덱스 경로의 항목에 대한 레이아웃 정보를 검색한다.

이 두 API의 경우 성능이 중요하다는 점에 주목하는 것이 좋다.

![](https://velog.velcdn.com/images/xoxo0223/post/a9e6e54c-7a52-40ea-8823-d1cb346a28db/image.png)



4번째는 prepare() 메서드임

레이아웃이 무효화될 때마다 호출뎀

캐싱하려는 레이아웃 속성과 나중에 곧 요청하게 될 콘텐츠 크기와 같은 모든 것을 계산하기에 좋은 시점임

![](https://velog.velcdn.com/images/xoxo0223/post/52385212-6371-4d65-9b41-83308fc6c5ad/image.png)


마지막 추가 메서드

바운즈 변경에 대한 레이아웃을 무효화함

이 메서드는 CollectionView의 바운즈가 변경될 때마다 호출된다.

CollectionView는 UIScrollView의 하위 클래스이고 스크롤할 때마다 바운즈가 바뀌겠지?

앱의 크기가 변경되거나 COllectionView 크기가 변경되는 경우도 있겠다

따라서 이것은 스크롤하는 동안 호출될 꺼임

매우 자주 호출뎀

따라서 여기서 올바른 결정을 내리는 것이 중요하다.

기본 구현은 false를 리턴함

예를 들어, UICollectionViewLayout은 origin이 변경되면 false를 리턴함 따라서 사용자가 콘텐츠를 스크롤하기만 한다고 레이아웃이 무효화되지는 않음

그러나 기기가 회전하고 앱이 다른 크기로 변경되면 true를 반환함

![](https://velog.velcdn.com/images/xoxo0223/post/433e475e-a975-4588-95b8-290b4032b9c8/image.png)


영상에서 어캐 커스텀하는지 알려줌 고고


![](https://velog.velcdn.com/images/xoxo0223/post/cfd63fde-45d7-406b-898a-e9e2e9ae4666/image.png)


새로 고침, 이동, 삭제 작업을 수행할꺼임

Perform Batch Update API를 이용함

이 API를 통해 기본적으로 애니메이션과 동시에 수행할 수 있는 일련의 업데이트를 컬렉션 뷰에 전달할 수 있음

![](https://velog.velcdn.com/images/xoxo0223/post/22e3724c-9993-46e7-ad49-3b0fa3d8a3d7/image.png)


에러 분석

동일한 인덱스패스(0~3)에서 삭제 및 이동을 시도하고 있음을 나타냄



![](https://velog.velcdn.com/images/xoxo0223/post/7588f51a-447d-4384-bad1-ef273145e6b9/image.png)

이 API의 목적은 동시에 여러 업데이트를 커밋하고 애니메이션화 하도록 하는 것임

update 클로저 내에서 CollectionView 업데이트와 함께 데이터소스 업데이트를 수행하는 것이 매우 중요하다!!

![](https://velog.velcdn.com/images/xoxo0223/post/a2607b4c-89c6-4571-83bf-1b7f37ceae4e/image.png)


3개의 요소가 있는 두 개의 배열이 있음

첫 번째 삭제를 먼저 실행하고 두 번째 삭제를 이후에 수행할것임



![](https://velog.velcdn.com/images/xoxo0223/post/7cfd9d99-1766-4618-bb59-a666a5828437/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/0d652ecd-ce77-4222-8d77-a169c8009777/image.png)


첫 번째 항목을 지웠고 이제 인덱스 1에 삽입할 꺼임

![](https://velog.velcdn.com/images/xoxo0223/post/de9a4748-2046-4228-bf0f-a5c2807c5ebf/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/0818669e-df23-47db-ac3d-7fcbcbc6d9fb/image.png)

두 번째는 반대로 삽입을 먼저 수행하고 삭제를 할꺼임

둘은 다름!! 순서가 중요하다~

이를 CollectionView의 update에 비교해보자



![](https://velog.velcdn.com/images/xoxo0223/post/630b6a4b-8a16-4664-97c6-cbd227ce9ded/image.png)

dataSource의 업데이트는 생략했다함

update1, update2 둘 다 삽입과 삭제가 있는데 위의 예제처럼 순서가 다름!

이것은 우리에게 똑같은 결과를 준다고함 

왜와이??????

이유를 아라보자



![](https://velog.velcdn.com/images/xoxo0223/post/824a0183-f6b7-46c6-9be2-e486fdc939c0/image.png)

CollectionView로 전송되는 업데이트의 순서가 중요하지 않은 이유는 데이터 소스에 대한 것임

작업별로 살펴보자

첫 번째 삭제는 내림차순 indexPath에서 처리된다함

삭제를 위해 indexPath는 항상 이전 단계를 참조한다.

두 번째 insert는 오름차순 indexPath에서 삽입이 처리된다함

삽입에서 참조하는 indexPath는 항상 최종 상태 또는 업데이트 후 단계를 참조한다함

세 번째 move는 이 두가지를 혼합한 것이라함

from과 to indexPaht가 있고 from은 이전 상태, to는 이후 상태임

마지막 reload는 슈퍼 명령임

실제로는 삭제와 삽입으로 분해된다

그리고 다시 로드에 지정된 indexPath는 이전 상태에 대해 말하고 있음


![](https://velog.velcdn.com/images/xoxo0223/post/67629111-3b52-450f-abb8-6176f914b156/image.png)

요런 경우에 exception이 발생한다 함



![](https://velog.velcdn.com/images/xoxo0223/post/c4a67fc0-fb5c-43b5-a83f-bfae7acd3a64/image.png)

이 모든 것들을 어떻게 단순화하고 CollectionView 또는 TableView 업데이트 세트에서 항상 데이터 소스 업데이트를 적용하고 모든 것이 동기화되도록 할 수 있는 방식으로 추출할 수 있을까?

먼저 이동을 분해하고 삭제 및 삽입을 원한다.

그런 다음 모든 삭제 및 삽입을 두 개의 개별 목록으로 결합하고 먼저 삭제를 인덱스 경로에서 내림차순으로 처리한 다음 마지막으로 해당 삽입을 오름차순 인덱스 경로 순서로 적용한다. 이렇게 하면 잘된다함 ㅋㅋ



![](https://velog.velcdn.com/images/xoxo0223/post/e6b0dc3a-aebb-4ce7-b1f6-8f9b4069dedd/image.png)


reloadData()는?

애니메이션이 없음;;

[A Tour of UICollectionView - WWDC18 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2018/225/)

[[iOS - swift] 3. CollectionView (컬렉션 뷰) - custom layout (grid, pinterest 레이아웃 구현)](https://ios-development.tistory.com/629)
