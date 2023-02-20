# [WWDC 2020] List in UICollectionView

[Lists in UICollectionView - WWDC20 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2020/10026/)

- iOS 14의 list는 `CollectionView`에서 `UITableView`와 유사한 모양을 제공한다.
    - iOS 13에서 도입한 `CompositionalLayout` 위에 구축했음
    - 매우 유연하고 커스텀할 수 있음
    - `List`에 대한 `self-sizing` 지원을 크게 개선했고 `UICollectionView`에서 `List`를 사용할 때 `self-sizing`을 새로운 기본 동작으로 만들었음
        - 셀의 높이를 수동으로 계산하는 것을 신경 쓸 필요 없고 대신에 auto layout을 사용해서 셀을 간단하게 만들 수 있음 → 나머지는 `Collection View`가 처리함
    - 수동으로 크기 조정이 필요한 경우 셀 하위 클래스에서 `preferredLayoutAttributesFitting`를 재정의하여 이 작업을 수행할 수 있다.

## List의 구성요소

![](https://velog.velcdn.com/images/xoxo0223/post/d4a80c15-eeb1-4394-a2c9-25b09bb5160d/image.png)


UICollectionLayoutListConfiguration은 컬렉션 뷰에서 list를 작성하기 위해 레이아웃 측면에서 필요한 새로운 타입이다.

iOS13에서 도입한 `UICollectionViewCompositionalLayout` 뿐만 아니라 `NSCollectionLayoutSection` 위에 구축된다.

`Compositional Layou`는 2019 Advances in Collection View Layout을 확인해라

List configuration은 plain, grouped, inset-grouped와 같이 tableView에서 스타일로 이미 알고 있는 것과 동일한 모양을 제공한다.

`sidebar`, `sidebar plain`이라고 하는 `UICollectionView List`에서만 사용할 수 있는 새로운 스타일도 있음

(`iPad OS`에서 쓰이는 친구)

일반적인 모양 외에도 `list configuration`에서 `seperator`를 표시하거나 숨길 수 있고 `header`, `footer`를 구성할 수 있는 옵션도 제공한다.

## List를 만드는 방법

![](https://velog.velcdn.com/images/xoxo0223/post/4c0646db-0ba0-408b-8c90-9405e828fda4/image.png)


가장 쉬운 방법은 `UICollectionLayoutListConfiguration`을 만들고 모양을 지정한 다음 이 `configuration`을 사용하여 `UICollectionViewCompositionalLayout`을 만드는 것이다.

위 예제에서는 `insetGrouped` 스타일을 사용하고 있다. 이 스타일은 레이아웃의 모든 섹션이 동일하게 보이는 `UITableView`의 `insetGrouped`와 똑같이 보인다.

List를 만드는 더 강력한 방법이 있는데 이게 per-section setupdla

섹션별 설정에서 동일한 `configiration`을 사용하지만 `configuration` `layout`을 만드는 대신 이 `configuration`을 사용해서 `NSCollectionLayout`을 만든다.

![](https://velog.velcdn.com/images/xoxo0223/post/e37b66cc-e9bc-4797-abe1-399cd19abf0e/image.png)


위 코드는 `Compositional Laout`의 기존 섹션 제공자 `initializer`내에서 사용할 수 있고, 컬렉션 뷰의 모든 섹션에 대해 호출되어 특정 섹션에 특정한 개별 레이아웃 정의를 반환할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/230efea3-8003-47af-8cdb-eaf365914151/image.png)


여기에 보이는 것은 이전에 보여준 간단한 설정과 완전히 동일한 디자인이다.

이제 설정이 완료되었으므로 섹션 별로 레이아웃을 커스텀할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/283d1cbb-5015-4187-b78d-a39913a48485/image.png)


위 코드에서 첫 번째 섹션의 경우 기존 `Compositional Layout API`를 사용하여 빌드한 사용자 지정 그리드 레이아웃을 반환한다.

## UICollectionView list section header footer 만드는법

`UITableView`랑 약간 다르게 작동함

`UICollectionView`에서는 두개를 명시적으로 활성화 해야하고 이를 수행하는 방법은 두 가지가 있음

![](https://velog.velcdn.com/images/xoxo0223/post/ebba19e4-a125-4453-8ab3-37d62e7af889/image.png)

첫번째 방법은 헤더랑 푸터를 보조 뷰로 등록하는 거임

이 예에서는 헤더를 구성하지만 동일한 코드가 푸터에도 적용된다.

`Configuration`에서 헤더와 푸터를 `supplementary`로 설정하기만 하면 된다. 

다음으로 헤더와 푸터를 화면에 렌더링할 때 컬렉션 뷰에서 추가 뷰를 제공할지 묻는 메시지가 표시된다.

이 뷰를 제공하는 가장 쉬운 방법은 `diffable` 데이터 소스에 대한 보충 뷰 공급자를 통하는 것이지만 `UICollectionVIew` `delegate`에 동등한 메서드를 구현할 수도 있다.

위 코드의 콜백 내에서 `elementKindSectionHeader` 또는 elementKindSectionFooter에 대한 요소 종류를 확인하고 적절한 뷰를 구성하고 반환할 수 있다.

이 접근 방식을 사용할 때 Collection View에서 요청할 때 보충 뷰를 제공해야 한다는 점이 중요하다.

만약에 supplement-view 콜백에서 nil을 반환하면 Collection View는 assert 한다.

![](https://velog.velcdn.com/images/xoxo0223/post/7b747a84-e400-44d7-8a35-3444a04f59b9/image.png)


따라서 레이아웃의 일부 섹션에는 헤더가 필요하고 다른 섹션에는 필요하지 않은 경우 섹션별 구성을 사용하고 이 특정 세션이 표시되어야 하는 여부에 따라 모드를 supplementary 또는 none으로 설정해라

![](https://velog.velcdn.com/images/xoxo0223/post/1a4356b4-50f1-49ff-b76e-9a0efb965e83/image.png)


두 번째 방법은 헤더에만 사용할 수 있고 헤더 모드를 firstItemInSection으로 설정하면 활성화 된다.

이것은 헤더처럼 보이도록 이 특정 섹션의 첫 번째 셀을 구성하도록 collection view에 지시한다.

계층적 데이터 구조와 새로운 섹션 스냅샷 API를 사용할 때 권장하는 모드이다.

이것이 어떻게 작동하는지는 Advances in DIffable Data Source 영상에서 확인할 수 있다. 이 모드를 사용할 때 데이터 소스의 첫 번째 항목은 더 이상 섹션의 실제 콘텐츠를 나타내지 않고 섹션의 헤더를 나타내기 때문에 데이터 소스가 알고 있어야 한다.

present 측면에서 고고

iOS 14에서 UICollectionViewListCell이라는 새로운 UICollectionView 셀 하위 클래스를 사용할 수 있음

Collection VIew의 컴포지션 특성을 유지하면서 일반 collection view 셀이 예상되는 모든 위치에서 list cell을 사용할 수 있고 list 섹션이 있는 모든 UICollectionView 셀을 사용할 수 있다는 점이 있음

따라서 원하는 디자인을 달성하는 데 필요한 API의 일부만 선택할 수 있다.

List 셀은 셀 내용의 들여쓰기 뿐만 아니라 seperator의 inset을 구성하기 위해 보다 세분화된 지원을 제공한다.

UITableView와 달리 스와이프 동작은 셀의 기능이 됌

크게 개선된 accessories API가 있고 기본 시스템 콘텐츠 및 배경 구성에 액세스할 수 있다.

### seperators

![](https://velog.velcdn.com/images/xoxo0223/post/8b19a131-a290-4de4-bf08-e8554ffba466/image.png)


Seperator는 셀의 기본 콘텐츠와 정렬되어야 한다. 이 예에서는 셀의 레이블과 정렬되어야 함

![](https://velog.velcdn.com/images/xoxo0223/post/76a88b9a-9d8b-4473-a9e2-c086f26df262/image.png)


UITableView에서는 separator inset이라는 포인트 기반 값을 제공해서 이 작업을 수행한다.

이거때문에 원래 엄청 쉬웠음 그냥 값 주면 되니까 근데 safe area, dynamic size 등등 계속 변하니까 쉽지 않음 (이미지 크기가 변경되면 레이블의 위치가 변경될 수 있기 때문임) 따라서 레이블이 끝난느 위치를 미리 알기 매우 어려움

그래서 List 셀에 separator layout guide라는 새로운 개념을 도입했음

UIKit의 기존 레이아웃 가이드와 약간 다르게 작동함

콘텐츠를 레이아웃 가이드로 제한하는 대신 이 레이아웃 가이드를 콘텐츠로 제한함

완전 반대!!

saparator layout 가이드를 설정하는 가장 쉬운 방법은 먼저 셀의 레이아웃을 구성하고 셀이 의도한 대로 표시되면 constraints를 추가하면 된다.

separatorLayoutGuide의 leading anchor를 레이블의 leading anchor 또는 셀의 기본 콘텐츠로 제한해준다.

List cell은 list section과 함께 separator를 셀의 기본 콘텐츠에 맞춰 자동으로 유지한다.

시스템이 제공하는 거를 사용하면 자동으로 수행됌

## Swipe Action

UITableView와 달리 List 셀의 기능임 셀의 내용과 함께 구성된다.

이미지 뷰 또는 레이블을 구성할 때마다 선행 또는 후행 스와이프 동작 구성을 설정할 수도 있다.

이를 위해 셀과 레이아웃 간의 통신이 필요하므로 list configuration을 사용하여 구성된 섹션 내에서 셀이 렌더링되는 경우에만 swipe action이 지원된다.

![](https://velog.velcdn.com/images/xoxo0223/post/0fd57292-f96e-4a02-a09a-5256bac985a4/image.png)


swipe action handler에서 구성 중인 셀의 indexPath를 절대 캡처하지 않는다.

indexPath는 안정적인 identifier가 아니다.

이 셀의 indexPath는 그 위에 콘텐츠를 삽입하거나 삭제할 때마다 변경되며, 이 특정 셀을 반드시 다시 로드하지는 않는다.

스와이프 동작을 트리거할 때 저장된 indexPath를 사용하여 셀의 데이터 모델을 가져오면 실제로 다른 셀의 데이터에서 작업할 수 있다.

잘못된 데이터를 삭제할 수 있기 때문에 위험함

대신에 데이터 모델을 직접 캡처하거나 이 셀의 콘텐츠를 식별하는 데 사용할 수 있는 안정적인 식별자를 캡처한다.

Diffable 데이터 소스와 안정적인 item identifier, iOS 14의 새로운 셀 등록 타입이 적합하다.

![](https://velog.velcdn.com/images/xoxo0223/post/923dd219-90b8-4ab9-b729-b107e81591e0/image.png)


## Accessories

UITableView에서 액세서리 API는 제한적이었다.

액세서리 타입 및 뷰에 액세스할 수 있었는데, 이는 상호 배타적이며 셀의 후미에만 관련이 있다.

List Cell은 많은 새로운 액세서리 타입을 제공하고 셀의 후행, 선행 측면 모두에 대한 액세서리를 구성할 수 있고 동일한 측면에서 여러 액세서리를 구성할 수도 있다.

UITableView 셀 액세서리는 데코레이션 뷰와 비슷하지만 List Cell에서는 기능을 활성화할 수 있다.

예를 들어 재정렬 액세서리로 셀을 구성하는 경우 필요한 재정렬 콜백도 구현한다고 가정하면 사용자가 이 액세서리를 탭하면 컬렉션 뷰가 자동으로 재정렬 모드로 설정된다.

![](https://velog.velcdn.com/images/xoxo0223/post/6f4cea59-4c43-4553-b3fd-610ee3efda82/image.png)


셀의 액세서리를 구성하려면 List Cell의 단일 액세서리 속성을 UICell 액세서리 영역으로 설정하기만 하면 된다.

위 코드는 disclosureIndicatior, delete 액세서리로 셀을 구성했다.

시스템은 disclosureIndicatior가 항상 셀의 뒤쪽에 있어야 하는 반면 삭제 액세서리는 항상 셀의 앞쪽에 표시된다는 것을 알고 있다.

따라서 UIKit은 자동으로 액세서리를 올바른 순서로 정렬하고 표시함

또한 시스템은 삭제 액세서리가 편집 모드에 있을 때만 표시되어야 함을 알고 있음

편집 모드를 시작하거나 종료할 때 삭제 액세서리를 자동으로 안팎으로 애니메이션함

시스템 기본값을 많이 제공하지만 거의 모든 항목을 커스텀할 수 있음

예를 들면 편집 모드가 아닐때만 disclosureIndicatior를 표시하려면 displayed 매개변수를 whenNotEditing으로 설정하기만 하면 된다.

![](https://velog.velcdn.com/images/xoxo0223/post/5defce17-4dc4-48d0-83ff-b6f04d314cea/image.png)
