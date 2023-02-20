# [WWDC 2020] Advances in Diffable Data Sources

[Advances in diffable data sources - WWDC20 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2020/10045/)

iOS 14에서 Diffable Data Source의 발전에 대해서 얘기할꺼임

![](https://velog.velcdn.com/images/xoxo0223/post/1be5eaa0-d558-47a2-965f-1fedc23ee320/image.png)


첫 번째 섹션은 가로로 스크롤되는 이모티콘 그리드가 있음!

![](https://velog.velcdn.com/images/xoxo0223/post/fdcaf4b7-1da4-43a1-955e-f22bb2d05995/image.png)


중간에 있는 섹션은 iOS 14의 새로운 기능인 확장 및 축소가 가능한 스타일의 UI임

![](https://velog.velcdn.com/images/xoxo0223/post/35825aba-0c79-41a1-90e3-d313dac3d4cc/image.png)


마지막 섹션은 UITableView처럼 보이는 UI임

![](https://velog.velcdn.com/images/xoxo0223/post/b7a8f693-0fab-4840-8ec6-d9e36152e231/image.png)


iOS 13에서 도입된 Diffable Data Source는 새로운 스냅샷이라는 데이터 타입을 추가해서 UI 상태 관리를 크게 단순화했음

스냅샷은 고유 섹션 및 항목 식별자를 사용해서 전체 UI 상태를 캡슐화함

따라서 UICollectionView를 업데이트할 때 먼저 새로운 스냅샷을 만들고 현재 UI상태로 채우고 데이터소스에 apply하면 된다~

Diffable Data Source는 개발자의 추가 작업 없이 데이터의 차이를 계산하고 자동으로 애니메이션을 적용한다.

iOS 14에서는 Diffable Data Source를 기반으로 섹션 스냅샷 및 재정렬 두 가지 새로운 기능을 구축했음

![](https://velog.velcdn.com/images/xoxo0223/post/7ca8de33-fc44-4bf9-8505-7bf8d1ea2648/image.png)


섹션 스냅샷 부터 시작

![](https://velog.velcdn.com/images/xoxo0223/post/6a83eeab-0810-4222-86f2-952f453d23f0/image.png)


iOS 14의 경우 섹션 스냅샷이라고 하는 기존 스냅샷 타입 옆에 새로운 섹션 스냅샷을 추가한다.

섹션 스냅샷은 UICollectionView의 단일 섹션에 대한 데이터를 캡슐화한다!!

이러한 개선에는 2가지 이유가 있다고함

1. 데이터 소스를 섹션 크기 청그로 더 잘 구성할 수 있도록 함
2. outline-style UI 렌더링을 지원하는 데 필요한 계층적 데이터의 모델링을 허용하기 위해 iOS 14 전체에서 볼 수 있는 공통 시각적 디자인임

섹션 스냅샷을 이용해서 샘플 앱에서 이를 구축하는 방법을 살펴보자

![](https://velog.velcdn.com/images/xoxo0223/post/da134552-d969-4353-b27d-4745ec5b2f0e/image.png)


먼저 가로 스크롤 섹션에서 단일 섹션 스냅샷을 사용하여 해당 부분의 콘텐츠를 완전히 모델링함

![](https://velog.velcdn.com/images/xoxo0223/post/314eb085-b5be-4796-9f53-0c3557447429/image.png)


다음으로 확장 및 축소가 가능한 outline-style 섹션이 있는 두 번째 섹션에서 계층적 데이터를 모델링하는 두 번째 섹션 스냅샷이 사용됌

![](https://velog.velcdn.com/images/xoxo0223/post/8f70e828-c452-4a18-bf7c-91fb426eda2a/image.png)


마지막 리스트 섹션에서 세 번째 섹션 스냅샷으로 해당 섹션의 콘텐츠를 다시 모델링함

영상에서 나오는 샘플 앱의 경우 각각 단일 섹션의 콘텐츠를 나타내는 세 개의 개별 섹션 스냅샷에서 Diffable Data Source를 구성함

API를 살펴보자

![](https://velog.velcdn.com/images/xoxo0223/post/d9874665-e402-4192-8d0b-45c809f95305/image.png)

전체 API는 SDK를 확인해라

새로운 섹션 스냅샷 타입을 살펴보면 아이템 타입에 대해 제네릭함

섹션 식별자 타입은 없다

섹션 스냅샷은 본질적으로 자신이 나타내는 섹션을 알지 못함

섹션 스냅샷에 콘텐츠를 추가하기 위해 append API를 사용함

파라미터를 보면 계층적 데이터를 모델링하는 데 필요한 섹션 스냅샷에서 부모-자식 관계를 만들 수 있음

![](https://velog.velcdn.com/images/xoxo0223/post/a236296c-ad60-446a-9324-14bb65833ba7/image.png)


새로운 섹션 스탭샷 타입을 수용하기 위해 두 개의 새로운 API를 UICollectionViewDiffableDataSource에 추가했음

먼저 섹션 스냅샷과 섹션 식별자를 가져오는 새로운 apply 메서드가 있음

snapshot 메서드는 특정 섹션의 콘텐츠를 나타내는 섹션 스냅샷을 검색할 수 있음

스냅샷과 섹션 스냅샷을 함께 사용해서 Collection View의 콘텐츠를 구성하는 방법을 보자

![](https://velog.velcdn.com/images/xoxo0223/post/5f6f6a9c-c534-4c82-aab5-aa58ef0cfeaf/image.png)


먼저 Diffable Data Source에 스냅샷을 적용하여 원하는 순서대로 섹션을 추가함

원하는 섹션 순서를 정했으면 섹션 스냅샷을 각 섹션에 직접 적용해서 각 섹션에 대한 항목을 채울꺼임

![](https://velog.velcdn.com/images/xoxo0223/post/a77f804d-2dd1-4d29-8f4b-f40ee76a2aa7/image.png)


섹션 스냅샷 API를 사용해서 계층적 데이터를 만드는 방법을 살펴보자

![](https://velog.velcdn.com/images/xoxo0223/post/32f2cb3f-13c3-454f-8132-e19f5b6616cc/image.png)


먼저 루트 항목을 섹션에 추가한다.

append 메서드에는 optional parameter인 parent 가 있음

이것이 제공되지 않으면 해당 항목을 루트에 적용한다는 의미임

그리고 아래에서 부모-자식 관계를 구성하기 위해 일부 자식 항목을 부모 항목에 추가함

끝 이제 계층적 데이터를 모델링하는 섹션 스냅샷을 만들었음!!

![](https://velog.velcdn.com/images/xoxo0223/post/0208b4df-9b72-4e36-8f91-4896abc20a28/image.png)

위 코드는 특정 상위 항목과 관련된 모든 하위 항목을 가져오고 선택적으로 상위 항목을 포함시킬 수 있음

![](https://velog.velcdn.com/images/xoxo0223/post/8ff7afef-5cb9-4b86-b1c3-dd8e75dc2b17/image.png)


확장 상태는 섹션 스냅샷 상태의 일부로 관리된다.

또한 표시할 스냅샷을 만들 때 해당 항목의 상위 확장 상태를 설정해서 하위 콘텐츠가 처음에 표시되는지 여부를 쉽게 결장할 수 있다.

항목이 확장되었는지 축소되었는지 확인하기 위해 스냅샷을 쿼리할 수도 있다.

섹션 스냅샷의 확장 상태를 변경하면 Diffable Data Source에 실제로 적용할 때까지 적용되지 않음

![](https://velog.velcdn.com/images/xoxo0223/post/bb4c8aa5-91d7-4a3f-a14a-edbd60c70b9b/image.png)


사용자가 새로운 Cell Outline Disclosure Accessory로 구성된 outline-style의 UI와 상호 작용하면 프레임워크는 자동으로 섹션 스냅샷을 새로운 확장 상태로 업데이트하고 해당 섹션 스냅샷을 데이터 소스에 적용한다.

이러한 사용자 상호작용으로 인한 확장 상태 변경에 대한 알림을 받는 것이 유용한 경우가 많다.

예를 들어 특정 부모가 절대 축소되지 않도록 요구하는 디자인이 있을 수 있다.

이를 지원하기 위해 Diffable Data Source에는 사용자 상호 작용으로 인한 확장 상태 변경에 대한 프로그래밍 방식 제어를 응용 프로그램에 제공하기 위한 새로운 API가 있음

![](https://velog.velcdn.com/images/xoxo0223/post/e7f64fb8-2ef4-4003-98ea-8022736a111a/image.png)

SectionSnapshotHandlers라는 프로퍼티가 가 추가되었고

SectionSnapshotHandlers라는 새로운 구조체 타입이 추가됌

새로운 타입은 5개의 옵셔널 클로저를 포함함!!

위에서 얘기한 요구사항(특정 부모가 절대 축소되지 않도록 하는 디자인)을 처리하기 위해 특정 부모에 대해 false를 반환하여 축소를 방지하는 shouldCollapseItem handler를 제공할 수 있음!!

snapshotForExpandingParent를 사용해서 비용이 많이 드는 콘텐츠의 지연 로드도 지원함

이를 통해서 콘텐츠를 가져오는 데 비용이 많이 들 때 초기 섹션 스냅샷에 로드되는 콘텐츠의 양을 최소화하는 데 유용함

현재 하위 스냅샷의 상태에 따라 해당 콘텐츠를 로드할 수 있음

![](https://velog.velcdn.com/images/xoxo0223/post/1db2dc9d-7866-474a-a7eb-f2d4c6fb272a/image.png)


Diffable Data Source가 제공하는 고급 기능 중 하나는 컬렉션 뷰의 데이터를 고유한 항목 식별자로 모델링하는 기능임

![](https://velog.velcdn.com/images/xoxo0223/post/3b8e83bc-6b11-483b-86a7-c4ec44835726/image.png)


이러한 고유 항목 식별자를 사용하면 프레임워크가 사용자 상호 작용을 기반으로 응용 프로그램을 대신해서 재정렬 변경 사항을 자동으로 커밋할 수 있음

응용 프로그램은 사용자가 시작한 재정렬 상호 작용이 발생했음을 알려야 최종 정보 소스인 응용 프로그램의 백업 저장소에 새로운 시각적 순서를 유지할 수 있음

따라서 재정렬을 지원하기 위해 DIffable Data Source에는 reorderingHandlers라는 새로운 프로퍼티가 있음

![](https://velog.velcdn.com/images/xoxo0223/post/5b607c18-b0a7-446d-a23f-796ed7ee5feb/image.png)

3가지 옵셔널 클로저를 포함하는 구조체임

새로운 API를 통해 재정렬을 활성화하려면 먼저 canReorderItem 클로저를 제공해야 한다.

이것은 사용자가 재정렬 상호작용을 시작하려고 시도할 때 호출됌

true를 반환하면 재정렬 상호 작용을 시작할 수 있음!

사용자가 재정렬 상호 작용을 완료하면 응용 프로그램이 새 주문 상태를 응용 프로그램의 truth 소스에 커밋할 수 있도록 didReorder 클로저가 호출된다.

재정렬 기능을 활성화하려면 canReorderItem 및 didReorder 클로저를 모두 제공해야 한다.

didReorder 클로저는 NSDiffableDataSourceTransaction이라는 새로운 타입을 전달함

트랜잭션은 Diffable Data Source에 대해 수행되는 업데이트에 대해 추론하는 데 필요한 모든 정보를 제공한다.

![](https://velog.velcdn.com/images/xoxo0223/post/cd528ae7-58e0-4f66-b42a-5fc4d1e75a6c/image.png)

해당 타입은 4가지 기본 정보를 제공한다.

1. initialSnapshot → 업데이트가 적용되기 전의 Diffable Data Sourc의 상태이다.
2. finalSnapshot → 업데이트가 적용된 후의 상태

그리고 이 스냅샷 항목 식별자를 직접 사용하여 어플리케이션의 truth 소스에 커밋해야 새로운 순서를 결정할 수 있다.

1. CollectionDifference도 제공뎀 → 응용 프로그램에 Array와 같은 데이터 타입이 있는 경우 CollectionDifference를 여기에 직접 apply할 수 있다.
2. 재정렬 업데이트와 관련된 모든 섹션에 대한 섹션별 세부 정보를 제공하는 섹션 트랜잭션 리스트가 표시됌

섹션 트랜잭션도 매우 유사함

재정렬 업데이트와 관련된 각 섹션에 대해 하나의 섹션 트랜잭션이 제공된다.

먼저 sectionTransaction이 적용된 sectionIdentifier를 검사할 수 있음

이 섹션에 업데이트에 특정한 CollectionDifference와 함께 초기 및 최종 섹션 스냅샷이 있음

![](https://velog.velcdn.com/images/xoxo0223/post/b25b0be1-7099-400c-8dbf-019657abb35b/image.png)


예시로 아라보자

위 코드에서 백업 저장소는 단일 섹션 컬렉션 뷰에 대한 정보 소스를 제공하는 배열임

트랜잭션과 함께 제공되는 Swift 표준 lib CollectionDifference를 사용해서 새로운 백업 저장소를 생성하고 truth 소스를 직접 업데이트한다.

끝~
