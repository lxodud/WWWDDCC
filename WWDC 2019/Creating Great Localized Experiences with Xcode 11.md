# Creating Great Localized Experiences with Xcode 11

[Creating Great Localized Experiences with Xcode 11 - WWDC19 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2019/403/)

![](https://velog.velcdn.com/images/xoxo0223/post/37577849-a96b-4ecb-b899-00ccba272b94/image.png)

세션에서 새로운 현지화 기능을 살펴본다함

![](https://velog.velcdn.com/images/xoxo0223/post/c8b6407f-6c21-4218-9511-7bd2e4a59681/image.png)

iOS 13에는 앱 설정 페이지의 다국어 사용자에게 표시되는 새로운 설정이 있다고함

이제 사용자가 시스템 언어와 상관없이 앱의 언어를 설정할 수 있게 되었다고 한다.

애플워치도 동기화 된다함

macOS도 가능~

현지화를 위해 모든 표준 기반 API를 사용하는 경우 기본적으로 앱에서 무료로 작동한다.

이 작업을 사용자에게 유용하게 만들기 위해 염두해야할 몇 가지 사항

![](https://velog.velcdn.com/images/xoxo0223/post/c110c8fd-23b6-4751-99e0-b222b18bff8c/image.png)


1. 코드에서 어플리케이션 언어를 수동으로 설정하려고 시도하지 마라

API는 사용자가 올바른 언어 및 글꼴 폴백을 받을 수 있도록 많은 작업을 수행한다.

언어를 전환하기 위해 앱 내에 옵션을 추가해야 하는 경우 사용자가 앱의 언어를 전환할 수 있는 설정창?으로 실행하는 것이 좋다.

![](https://velog.velcdn.com/images/xoxo0223/post/dfcb76f8-b29f-4b37-8338-712d44c54271/image.png)

1. 사용자가 앱의 언어를 변경하면 앱이 대상 언어로 다시 실행된다.

이 언어 변경이 사용자에게 원활한 환경이 되도록 하려면 상태 복원 API를 적용하는 것이 좋다.

![](https://velog.velcdn.com/images/xoxo0223/post/f9aa6a14-43d4-4c8e-9ae6-b3cf84a2748b/image.png)


iOS 13에는 복원 API가 추가되었다 함

![](https://velog.velcdn.com/images/xoxo0223/post/c8dc84b4-e050-4901-891e-84c5e0261e7e/image.png)


모든 콘텐츠가 앱 전체에서 한 언어로 일관되게 표시되는 것이 중요하다.

일부 커스텀 또는 서버 측 리소스 로드를 수행하는 경우 여기에서 해당 콘텐츠를 요청할 언어를 결정하는 데 유용할 수 있는 몇 가지 언어가 있음

![](https://velog.velcdn.com/images/xoxo0223/post/df34b4a9-2580-4c5b-bcc5-1558a887918f/image.png)


Locale API는 사용자의 언어 및 지역 기본 설정을 가져오는 데 유용하다.

preferred languages는 사용자 기본 설정 순서대로 언어 리스트를 반환하는 놈임

이 리스트가 앱을 빌드하고 모든 사용자 언어의 글꼴과 리소스를 가져와야 하는 경우 유용할 수 있다.

한 가지 알아야 할 점은 어플리케이션 번들이 지원하는 언어에 관계없이 preferred languages가 언어 리스트, 해당 사용자의 기본 언어를 반환한다는 것임(앱이랑 상관 없이 폰 세팅값을 다 가져온다 그런 뜻 인듯)

따라서 현재 실행 중인 응용 프로그램 언어를 가져오는 데 유용하지 않다!!

![](https://velog.velcdn.com/images/xoxo0223/post/4b743550-b498-4cf1-be7c-1e096a830c2e/image.png)


현재 실행 중인 어플리케이션 언어를 얻으려면 번들 API를 사용할 수 있다.

번들 API는 언어 매칭에 유용하다.

Bundle.main.preferred 현지화는 사용자 기본 설정 언어와 어플리케이션 번들이 지원하는 언어를 모두 고려하고 사용자 기본 설정 순서대로 어플리케이션 번들이 지원하는 언어 목록을 반환한다.

이제 외부 목록이 있는 경우, 예를 들어 서버에서 일부 원격 콘텐츠를 로드하고 있고 서버에 다른 설정 지원 언어가 

있는 경우 Bundle.prefferedLocalizations from 클래스 메서드를 사용할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/20bbc2f9-d740-4c4f-9911-a1960a84f882/image.png)


데모 5:30 고고

요약

![](https://velog.velcdn.com/images/xoxo0223/post/df4bc4ae-8400-4a39-bf05-80cf9c32e859/image.png)!

App Languafe Settong을 통해 사용자는 앱의 언어를 선택할 수 있다.

설정으로 실행하는 것은 사용자가 앱의 언어를 전환할 수 있도록 하는 가장 좋은 방법이다.

상태 복원을 사용하여 해당 언어 전화이 사용자에게 원활한 경험이 되도록 한다.

일부 커스텀 또는 서버 측 리소스 로드를 수행하는 경우 사용 사례에 적합한 번들 또는 로케일 API를 사용하고 있는지 확인한다.

[](https://velog.velcdn.com/images/xoxo0223/post/d3cab669-4ae0-40d1-ba2e-17d2d0685884/image.png)

Xcode에서 현지화 도구의 향상된 기능에 대해 이야기해보자

![](https://velog.velcdn.com/images/xoxo0223/post/5e1fe629-6dbe-49be-8401-675e9728a5f0/image.png)




현지화를 위한 Interface Builder 파일이 포함된 프로젝트 내보내기(exporting)가 이제 평균적으로 15배 빨라졌다 함

![](https://velog.velcdn.com/images/xoxo0223/post/683e9ad7-211a-44af-b93c-0174be77b6d9/image.png)


문자열 추출 프로세르를 재설계하여 이를 수행했다. 따라서 Interface Builder 파일이 많을수록 더 큰 개선을 보게 된다함

또한 작업 속도를 높이고 능률화 하려면 genstrings 또는 ibtool을 직접 호출하지 말고 xcodebuild, exportlocalizations 및 importlocalizations를 사용하여 모든 문자열 파일 생성을 처리한다.

![](https://velog.velcdn.com/images/xoxo0223/post/bb04664b-b2aa-415e-9217-a0c1486f6975/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/54af77ce-1ed6-4a39-9ba2-39227d5c935d/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/b44f525e-d8c7-41cb-90b2-e23d85e3b3ee/image.png)


![](https://velog.velcdn.com/images/xoxo0223/post/bf947975-3296-4727-a09f-5be8cb0d1439/image.png)

![](https://velog.velcdn.com/images/xoxo0223/post/26b122eb-cc8a-4aad-904a-f3c54b1b82e8/image.png)


설정 번들이 Xcode 현지화 카탈로그에 포함된다.


![](https://velog.velcdn.com/images/xoxo0223/post/36bf427d-d569-4966-98ee-d4bab849ac8b/image.png)

따라서 이들은 프로젝트의 다른 어떤 것보다 쉽게 현지화할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/6e04325f-8190-4cf3-8d4a-1b629c90e4e5/image.png)


다음은 asset을 현지화하는 방법임

![](https://velog.velcdn.com/images/xoxo0223/post/b6968e92-2631-4803-9139-8592721991ba/image.png)


지금까지 이미지를 현지화하려면 프로젝트에서 독립적인 파일이어야 했으며 이는 asset catalog에 일부 파일이 있고 현지화된 파일이 손실되었음을 의미할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/2cab628b-bc39-461e-9c27-d5457ec5011c/image.png)
![](https://velog.velcdn.com/images/xoxo0223/post/39d82818-529f-42c3-84eb-d75643fe4164/image.png)


이제 이미지 세트, 시계 컴플리케이션, Apple TV 이미지 스택, 스프라이트 아틀라스, Game Center 대시보드 및 리더보드는 물론 새로운 기호 세트를 현지화할 수 있다.

어캐 하냐?

![](https://velog.velcdn.com/images/xoxo0223/post/263bf0c2-d145-40e1-8678-fa2c06343230/image.png)


Asset Catalog Editor의 Attribute Inspector에서 localized button을 찾을 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/c29234e9-839f-4a1b-8521-91df8457e514/image.png)


클릭하면 해당 asset에 인접한 컨텐츠에 현지화된 특성을 추가하여 Xcode 현지화 카탈로그를 내보낼 준비가 된 것이다.

그러나 다국어를 구사하고 현지화 작업 중 일부를 직접 수행하거나 이미 현지화된 이미지를 자산 카탈로그로 옮기고 있다고 가정해 보자. 해당 버튼을 클릭하면 이미 모든 현지화가 포함된 확인란이 표시된다.

![](https://velog.velcdn.com/images/xoxo0223/post/3f5db470-2257-4e35-ad53-952aa5e95293/image.png)

그 중 하나를 선택하면 Asset Catalog Editor에 웰이 생성되므로 다른 asset과 마찬가지로 현지화된 이미지를 드래그할 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/c0af3ee9-8f07-4bee-9d1f-732f4c740550/image.png)

SF 시스템 기호는 현지화되어 있으므로 작업할 필요가 없다.

커스텀도 현지화할 수 있다.

Attribute Inspector에서 동일한 localized 버튼을 찾을 수 있다.

![](https://velog.velcdn.com/images/xoxo0223/post/84dc1a0a-11f3-423f-bac6-a870aef750f5/image.png)


데모 고고

![](https://velog.velcdn.com/images/xoxo0223/post/aab38968-e532-4ad7-a575-163b088d871c/image.png)



이제 Interface Builder 파일의 내보내기 및 가져오기가  훨씬 빨라졌다.

장치별 문자열에 대한 새로운 stringsdict 규칙이 있다.

설정 번들은 이제 쉽게 현지화할 수 있다.

자산 카탈로그에서 기호와 이미지를 현지화할 수 있다.

# 현지화 테스트하는 방법,,,

이건 추후에,,,
