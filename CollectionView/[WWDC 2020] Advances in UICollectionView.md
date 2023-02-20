# [WWDC 2020] Advances in UICollectionView

[Advances in UICollectionView - WWDC20 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2020/10097)

이 세션에서는 iOS 14에서 UICollectionView의 발전에 대해서 알려줌

9분짜리니까 정리할 필요도 없어보임 가서 보면뎀
![](https://velog.velcdn.com/images/xoxo0223/post/055b8fa3-3096-4ab3-be3f-474b3027e8fd/image.png)


영상 샘플 앱의 중간부분 셀들을 보면 확장 및 축소가 가능한데 이건 iOS 14의 새로운 기능이라함

![](https://velog.velcdn.com/images/xoxo0223/post/0411f44e-3d5b-402d-85b7-45b1a655bc68/image.png)


그리고 하단에 테이블뷰처럼 생긴넘이 있는데 이게 뭔지 영상에서 다 설명해줄거라함 고고

![](https://velog.velcdn.com/images/xoxo0223/post/26d880a4-394c-43a8-a517-64d4024f57f7/image.png)

UICollectionView를 구성하는 API는 Data, Layout 및 Presentation 3가지 범주로 나눌 수 있음

UICollectionView가 iOS6에서 처음 출시되었을 대 데이터는 indexPath 기반 프로토콜인 UICollectionViewDataSource를 통해 관리되었다.

레이아웃의 경우 추상 클래스인 UICollectionViewLayout이 있었고 구체적인 하위 클래스인 UICollectionViewFlowLayout이 있었다.

Presentation 측면에서 UICollectionViewCell 및 UICollectionReusableView라는 두 가지 view 타입을 개시했음

![](https://velog.velcdn.com/images/xoxo0223/post/20a150a1-02d2-4810-9b62-8bb315f35924/image.png)

iOS 13에서 Data랑 Layout이 바낌

Diffable이랑 Compositional

![](https://velog.velcdn.com/images/xoxo0223/post/d0da6ecd-051f-43ce-ba16-73de13dd83ab/image.png)


iOS 14에서 요로코롬 추가뎀

# Diffable Data Source에서 추가된 부분 먼저 고고

새로운 스냅샷 데이터 타입을 추가해서 UI 상태 관리를 크게 단순화 한다.

스냅샷은 고유 섹션 및 항목 식별자를 사용해서 전체 UI 상태를 캡슐화함

UICollectionView를 업데이트할 때 먼저 새 스냅샷을 만들고 현재 UI 상태로 채운 다음 데이터 소스에 apply 하면뎀

Diffable Data Source는 차이를 계산하고 추가 작업 없이 자동으로 애니메이션을 생성한다.

iOS 14의 경우 섹션 스냅샷이라고 하는 새로운 스냅샷 타입을 추가하고 있음!!

섹션 스냅샷은 UICollectionView의 단일 섹션에 대한 데이터를 캡슐화한다.

이걸 추가한 2가지 이유가 있다함

1. Composable data source → 자막이 엄슴
2. iOS 14 전체에서 볼 수 있는 일반적인 시각적 디자인인 개요 스타일 UI 렌더링을 지원하는 데 필요한 계층적 데이터 모델링을 허용한다?

![](https://velog.velcdn.com/images/xoxo0223/post/6805b7fa-d5aa-4c73-8992-837b31cdeec5/image.png)


가로 스크롤 섹션에서는 단일 섹션 스냡샷을 써서 여기에 있는 콘텐츠를 완전히 모델링하고 있다고 함

![](https://velog.velcdn.com/images/xoxo0223/post/af3fff56-0818-411e-b5bd-a2b49f5b916e/image.png)


확장 및 축소가 가능한 두 번째 섹션에서는 second section snapshot을 이용해서 계층적 데이터를 모델링했다고함

마지막 List 섹션에서는 세 번째 섹션 스냅샷으로 이 섹션의 콘텐츠를 다시 모델링한다함

따라서 영상에 나오는 예제의 경우 Diffable Datasource를 각각 단일 섹션의 콘텐츠를 나타내는 세 개의 개별 섹션 스냅샷으로 구성했다함

여기까지가 섹션 스냅샷에 대한 간략한 개요라고함

그리고 새로운 재정렬 API도 추가되었다고 함

자세한 내용은 Advances in Diffable Data Source를 보자~~

# Compositional Layout 고고

Compositional Layout은 iOS13에 도입 되었고, 더 작고 이해하기 쉬운 레이아웃 비트를 함께 구성하여 복잡한 레이아웃을 구축할 수 있다함

컴포지셔널 레이아웃은 레이아웃이 어떻게 작동해야 하는지 대신 원하는 레이아웃 모양을 설명하기 때문에 빠르다. (선언형??)

Compositional Layout에는 섹션별 레이아웃을 포함하여 보다 정교한 UI를 구축하는 데 도움이 되는 기능도 포함되어 있다.

직교 스크롤 섹션도 지원함

iOS 14에서는 Compositional Layout을 기반으로 List라는 새로운 기능을 구축함

List를 사용하면 UICollectionView에 바로 UITableView와 유사한 섹션을 포함할 수 있음!!

스와이프 동작 및 많은 일반적인 셀 레이아웃과 같이 UITableView에서 기다핼 수 있는 기능이 풍부하다고 함!

List 스타일의 레이아웃을 만드는 것은 매우 간단하다고 함

List는 Compositional Layout위에 구축되기 때문에 List를 섹션별로 다른 종류의 레이아웃과 쉽게 혼합하고 일치시킬 수 있음!!

![](https://velog.velcdn.com/images/xoxo0223/post/7296a575-2c65-4870-bf1e-7da0e18c9f3e/image.png)

요로코롬 2줄 써주면뎀

위 코드에서는 insetGrouped 모양의 UICollectionView 레이아웃을 만든다.

새로운 구체적인 UICollectionViewListCell, 헤더 푸터 지원 많은 iPadOS 시스템에서 볼 수 있는 새로운 사이드바 모양 등등이 있음

자세한 내용은 WWDC UICollectionView의 List를 참고하자

# UICollectionView의 Modern Cell

iOS 14의 경우 cell registrations라고 하는 새로운 API로 셀을 구성하는 완전히 새로운 방법이 있음!!

cell registration은 view model에서 셀을 설정하는 간단하고 재사용 가능한 방법임

이를 이용하면 cell identifier와 연결하기 위해 cell class 또는 nib를 등록하는 추가 단계를 제거함!!

대신에 view model에서 새로운 셀을 설정하기 위한 closure를 통합하는 일반 등록 타입을 사용함

![](https://velog.velcdn.com/images/xoxo0223/post/366bfb24-6511-4652-b180-95432d817d7d/image.png)

먼저 셀 클래스와 ViewModel 클래스에 대해 cell registration을 만든다.

등록은 새 셀이 생성될 때마다 호출되는 클로저를 사용한다함!!

등록을 사용하여 새로운 API인 dequeueConfiguredReusableCell과 함께 Diffable Data Source 셀 공급자의 셀 등록을 사용하기만 하면 된다.

위 코드에 있는 메서드들 쓰면된다 이말임

다음은 cell content configurations

이것은 UITableView 표준 셀 타입에 표시되는 것과 유사한 셀에 대한 표준화된 레이아웃을 제공함

셀 내부 콘텐츠들의 레이아웃을 애플이 만들어 줌

이러한 구성은 모든 셀 또는 일반 UIView와 함께 사용할 수 있음!!

어떻게 작동하는지 맛만 보자

![](https://velog.velcdn.com/images/xoxo0223/post/621c5412-0f24-4d42-a8fa-32c72232e6dc/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/b69bab83-e330-41f6-9d45-fae5946edd5f/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/b6bd2880-3936-4b73-a73a-174e6e229ddd/image.png)


이것도 Modern Cell Configuration 영상을 참고하자!!
