# Design protocol interfaces in Swift

[Design protocol interfaces in Swift](https://developer.apple.com/videos/play/wwdc2022/110353/)

세션에서 얘기할 3가지

![](https://i.imgur.com/WneWGBT.png)

1. type-erasure 기능의 작동 방식에 대한 설명을 통해 연관 타입을 가진 프로토콜이 existential Any 타입과 상호작용하는 방법을 보여줌

2. Opaque result 타입을 사용하여 인터페이스를 구현에서 분리함으로써 캡슐화를 개선한는 방법을 보여줌

3. 포로토콜의 동일 타입 요구사항이 어떻게 여러개의 서로 다른 구체 타입 집합간의 관계를 모델링하는지 보여줌

## Understand type erasure

![](https://i.imgur.com/8qynkgt.png)

프로토콜 2개와 4개의 구체타입이 있는 데이터 모델이 있음

![](https://i.imgur.com/3SxjIo0.png)
닭은 달걀을 낳고, 소는 우유를 만듬

![](https://i.imgur.com/p6lVrWo.png)

식품의 생산을 추상화하기 위해 Animal 프로토콜에 produce() 메서드를 추가함

연관타입을 이용해 produce() 메서드의 반환타입을 추상화했음

연관타입을 사용해서 구체 타입의 동물을 선언하고 produce() 메서드를 호출하면 구체 동물 타입에 따라 특정 타입의 식품이 반환됨

![](https://i.imgur.com/MFwNNQg.png)

프로토콜 Self 타입은 Animal 프로토콜을 따르는 실제 구체타입을 나타내며 Self 타입에는 Food를 준수하는 연관 Commodity 타입이 있다.

구체적인 Chicken 및 Cow 타입 간의 관계와 동물 프로토콜에 대한 연관 타입 다이어그램을 보자


![](https://i.imgur.com/0LlqLQk.png)

Chicken 타입은 CommodityType가 Egg인 Animal 프로토콜을 따르고

![](https://i.imgur.com/w3nWZDH.png)

Cow 타입은 CommodityType가 Milk인 Animal 프로토콜을 따른다.

![](https://i.imgur.com/8fmJbDP.png)

동물로 가득 찬 농장이 있다고 가정해보면 Farm의 animals 프로퍼티는 any Animal의 이종 배열이다.

![](https://i.imgur.com/d2IgLHv.png)

any Animal 타입이 어떠한 특정 동물 타입이라도 동적으로 저장할 수 있는 박스로 표현되는지는 저번 세션보면뎀

이렇게 다양한 구체 타입에 대해 동일한 표현을 사용하는 전략을 Type Erasure이라고 한다!!


produceCommodities 메서드는 동물들의 배열을 매핑하면서 각 동물에 대해 produces 메서드를 호출하는데 간단해 보이지만 Type Erasure가 실제 동물 타입에 대한 정적 타입 관계를 제거함을 알고 있으므로 이러한 타입을 검사하는 이유를 더 깊게 이해할 필요가 있다.

![](https://i.imgur.com/FKd4n7e.png)

map 클로저의 animal 파라미터는 any Animal 타입이며 produce()의 반환 타입은 연관 타입이다.

existential 타입에 대해 연관 타입을 반환하는 메서드를 호출하면 컴파일러는 타입 소거를 사용하여 호출의 결과 타입을 결정한다. 

![](https://i.imgur.com/kmnbQ3t.png)

타입 소거는 이러한 연관 타입을 동등한 제약 조건을 가진 일치하는 실존 타입으로 대체한다.

우리는 구체적인 동물 타입과 연관 CommodityType 간의 관계를 any Animal과 any Food로 대체하여 제거했다.

any Food 타입은 연관 Commodity 타입의 upper bound 라고 하는데 any Animal에 대해 produce() 메서드가 호출됐기 때문에 반환값은 소거되어 any Food 타입의 값이 반환된다.

![](https://i.imgur.com/z6j4AkQ.png)

Swift 5.7의 새로운 기능인 associated type erasure의 작동 방식을 아라보자

![](https://i.imgur.com/cWe2z3S.png)

프로토콜 메서드의 결과 타입에 나타나는 연관 타입은 producing position??에 있다고 한다. 메서드를 호출하면 해당 타입의 값이 생산되기 때문임

![](https://i.imgur.com/GYmpe2C.jpg)

any Animal에 대해 이 메서드를 호출하는 경우 컴파일 타임에는 구체적인 결과 타입은 알 수 없지만 upper bound의 하위 타입이라는 건 알 수 있다.

위 그림을 보면 런타임에 Cow 인스턴스를 가진 any Animal에 대해 produce()를 호출하고 있는데

![](https://i.imgur.com/sefdeYr.jpg)

이 경우 Cow의 produce() 메서드는 Milk를 반환하고 Milk는 Animal 프로토콜의 연관 타입인 CommodityType의 upper bound인 any Food 내에 저장될 수 있다.

Animal 프로토콜을 따르는 모든 구체 타입에 대해 항상 안전하다.

![](https://i.imgur.com/8CBBczg.png)

메서드나 생성자의 매개변수 리스트에 연관 타입이 나타난다면 어떻게될까?

위 그림에서 Animal 프로토콜의 eat() 메서드는 comsuming position에 연관된 타입인 FeedType을 가지고 있다.

메서드를 호출하려면 이 타입의 값을 전달해야 하는 것이다.

![](https://i.imgur.com/kSIUTk6.png)

그런데 변환이 반대 방향으로 진행되기 때문에 타입 소거를 수행할 수 없으며 구체 타입을 알 수 없기 때문에 연관 타입의 upper existential 타입은 실제 구체 타입으로 안전하게 변환되지 않는다.

예를 들어보자

![](https://i.imgur.com/e5FfO8G.png)

이번에도 Cow를 저장하고 있는 any Animal이 있고

![](https://i.imgur.com/B2jbOYg.png)

Cow의 eat 메서드가 Hay를 취한다고 가정할 경우 Animal 프로토콜의 연관된 타입인 FeedType의 upper bound는 any AnimalFeed이다.

임의의 any AnimalFeed가 주어질 경우 Hay 구체 타입을 저장한다는 것을 정적으로 보장할 방법은 없다. 타입 소거는 소비 위치에서 연관 타입을 사용하는 것을 허용하지 않는다.

![](https://i.imgur.com/p1wEkOa.png)

그니까 파라미터로 예를 들어서 Cow의 eat 메서드는 Hay 구체타입이 무조건 들어와야 하는데 그걸 정적으로 보장할 방법이 없다 이 말임

그래서 대신 Opaque some 타입을 취하는 함수에 입력함으로써 existential any 타입을 언박싱해야한다.



사실 연관된 타입의 이러한 타입 소거 동작은 Swift 5.6에서 볼 수 있는 기존의 기능과 유사하다.

![](https://i.imgur.com/BKMXAAY.png)

참조 타입을 복제하기 위한 프로토콜이 있다고 해보자.
이 프로토콜은 Self를 반환하는 단일 clone() 메서드를 정의하는데 any Cloneable 타입의 값에 대해 clone()을 호출하면 결과 타입 Self가 upper bound 까지 타입 소거된다.

![](https://i.imgur.com/2d5xURt.png)

Self 타입의 upper bound는 항상 프로토콜 자체이므로 any Cloneable 타입의 새로운 값이 반환된다.

요약하면 any를 사용하여 값 타입이 프로토콜을 따르는 구체타입을 저장하는 existential 타입임을 선언할 수 있다는 것이다. 이 기능은 연관 타입을 가진 프로토콜에서도 사용 가능하다.

생산 위치에서 연관 타입을 가진 프로토콜 메서드를 호출할 경우 연관 타입은 연관 타입 제약조건을 가진 또 다른 existential 타입인 upper bound 까지 타입 소거된다.

![](https://i.imgur.com/iFSq0h5.png)

## Hide implementation details
구체 타입을 추상화하는 것은 함수 입력뿐만이 아니라 출력에도 유용한데 구체 타입은 구현했을 때만 볼 수 있다.

그럼 구체적인 결과 타입을 추상화하여 구현 세부 정보에서 코드 조각의 필수 인터페이스를 분리함으로써 정적 타입 할당을 더욱 모듈화하고 변화에 강하게 만드는 방법을 보자

![](https://i.imgur.com/xOvazOi.png)

동물에게 먹이를 줄 수 있도록 Animal 프로토콜을 일반화해보자

동물은 배가 고파질 테고 그러면 먹이를 먹여야 하니 Animal 프로토콜에 isHungry 프로퍼티를 추가한다.

![](https://i.imgur.com/aKiD6sN.png)

Farm의 feedAnimals는 배고픈 하위 집합 동물에게 먹이를 주는데 배고픈 동물의 하위 집합에 대한 연산을 hungryAnimals 프로퍼티로 나누었다.

이 hungryAnimals의 초기 구현은 filter 메서드를 사용하여 isHungry 프로퍼티가 true인 동물의 하위 집합을 선택한다. 
any Animal 배열에 대해 filter를 호출하면 any Animal의 새로운 배열이 반환된다.

feedAnimals는 hungryAnimals를 한 번 반복한 다음 즉시 이 임시 배열을 버린다는 걸 알 수 있는데 농장에 배고픈 동물이 많은 경우 비효율적인 방법이다.

이러한 임시 할당을 피할 수 있는 방법 중 하나는 표준 라이브러리의 지연 계산 컬렉션 기능을 사용하는 것이다.

![](https://i.imgur.com/2U1JYr9.png)

지연 계산 컬렉션은 filter에 대한 호출을 lazy.filter로 대체하면 얻을 수 있다. 

지연 계산 컬렉션은 filter에 대한 일반 호출을 통해 반환된 배열과 동일한 요소를 갖지만 임시 할당을 피한다.

하지만 이 경우 hungryAnimals 프로퍼티의 타입은 any Animal 배열의 LazyFilterSequence라는 복잡한 구체 타입으로 선언되어야 하는데 이는 불필요한 구현 세부 사항을 노출한다.

클라이언트인 feedAnimals는 hungryAnimals 구현 시 lazy.filter를 썼는지 알 필요가 없고 반복하는 컬렉션을 얻을 수 있다는 것만 알면 된다.

![](https://i.imgur.com/obTwSeV.png)

Opaque result 타입을 사용하면 복잡한 구체 타입을 컬렉션의 추상 인터페이스 뒤로 숨길 수 있다. 그럼 hungryAnimals를 호출하는 클라이언트는 컬렉션 프로토콜을 따르는 어떤 구체 타입을 얻고 있다는 것만 알고 특정 구체 타입의 컬렉션은 모르게 된다.

![](https://i.imgur.com/HSKae6B.png)

하지만 위 그림처럼 이러한 방법은 클라이언트로부터 정적 타입 정보를 너무 많이 숨겨버린다.
hungryAnimals가 컬렉션을 따르는 구체 타입을 출력한다고 선언하지만 그 컬렉션의 요소 타입에 대한 정보는 전혀없다.

요소 타입이 any Animal이라는 지식이 없다면 요소 타입을 전달하는 작업만 할 수 있을 뿐이고 Animal 프로토콜의 어떤 메서드도 호출할 수 없다. 

그럼 Opaque result 타입인 some Collection을 자세히보자.

![](https://i.imgur.com/cDwW1G8.png)

제한된 Opaque result 타입을 사용하면 구현 세부 정보를 감추고 인터페이스 정보를 노출시키는 정도를 균형 있게 조절할 수 있다.

![](https://i.imgur.com/aLG8VQb.png)

제한된 Opaque result 타입이란 Swift 5.7의 새로운 기능으로 프로토콜 이름 뒤에 있는 꺾쇠 괄호 안에 타입 인수를 넣는 형식으로 작성하며 Collection 프로토콜의 인수는 Element 타입 하나만 사용한다. 

![](https://i.imgur.com/7rwYQYW.png)

이렇게 hungryAnimals가 제한된 Opaque result 타입으로 선언되면 클라이언트는 이게 any Anumal 배열의 LazyFilterSequence라는 사실은 알 수 없게 되지만 any Animal과 같은 Element 연관 타입을 가진 컬렉션을 따르는 구체 타입이라는 것은 여전히 알 수 있다.

우리가 원하는 인터페이스임!!

![](https://i.imgur.com/9CYxKkg.png)

feedAnimals 메서드의 for문 내에서 animal 변수는 any Animal 타입을 갖게 되므로 Animal 프로토콜 메서드를 각각의 배고픈 동물에 대해 호출할 수 있다.

![](https://i.imgur.com/WWsFNGO.png)

이렇게 동작할 수 있는 이유는 Collection 프로토콜이 Element 연관 타입을 기본(primary) 연관 타입으로 선언했기 때문이다.

이렇게 프로토콜 이름 뒤에 괄호로 묶인 하나 이상의 연관 타입을 넣으면, 기본(primary) 연관 타입을 가진 고유한 프로토콜을 선언할 수 있다.

기본(primary) 연관 타입으로 가장 적합한 것은 컬렉션의 Element타입 같이 호출자가 일반적으로 제공하는 타입이고 컬렉션의 반복문 타입 같은 구현 세부 정보는 적합하지 않다.

![](https://i.imgur.com/z0k8Vbp.png)

또한 프로토콜의 기본(primary) 연관 타입과 그 프로토콜을 따르는 구체 타입의 제네릭 매개변수는 일치하는 경우가 많은데 위 사진을 보면 Collection의 Element 기본(primary) 연관 타입이 Array 및 Set의 Element 제네릭 파라미터로 구현되었으며 두 구체 타입은 컬렉션을 따르는 표준 라이브러리에 의해 정의된다.

![](https://i.imgur.com/FNRgaEn.png)

Elemen의 Collection은 some 키워드를 사용하는 Opaque result으로 쓸 수도 있고 any 키워드를 사용하는 제한된 Existential 타입과 함께 쓸 수 있다.

![](https://i.imgur.com/4DNjj91.png)

Swift 5.7전에는 특정 제네릭 인수를 가진 Existential 타입을 나타내려면 고유한 데이터 타입을 작성해야 했지만 Swift 5.7은 제한된 Existential 타입이라는 개념을 언어에 적용했다.

![](https://i.imgur.com/OTqvZ5p.png)

hungryAnimals가 hungryAnimals 수를 지연 계산할지 즉시 계산할지 선택할 수 있도록 하고 싶은 경우에 Animal의 Opaque 컬렉션을 사용하면 함수가 2가지 기본 타입을 반환한다는 오류가 발생하는데

![](https://i.imgur.com/drFrk45.png)

이를 해결하려면 any Animal의 any Collection을 반환하여 이 API가 호출시 다른 타입을 반환할 수 있음을 알려주면 된다.

![](https://i.imgur.com/VDZ416f.png)

기본 연관 타입을 제한하는 기능은 Opaque 타입과 Existential 타입에 새로운 수준의 표현력을 제공한다.

이것은 컬렉션과 같이 다양한 표준 라이브러리 프로토콜과 사용할 수 있으며 기본 연관 타입을 가진 고유한 프로토콜을 선언할 수도 있다.

## Identify type relationships

Opaque 타입 제네릭 코드 작성은 추상 타입 관계에 기반해야 하는데 연관 프로토콜을 사용하여 여러 추상 타입 간에 필요한 타입 관계를 식별하고 보장하는 방법을 살펴보자

![](https://i.imgur.com/nfFzzOt.png)

Animal 프로토콜에 동물들이 먹는 사료의 구체적인 타입에 대한 새로운 연관 타입과 동물들에게 해당 타입의 사료를 먹도록 지시하는 eat 메서드를 추가하려고 하는데 재미를 위해 좀 더 복잡하게 만들어보자

동물에게 먹이를 주기 전에 적절한 타입의 작물을 재배하고 수확하여 사료를 생산해야 하게 구현할꺼임

![](https://i.imgur.com/ayZlAfQ.png)

첫 번째 구체 타입 집합은 이렇다.

소는 Hay(건초)를 먹으니 소가 주어지면 건초를 키워야한다. 그리고 알파파를 수확해서 소가 먹을 수 있는 건초로 가공해서 소에게 먹인다.


![](https://i.imgur.com/gTyCHZQ.png)

이건 두번째 구체타입

닭의 모이는 스크래치이므로 닭이 입력되면 먼저 기장이라고하는 곡물을 재배하고 이걸 수확하고 가공하여 스크래치를 만들어서 닭에게 먹인다.

![](https://i.imgur.com/0YKfWjr.png)

이러한 두 가지 연관 구체 타입 세트를 추상화해서 feadAnimal 메서드를 한 번만 구현함으로써 소와 닭 그리고 앞으로 데려올 새로운 타입의 동물들에게까지 먹이를 주려고 한다.

feedAnimal()은 소비 위치에서 연관 타입 Animal 프로토콜의 eat 메서드와 함께 작동해야 하므로 feedAnimal() 메서드가 some Animal을 매개변수 타입으로 사용한다고 선언하여 Existential 타입을 언박싱한다.


![](https://i.imgur.com/BnE5Iz9.png)


먼저 프로토콜과 연관 타입에 대해 그동안 배운걸 활용하여 AnimalFeed와 Crop 프로토콜 한 쌍을 정의해보자

AnimalFeed에는 Crop을 따르는 연관 CropType이 있고 Crop에는 AnimalFeed를 따르는 연관 FeedType가 있다.

이번에도 각 프로토콜의 타입 매개변수 다이어그램을 볼 수 있는데 

먼저 AnimalFeed

모든 프로토콜을 구체적인 준수 타입을 나타내는 Self 타입을 가지고 우리의 프로토콜은 Crop을 따르는 연관 CropType을 가진다. 연관 CropType은 AnimalFeed를 따르는 중첩 연관된 FeedType을 가지며 이 타입은 또 Crop을 따르는 중첩 연관된 Crop를 갖는다.
![](https://i.imgur.com/wYaDVf2.png)

![](https://i.imgur.com/vQHg8XW.png)

사실상 이런 식으로 Crop을 따르는 연관 타입 간에 앞뒤로 번갈아가며 무한 중첩이 형성된다.

![](https://i.imgur.com/greuFGc.png)

Crop 프로토콜의 경우도 한 칸만 이동될 분 상황은 마찬가지이다.

Crop을 따르는 Self에서 시작하여 Self는 AnimalFeed를 따르는 연관 FeedType을 갖고 FeedType은 Crop을 따르는 중첩 연관된 CropType을 갖는 식으로 끝없이 나아간다.


![](https://i.imgur.com/0CAUgcj.png)
![](https://i.imgur.com/LSihoZr.png)
![](https://i.imgur.com/CVUzEIm.png)

그럼 이러한 프로토콜이 구체 타입 간의 관계를 정확하게 모델링하는지 보자

![](https://i.imgur.com/6EbmDnB.png)

동물에게 먹이를 주기 전에 작물을 재배하여 올바른 타입의 사료로 가공해야 한다는 점을 기억하자

grow()는 AnimalFeed 프로토콜의 정적 메서드인데 그 말은 AnimalFeed를 따르는 특정 값이 아닌 타입에 대해 직접 호출되어야 한다는 뜻이다.

따라서 AnimalFeed를 따르는 타입의 이름을 넣어야 하는데 우리가 가진 것은 다른 프로토콜인 Animal을 따르는 일부 타입의 특정 값뿐이다. 

![](https://i.imgur.com/5gutptV.png)

어떤 타입이라는 걸 알고 Animal에는 AnimalFeed를 따르는 연관 FeedType이 있다.

![](https://i.imgur.com/v1VFfRI.png)

따라서 이 타입을 grow() 메서드 호출의 기본으로 사용할 수 있다.
AnimalFeed의 grow() 메서드는 AnimalFeed의 중첩 연관 CropType 타입을 갖는 값을 반환한다.

CropType이 Crop을 따른다는 것을 알고 있으므로 여기에서 harvest()를 호출할 수 있는데 그럼 어떤 결과를 얻게 될까?

![](https://i.imgur.com/bwWojql.png)

harvest()는 Crop 프로토콜의 연관 FeedType을 반환하도록 선언되었고 이 경우 호출의 기본 타입은 
(some Animal).FeedType.CropType이므로 harvest()는 (some Animal).FeedType.CropType.FeedType 타입의 값을 반환하게 될텐데 이건 잘못된 타입이다. eat 메서드가 
(some Animal).FeedType를 요구했기 때문이다.

![](https://i.imgur.com/9Tn4ZRs.png)

프로그램이 제대로 작성되지 않은 것이다.

이 프로토콜의 정의는 동물 사료 타입부터 시작하여 작물을 재배하고 수확할 경우 우리가 동물에게 먹이고자 하는 처음 시작과 동일한 타입의 동물 사료를 되돌려 받을 수 있다는 것을 보장하지 안흔ㄴ다.

또 다른 문제점은 프로토콜 정의가 너무 일반적이라서 구체 타입 간에 요구되는 관계를 정확하게 모델링하지 못한다는 것이다.

![](https://i.imgur.com/6D1PW6L.png)

Hay와 Alfafa 타입을 통해 그 이유를 확인해보자

![](https://i.imgur.com/ktnV30L.png)

건초를 재배하면 알팔파를 얻고 알팔파를 수확하면 건초를 얻는 과정이 반복되는데 코드를 리팩토링하던 중에 실수로 Alfalfa의 harvest() 메서드 반환 타입이 변경되어 Hay 대신 Scratch가 반환된다고 상상해보자

![](https://i.imgur.com/DaqHwIg.png)

이런 우발적인 변경이 발생하더라도 구체 타입은 AnimalFeed 및 Crop 프로토콜의 요구사항을 여전히 충족한다. 

애초에 원했던 작물을 재배하고 수확하면 처음 시작한 것과 동일한 타입의 사료가 생산된다는 불변성을 위반하더라도 말이다.

![](https://i.imgur.com/ptzCEFF.png)

AnimalFeed 프로토콜을 다시 살펴보자

여기서 진짜 문제는 어떤 면에서 보면 별도의 연관 타입이 너무 많다는 것이다. 따라서 이러한 연관 타입 중 2개는 사실상 동일한 구체 타입이라는 사실을 명시해 두어야 한다.

그러면 잘못 작성된 구체 타입이 프로토콜을 따르는 것을 방지할 수 있다.

또한 feedAnimal() 메서드가 의도한 대로 작동하도록 보장할 수 있다.

![](https://i.imgur.com/Nfodd2M.png)

이러한 연관 타입 간의 관계는 where문에 동일한 타입 요구 사항을 작성하여 표현할 수 있다.
![](https://i.imgur.com/KzaecDT.png)

동일 타입 요구사항은 중첩 가능성이 있는 2개의 연관 타입이 사실상 동일한 구체타입이어야 한다는 정적 보장을 나타낸 것으로 동일 타입 요구사항을 추가하면 AnimalFeed 프로토콜을 따르는 구체 타입에 제한 조건이 부과된다.

![](https://i.imgur.com/NiZFXoV.png)

동일 타입의 요구사항에서 Self.CropType.FeedType이 Self와 동일한 타입임을 선언하는 것인데 다이어그램으로 표현하면 어떻게 될까?

![](https://i.imgur.com/EaSPgf2.png)

바로 이런식으로 시각화할 수 있다.
AnimalFeed를 따르는 각 구체 타입은 Crop을 따르는 CropType을 갖지만 

![](https://i.imgur.com/s1PZF0S.png)

이 CropType의 FeedType은 AnimalFeed를 따르는 다른 타입일 뿐 아니라 원래의 AnimalFeed와 동일한 구체타입이다.

![](https://i.imgur.com/XMgjBLk.png)

중첩된 연관 타입의 무한한 탑 대신에 모든 관계를 연관 타입의 단일 쌍으로 축소해보았다.

Crop 프로토콜은 어떻게 될까?

보면 Crop의 FeedType을 한 쌍의 타입으로 축소했음에도 아직도 연관 타입이 너무 많다.

![](https://i.imgur.com/8zuyNcC.png)

우리가 원하는 것은 Crop의 FeedType.CropType이 최초에 시작했던 Crop과 같은 타입이라고 하는 것이다.

![](https://i.imgur.com/WhigIbL.png)

이제 이 두 프로토콜이 동일 타입 요구사항을 갖추었으니 feedAnimal() 메서드를 다시 살펴보자

![](https://i.imgur.com/FefJMuR.png)

이번에도 some Animal의 타입으로 시작한 다음 AnimalFeed 프로토콜을 따르는 동물의 FeedType을 얻는다.

![](https://i.imgur.com/8V5ETLA.png)

또, 작물을 키울 때 some Animal의 FeedType.CropType을 얻는데 이번에는 작물을 수확할 때 다른 중첩 연관 타입을 얻는 대신 동물에게 필요한 정확한 사료 타입을 얻게 되고 이제 동물들에 갓 재배한 올바른 타입의 사료를 넣어줄 수 있다.

![](https://i.imgur.com/uFTDCWh.png)

마지막으로 지금까지 살펴본 모든 것을 합친 Animal 프로토콜의 연관 타입 다이어그램을 보자

여기 일치하는 타입의 두 가지 세트가 있는데 첫 번째에는 Cow, Hay, Alfalfl가 있고 두 번째에는 Chicken, Scratch, Millet가 있다.

![](https://i.imgur.com/a9Ffvs8.png)

3개의 프로토콜이 3가지 구체 타입의 각 세트 간의 관계를 정확히 모델링하는 방식에 주목한다.

데이터 모델을 이해하면 동일 타입 요구사항을 사용하여 중첩된 서로 다른 연관 타입 간의 동일성을 정의할 수 있으며 이러한 관계에 기반하여 프로토콜 요구 사항에 대한 여러개의 호출을 연결하는 제네릭 코드를 작성할 수 있다. 

