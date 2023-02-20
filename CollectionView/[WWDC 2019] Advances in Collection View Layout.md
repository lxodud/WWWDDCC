# [WWDC 2019] Advances in Collection View Layout
---

[Advances in Collection View Layout - WWDC19 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2019/215/)

![](https://velog.velcdn.com/images/xoxo0223/post/f56f99ab-b767-40bc-97c7-79c7d327dbdc/image.png)


세션에서 할꺼

시작

![](https://velog.velcdn.com/images/xoxo0223/post/9ea30ab1-d46d-49f5-a253-ab02a4d319b6/image.png)

기존에 CollectionView 레이아웃을 어떻게 정의했을 까?

레이아웃을 정의하기 위한 별도의 추상화가 있었음

![](https://velog.velcdn.com/images/xoxo0223/post/04be1759-e73e-4c7e-8626-3db826ed3f16/image.png)

CollectionViewLayout은 추상적임

사용하기 위해서는 하위 클래스를 만들어야뎀

그리고 FlowLayout이라는 구체적인 레이아웃 클래스를 제공함

이는 라인 기반 시스템을 사용함

라인 기반 시스템은 사용 가능한 공간을 채우고 다음 라인으로 넘어감 (라인은 스크롤 방향과 직교함)

![](https://velog.velcdn.com/images/xoxo0223/post/49d827b2-24c3-4c90-91e6-3f4262551739/image.png)


간단한 레이아웃 만들기는 최곤데

복잡해지면 쉽지않음

위 그림에 있는 레이아웃을 디자이너한테 받으면 고민하기 시작함 FlowLayout으로 구현이 가능한가?

이 시점에서 우리는 custom 레이아웃을 직면하게 되는 거임

![](https://velog.velcdn.com/images/xoxo0223/post/f6770779-2019-4616-8d2b-edffa2f167af/image.png)

보충, 데코 뷰를 custom 레이아웃에서 다루기 쉽지않음

self-sizing도 그럼

그래서 새로운 콘크리트 레이아웃 클래스를 도입할꺼임

![](https://velog.velcdn.com/images/xoxo0223/post/8c92648f-7598-4df5-88fe-bb23f874a271/image.png)


등장

![](https://velog.velcdn.com/images/xoxo0223/post/95f590fb-690a-4c05-ae0e-672b1e94689f/image.png)


그래서 이게 뭐냐?

1. 구성이 가능하다. → 단순한 것에서 복잡한 것을 만드는 아이디어임
2. 유연하계 설계되어 있음 → 모든 레이아웃을 작성할 수 있음
3. 기본적으로 빠르다고함

프레임워크에서 모든 성능 최적화를 수행했기 때문에 이에 대해 생각할 필요가 없다고함

Compositional Layout은 수행하려는 작업을 설명하거나 정의하는 것임

선언적 유형의 API임

![](https://velog.velcdn.com/images/xoxo0223/post/83e3337b-3c3a-47d7-990c-22eae6776724/image.png)


서브클래싱 필요없음

![](https://velog.velcdn.com/images/xoxo0223/post/5804178e-ffe5-4670-bf2d-adca9738c636/image.png)



코드로 보자

![](https://velog.velcdn.com/images/xoxo0223/post/1dd7e7f4-0be9-4146-b5d6-7eaf335e85bc/image.png)

FlowLayout을 사용하면 두 줄의 코드로 작성할 수 있음

그러나 이러한 레이아웃은 점점 더 복잡해짐에 따라 코드의 양이 선형적으로 증가하지 않고 점점 줄어들고 있다는 것임 

복잡한 레이아웃 만들 떄 좋다 이말인듯

아이템 → 그룹 → 섹션 → 레이아웃 순으로 드감

시각적으로 살펴보자~

![](https://velog.velcdn.com/images/xoxo0223/post/67142c15-dbc3-4fec-a399-4fb79a67152f/image.png)


큰 사각형이 전체 레이아웃임

그리드 스타일의 레이아웃을 나타낼 꺼임

![](https://velog.velcdn.com/images/xoxo0223/post/0be75ab1-934f-4577-acbb-9a98052564c6/image.png)


그리고 전체 섹션

![](https://velog.velcdn.com/images/xoxo0223/post/47c26e8d-d00a-4d7c-98f8-57e60dbaba8f/image.png)


행을 나타내는 그룹

![](https://velog.velcdn.com/images/xoxo0223/post/12ea8776-dbac-4f20-8ed6-85e795973f2c/image.png)


내부에 항목이 있음

기본적인 계층 구조임

![](https://velog.velcdn.com/images/xoxo0223/post/77e600f0-9522-49a3-a5c1-9d96d6aabeb7/image.png)


Compositional Layout의 핵심 타입에 대해 얘기해보자

![](https://velog.velcdn.com/images/xoxo0223/post/56c500ef-8697-4ad5-95c9-90aa1ce44af8/image.png)


크기 조정부터 시작

Compositional Layout은 크기 조정을 확장하여 내부에서 크기를 조정하는 방법을 쉽게 추론할 수 있도록 한다.

모든 것은 명시적인 크기를 가지고 있음

너비와 높이가 있는데 플로트나 더블 같은 값이 아니라 NSCollectionLayoutDimension이라는 타입임

![](https://velog.velcdn.com/images/xoxo0223/post/4adb14b9-34f8-4234-b6a2-0ec08d1478b7/image.png)

이게 뭐냐?

특정 축이 얼마나 큰지를 설명하는 축 독립적인 방법이라고 함

우리는 이것을 정의하는 방법에 대한 네 가지 다른 변형이 있음

시각적인 방법으로 보자

![](https://velog.velcdn.com/images/xoxo0223/post/59712986-6943-4fa7-bcdc-44ce8a87d656/image.png)


항목이 있고 해당 항목의 크기를 해당 컨테이너에 대해 설명하려고 한다고 가정해보자

여기서 가장 바깥쪽 컨테이너는  CollectionView가 된다.

여기서 widthDimension이 해당 컨테이너 너비의 50%가 될 것이라고 말한다.

![](https://velog.velcdn.com/images/xoxo0223/post/1f50123b-e1d5-440a-b694-9095a8e7d018/image.png)

유사하게 height가 컨테이너의 fractionalHeight라고 할 수 있다 이 경우 30%이다.

![](https://velog.velcdn.com/images/xoxo0223/post/aaa18445-d6c0-47b1-ab6b-1bc9004dcb9d/image.png)


너비와 높이를 차원으로 정의하여 특정 종횡비를 갖는 것으로 정의할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/f8607f08-5aad-4090-a813-e2ed7bd7db4c/image.png)

포인트 기반 값은 어떤가?

2가지가 존재한다 함

첫 번째는 absolute

![](https://velog.velcdn.com/images/xoxo0223/post/35d13931-2de6-4d1d-9e02-42df6d55cf93/image.png)


항목이 얼마나 커질지 정확히 모른다면 estimated로 추정할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/eac93edf-ec2d-42dd-b496-e915f227592c/image.png)


그런 다음 시간이 지나고 항목이 렌더링됨에 따라 크기가 커지고 해당 항목의 콘텐츠에 대해 조금 더 알게 된다?

여기까지가 레이아웃 dimension, size임

![](https://velog.velcdn.com/images/xoxo0223/post/93d7a042-1116-4788-b2d1-29e675630253/image.png)


다음으로 항목임

이것은 cell 또는 supplementary임

화면에 렌더링 되는 것임

이것을 구성할 때 크기를 부여해야함

![](https://velog.velcdn.com/images/xoxo0223/post/33d524db-ecbf-497d-b395-4923435fdc11/image.png)


항목 다음은 그룹임

그룹은 3가지 형태가 있음

vertical, horizontal, custom

vertical, horizontal은 flow layout 처럼 선을 따라 배치하는 거고

custom을 통하여 항목의 절대 크기와 위치를 정할 수 있다고 함

![](https://velog.velcdn.com/images/xoxo0223/post/aeedec53-f854-41de-bd88-d857ecb2804a/image.png)


다음으로 섹션

CollectionView의 섹션별 레이아웃 정의이고 해당 섹션에 얼마나 많은 항목이 있는 지에 대한 데이터 소스 개념에 직접 매핑된다함

![](https://velog.velcdn.com/images/xoxo0223/post/841da674-ff88-4cc1-981c-2b2ec88984f4/image.png)


마지막 순서는 UICollectionViewConpositionalLayout임 (macOS는 NSCollectionViewCompositionalLayout이라함)

UICollectionViewConpositionalLayout을 만드는 방법은 2가지 있음

가장 간단한 방법은 레이아웃 섹션의 정의를 지정하는 것임

여기서 확장한게 두 번째 방법

우리는 섹션이 무엇인지에 대한 훌륭한 정의를 가지고 있기 때문에 콜백할 클로저를 지정할 수 있고 섹션별로 섹션에 대한 정의를 요청할 수 있음

이제 레이아웃이 섹션 간에 완전히 구별될 수 있기 때문에 많은 가능성이 열림

![](https://velog.velcdn.com/images/xoxo0223/post/b28a985c-d7a6-41da-8135-8e23ade0daa5/image.png)


코드로 고고

![](https://velog.velcdn.com/images/xoxo0223/post/bf75981a-eb2c-436c-b04f-6afa46cc1930/image.png)


고급 주제로 고고

![](https://velog.velcdn.com/images/xoxo0223/post/9a5ef0b7-2e1a-4816-bc99-1744839b9291/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/54b41e0f-d5b4-4f66-a644-46b52d846af8/image.png)

항목 또는 그룹에 고정할 수 있다는 개념으로 이를 단순화할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/041df9d1-9d3f-4541-9825-845c97e87d2a/image.png)


새로운 타입

![](https://velog.velcdn.com/images/xoxo0223/post/5c0f6e42-e196-4552-96de-416f4d9a8a19/image.png)

요런 식으로 생성함

![](https://velog.velcdn.com/images/xoxo0223/post/2d8c7fb1-7677-4d64-8d98-2536c192583c/image.png)


fractionalOffset은 약간 바깥쪽으로 찔러볼 수 있음 → 바깥 쪽으로 튀어나오게 만든다 이말

양수 x에서 30%정도 이동한 다음 음수 Y에서도 30%위로 이동함

그런 다음 badgeSize 및 elementKint를 사용해서 CollectionLayout SupplementaryItem을 정의했슴!

![](https://velog.velcdn.com/images/xoxo0223/post/24de5a4c-b43a-4679-a0eb-dcc86b2b255e/image.png)

헤더랑 푸터는 쪼까다름

![](https://velog.velcdn.com/images/xoxo0223/post/660b0ad6-b9f2-4c71-ba90-0237e176cad1/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/e2095f59-d809-4c94-af93-c37e9bd2f349/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/3565da69-a067-4cd9-ac60-caa81dbe6a28/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/d8b0319c-f3b5-431e-904d-487b66a23bdc/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/707cf2a3-b6c7-43ae-934d-b0bcc45e9be4/image.png)


Compositional Layout의 핵심 레이아웃은 레이아웃 그룹임

레이아웃 그룹은 실제로 NSCollectionLayoutItem의 하위 타입이다.

따라서 중첩시킬 수 있다 이런 말을 함

![](https://velog.velcdn.com/images/xoxo0223/post/c615bb23-e883-40e5-b098-0ff95643f92e/image.png)


매우 간단하다함

![](https://velog.velcdn.com/images/xoxo0223/post/3bd3d973-5d28-4cf6-9d24-41c68d46e762/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/2355e98d-29fe-4983-a55d-c5ced7aeeddb/image.png)

가로스크롤, 세로 스크롤을 코드 한줄로 할 수 있다함

![](https://velog.velcdn.com/images/xoxo0223/post/3130845c-5478-4202-ba61-59f6cf702266/image.png)


예제 코드에 종류별로 어떤 스타일인지 보여줌

암튼 개꿀
