---
layout: post
title: "[Reactor] Project Reactor Reference Document."
description: "Project React 공식 참고 문서 번역."
categories: blog
tags: [reactor, reference, document]
image:
feature:
date: 2019-05-05T21:31:50-04:00
---


> 이 문서는 `Project Reactor`의 [공식 참고 문서](https://projectreactor.io/docs/core/release/reference/)를 학습 목적으로 번역한 것입니다. 

# Getting Started
이 섹션은 Reactor를 활용하기 위한 도움을 주는 정보들을 포함하고 있습니다. 이 정보들은 아래 내용을 포함합니다.
- Introducing Reactor
- Prerequisites
- Understanding the BOM
- Getting Reactor

## Introducing Reactor
`Reactor`는 효율적인 요구 관리(demand management, `배압(backpressure)`의 형태로 관리되는)를 통해 `JVM`에 충분한 `non-blocking reactive programming` 토대를 제공합니다.
`Reactor`는 `CompletableFuture`, `Stream` 그리고 `Duration`으로 대표되는 Java 8 functional API들을 직접적으로 통합하고 있습니다.
`Raactor`는 조합 가능한 비동기 sequence API들인 `Flux`(N개 이상의 요소들)와 `Mono`(0 혹은 한 개의 요소)를 제공하며, 넓은 의미로 [`Reactive Streams`](https://www.reactive-streams.org/) 스펙을 구현하고 있습니다.  
`Reactor`는 또한 [`reactor-netty`](https://github.com/reactor/reactor-netty) 프로젝트를 통해 프로세스간 non-blocking 통신을 지원하고 있습니다. `Micro-Service Architecture`에 적합하며, `Reactor-Netty`는 WebSocket을 포함한 HTTP, TCP 그리고 UDP 통신을 위한 backpressure-ready 엔진을 제공합니다. Reactive encoding과 decoding 또한 충분히 지원합니다.
> `Reactive encoding/decoding` : Reactive Streams back-pressure를 통한 non-blocking I/O로 byte level contents와 상위 레벨의 object간의 직렬화/역직렬화를 지원하는 것을 말하는 것 같습니다.

## Prerequisites
`Reactor Core`는 Java 8 이상에서 동작합니다.  
또한 `org.reactivestreams:reactive-streams:1.0.2`에 이행적인 의존성을 가집니다.

> 안드로이드 지원:
> - `Reactor 3`는 공식적으로 안드로이드를 지원하지 않습니다. RxJava 2를 사용하는 것을 고려 해보세요. 
> - 하지만 안드로이드 SDK 26(Android O)와 그 이상 버전에서의 동작에는 문제가 없습니다.
> - 우리는 안드로이드 지원이라는 잇점에 대한 변화를 고려하는 것에 열려 있지만, 그것을 보장할 수는 없습니다. 각각의 경우를 고려하여 결정 되어야 합니다.

## Understanding the BOM
`Reactor 3`는 `reactor-core 3.0.4` `Aluminium` 릴리즈들 이후로부터 `BOM` 모델을 사용하고 있습니다. 이 나열된 리스트 그룹의 아티팩트들은 함께 잘 동작함을 의미하며, 각각의 아티팩트들에 별도의 버전 관리 체계가 존재하더라도 관련된 버전을 제공합니다.  
`BOM`(Bill of Materials)는 그 자체로 버전 관리되며, 한정자(qualifier) 뒤에 코드네임이 따라오는 release train scheme을 사용합니다. 아래는 몇 가지 예입니다.

> Aluminium-RELEASE  
> Californium-BUILD-SNAPSHOT  
> Aluminium-SR1  
> Bismuth-RELEASE  
> Californium-SR32  

코드네임은 전통적인 MAJOR.MINOR 번호로 표현됩니다. 그들은 알파벳순으로 증가하는 [주기율표](https://en.wikipedia.org/wiki/Periodic_table#Overview)에서 따왔습니다.  
한정자들은 시기 순으로 아래와 같습니다.

> BUILD-SNAPSHOT  
> M1..N: 마일스톤 혹은 개발자 프리뷰  
> RELEASE: 해당 코드네임 시리즈의 첫 GA(General Available) 릴리즈  
> SR1..N: 해당 코드네임 시리즈의 다음 GA 릴리즈로 Service Release를 나타내고 PATCH number와 동일함  

## Getting Reactor
초기에 언급 했던 것 처럼, `Reactor`를 사용하는 가장 쉬운 방법은 `BOM`을 사용하고 프로젝트에 상응하는 의존성을 추가하는 것입니다. 그러한 의존성을 추가할 때, 반드시 버전을 생략함으로써 BOM으로부터 버전을 획득할 수 있도록 해야 함을 주의합니다.  
만약 아티팩트의 특정 버전을 강제하고 싶은 경우에는 버전명을 설정할 수 있숩니다. 또한 `BOM` 사용을 포기하고 전체 의존성에 아티팩트 버전을 명시할 수도 있습니다.

### Maven Installation
`BOM`의 개념은 기본적으로 `Maven` 지원에 의한 것입니다. 우선, 아래 코드 조각을 당신의 `pom.xml`에 추가함으로써 `BOM`을 import 해야 합니다. 만약 최상위 섹션인 `dependencyManagement`가 이미 당신의 `pom.xml`에 존재한다면 `contents`만 추가하면 됩니다.
```xml
<dependencyManagement> {1}
    <dependencies>
        <dependency>
            <groupId>io.projectreactor</groupId>
            <artifactId>reactor-bom</artifactId>
            <version>Bismuth-RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```
{1} `dependencyManagement` 태그를 주의하세요. 이 태그는 기존의 `dependencies` 섹션에 추가 됩니다.  

다음으로, 적절한 `Reactor` 프로젝트의 의존성을 추가하세요. 보통의 경우 아래와 같이 <version> 태그를 제외합니다.
```xml
<dependencies>
    <dependency>
        <groupId>io.projectreactor</groupId>
        <artifactId>reactor-core</artifactId> {1}
        {2}
    </dependency>
    <dependency>
        <groupId>io.projectreactor</groupId>
        <artifactId>reactor-test</artifactId> {3}
        <scope>test</scope>
    </dependency>
</dependencies> 
```
{1} 코어 라이브러리 의존성.
{2} 버전 태그가 존재하지 않습니다.
{3} `reactor-test`는 `reactive streamse`에 대한 유닛 테스트 기반을 제공합니다.

### Gradle Installation
`Gradle`은 `Maven BOM`에 대한 코어 지원이 없지만, `Spring`의 `gradle-dependency-management` 플러그인을 사용할 수 있습니다.  
우선, `Gradle Plugin Portal`에 플러그인을 적용하세요.
```gradle
plugins {
    id "io.spring.dependency-management" version "1.0.6.RELEASE" {1}
} 
```
{1} 기술했던 것과 같이, `1.0.6 RELEASE`는 플러그인의 최신 버전입니다. 업데이트를 확인 하세요.  
그리고 `BOM`을 import 하기 위해 아래 항목을 추가하세요.
```gradle
dependencyManagement {
     imports {
          mavenBom "io.projectreactor:reactor-bom:Bismuth-RELEASE"
     }
}
```
마지막으로 버전 넘버 명시 없이 의존성을 추가하면 됩니다.
```gradle
dependencies {
     compile 'io.projectreactor:reactor-core' {1}
}
```
{1} 세 번째(버전) 부분이 없음 : 버전을 위한 부분으로, `BOM`에서 값을 취하게 됨.

### Milestones and Snapshots
마일스톤과 개발자 프리뷰들은 `Maven Central` 보다는 `Spring Milestones` repository를 통해 배포 됩니다. 빌드 설정 파일에 추가 하기 위해, 아래 코드 조각을 사용하세요.  

*Milestones in Maven*
```xml
<repositories>
	<repository>
		<id>spring-milestones</id>
		<name>Spring Milestones Repository</name>
		<url>https://repo.spring.io/milestone</url>
	</repository>
</repositories>
```
`Gradle`의 경우 아래 코드 조각을 사용하면 됩니다.

*Milestones in Gradle*
```gradle
repositories {
  maven { url 'https://repo.spring.io/milestone' }
  mavenCentral()
}
```
이와 유사하게, 별도의 지정된 레파지토리에 존재하는 스냅샷의 경우 아래와 같이 적용할 수 있습니다.

*BUILD-SNAPSHOTs in Maven*
```xml
<repositories>
	<repository>
		<id>spring-snapshots</id>
		<name>Spring Snapshot Repository</name>
		<url>https://repo.spring.io/snapshot</url>
	</repository>
</repositories>
```

*BUILD-SNAPSHOTs in Gradle*
```gradle
repositories {
  maven { url 'https://repo.spring.io/snapshot' }
  mavenCentral()
}
```

# Introduction to Reactive Programming
`Reactor`는 `Reactive Programming` 패러다임의 구현체이며, `reactive programming`은 아래와 같이 요약할 수 있습니다.
> Reactive programming은 데이터 스트림과 변화의 전파와 관련된 비동기 프로그래밍(asynchrounous programming) 패러다임입니다. 이는 채택한 프로그래밍 언어를 이용해 정적이거나(ex. 배열) 동적인(ex. event emitter) 데이터 스트림을 쉽게 표현할 수 있음을 의미합니다.  
> — https://en.wikipedia.org/wiki/Reactive_programming

`Reactive programming`을 향한 첫 걸음으로써, `Microsoft`는 `.NET` 생태계에 `Reactive Extensions`(Rx) 라이브러리를 만들었습니다. 그리고 `RxJava`는 `JVM` 상의 `reactive programming`을 위해 구현 되었습니다. 시간이 흐르면서, `JVM` 상에서 `reacive library`에 대한 인터페이스와 상호작용에 대한 일련의 규격을 정의하는 `Reactive Streams`라는 노력을 통해 자바 표준화로 나타나게 되었습니다. 이 인터페이스는 Java 9의 `Flow` 클래스 아래로 통합 되었습니다.  
  
`Reactive Programming` 패러다임은 종종 객체 지향 언어에서 `Observer deisgn pattern`의 확장으로서 표현되곤 합니다. 이러한 모든 라이브러리의 `Iterable-Iterator` 쌍에 대한 이중성이 있으므로, 친숙한 `Iterator pattern`과 `main Reactive Pattern`을 비교할 수 있습니다. 이들 간에 가장 주요한 차이점은, `Iterator`는 `pull-based`로 동작하는 반면 `Reactive Streams`는 `push-based`로 동작한다는 점입니다.  
  
`Iterator`를 사용하는 것은 값에 접근하는 방법이 전적으로 `Iterable`에게 달려있음에도 불구하고 명령형(imperative) 프로그래밍 패턴입니다. 순서적으로 
언제 `next()` 아이템에 접근할지를 결정하는 것은 전적으로 개발제의 몫입니다. `Reactive Streams`에서는 `Iteratable-Iterator` 쌍에 대응하는 `Publisher-Subscriber` 쌍이 존재합니다. 그러나 새로운 값이 발생했을 때 그것이 가용함을 `Subscriber`에게 알리는 것은 `Publisher`이며, 이러한 `push` 관점이 `reactive` 하기 위한 열쇠입니다. 또한 푸시 되는 값들에 대해 적용된 동작들이 명령형 프로그래밍에 비해 선언적으로 표현됩니다. 프로그래머는 정확한 제어 흐름을 나타내는 대신 계산 논리를 표현할 수 있습니다.  
  
값을 푸시하는 것에 더해, 에러 핸들링과 값의 완료 라는 측면은 잘 정의된 방법으로 처리 됩니다. `Publisher`는 `onNext()`를 호출함으로써 `Subscriber`에게 새로운 값을 push 할 수 있고, 또한 `onError()`를 호출하여 에러 시그널을 보내거나 `onComplete()` 호출을 통해 완료 시그널을 보낼 수 있습니다. 에러와 완료 모두 sequence를 완료 시킵니다. 이것은 아래와 같이 요약할 수 있습니다.

```java
onNext x 0..N [onErrpr | onComplete]
```

이러한 접근 방식은 매우 유연합니다. 값이 없거나, 하나만 존재하거나, 혹은 n개의 값이 존재하는 경우(무한한 경우도 포함하여)에 대한 유즈 케이스를 모두 설명할 수 있습니다.  
  
그러나 우선 왜 asynchronous reactive 라이브러리가 필요한지 고려해봅시다.

## Blocking Can Be Wasteful

현대의 응용 프로그램들은 수 많은 동시적인 유저들에게 도달할 수 있으며, 현대의 하드웨어가 계속해서 발전 함에도 불구하고 소프트웨어의 성능은 여전히 주요한 관심사입니다. 프로그램의 성능을 향상 시키기 위한 두 가지 광범위한 방법이 있습니다.
1. paralleize(병렬화): 더 많은 쓰레드와 하드웨어 리소스를 사용합니다.
2. 현재의 자원을 더욱 효율적으로 사용할 수 있는 방법을 찾습니다.

보통, Java 개발자들은 blocking code를 통해 프로그래밍합니다. 이는 성능에 대한 병목현상이 있기 전 까지는 별 문제가 되지 않을 것이며, 이 지점에서 추가적인 쓰레레드가 고려됩니다. 그러나 자원 활용성의 이러한 확장은 경합과 동싱성 문제들을 빠르게 초래하게 됩니다.  
  
여전히 나쁜 것은, 블록킹 코드가 리소스를 낭비한다는 것입니다. 더욱 자세히 들여다 보면, 프로그램에 D/B에 대한 요청이나 network 호출과 같은 I/O로 인한 동작 지연이 포함 되는 경우, 하나 혹은 그 이상의 쓰레드가 데이터를 기다리기 위해 idle 상태로 전환 되게 되면서 리소스가 낭비 되게 됩니다.  
  
이와 같이 병렬화를 통한 접근은 결코 은탄환이 될 수 없습니다. 이는 하드웨어의 전체 성능에 접근하기 위해 필요하지만, 또한 리소스를 낭비하기 쉬운 복잡한 방법이기도 합니다.  

## Asynchronously to the Rescue?
두번 째로 언급 했던 접근법인 `자원을 더욱 효율적으로 사용할 수 있는 방법을 찾는 것`이 자원 낭비 문제에 대한 해결책이 될 수 있습니다. `비동기/논블록킹`으로 코드를 작성함으로써, 다른 활성화 된 태스크로 동일한 자원을 사용하여 실행을 전환 시키고 비동기적인 처리가 완료 되면 현재 프로세스로 되돌아올 수 있습니다.  
그렇다면 어떻게 `JVM` 상에서 비동기적인 코드를 작성할 수 있을까요? `Java`는 비동기 프로그래밍을 위한 두 가지 모델을 제공하고 있습니다:
- Callbacks: 비동기적인 메서드가 리턴 값을 가지지 않고 대신 `lambda` 혹은 `anonymous class` 형태의 추가적인 `callback` 파라미터를 가집니다. 이 callback은 결과 값이 가용한 경우 호출 됩니다. 이에 대한 잘 알려진 예로 `Swing`의 `EventListener` 계층 구조가 있습니다.  
- Futures: 비동기 메서드가 즉시 `Future<T>`를 리턴합니다. 비동기 프로세스가 T 값을 계산하고, 대신 `Future` 객체가 이를 감싸게 됩니다. 즉시 값이 가용하지 않으면 `Future`객체를 통해 값이 유효할 때 까지 polling 할 수 있습니다. 그 예로, `Future` 오브젝트를 이용한 `Callable<T>` 태스크를 실행하는 `ExecutorService`가 있습니다.
  
이러한 방법들로 충분할까요? 이러한 접근들이 모든 유즈 케이스에 대해 충분한 것은 아니며, 두 방법 모두 제약사항을 가지고 있습니다.  

`Callback`은 함께 조합하기 어려우며, 코드 가독성과 유지보수성을 빠르게 떨어뜨립니다. (콜백 지옥으로 알려진)  

다음 예제를 통해 생각해봅시다: 사용자 UI로부터 상위 다섯개의 즐겨찾기를 표시하고, 존재하지 않는 경우에는 제안 사항을 표시합니다. 이것은 세 개의 서비스(1. 즐겨찾기 ID 제공, 2. 즐겨찾기 상세 내용 제공, 3. 제안 사항에 대한 상세 내용 제공)를 통해 이루어 집니다.

*Example of Callback Hell*
```java
userService.getFavorites(userId, new Callback<List<String>>() {            {1}
  public void onSuccess(List<String> list) {                               {2}
    if (list.isEmpty()) {                                                  {3}
      suggestionService.getSuggestions(new Callback<List<Favorite>>() {
        public void onSuccess(List<Favorite> list) {                       {4}
          UiUtils.submitOnUiThread(() -> {                                 {5}
            list.stream()
                .limit(5)
                .forEach(uiList::show);                                    {6}
            });
        }

        public void onError(Throwable error) {                             {7}
          UiUtils.errorPopup(error);
        }
      });
    } else {
      list.stream()                                                        {8}
          .limit(5)
          .forEach(favId -> favoriteService.getDetails(favId,              {9}
            new Callback<Favorite>() {
              public void onSuccess(Favorite details) {
                UiUtils.submitOnUiThread(() -> uiList.show(details));
              }

              public void onError(Throwable error) {
                UiUtils.errorPopup(error);
              }
            }
          ));
    }
  }

  public void onError(Throwable error) {
    UiUtils.errorPopup(error);
  }
});
```

{1} Callback 기반의 서비스: 비동기 실행이 성공하였을 때에 대한 메서드와 에러 케이스에 대한 메서드를 가지는 `Callback` interfase.  
{2} 첫 번째 서비스가 즐겨찾기 ID리스트로 콜백을 호출합니다.  
{3} 만약 리스트가 비어 있을 경우, `suggestionService`를 호출해야 합니다.  
{4} `suggestionService`는 두 번째 콜백에 `List<Favorite>`를 제공합니다.  
{5} 우리가 `UI`를 다루고 있기 때문에, 데이터를 소비하는 코드가 `UI thread`에서 동작함을 보장해야 합니다.  
{6} 제안 사항의 수를 다섯 개로 제한하기 위해 Java 8 Stream을 사용하고, UI에 가시적인 리스트로 표현합니다.  
{7} 각 단계에서 에러는 같은 방법으로 핸들링합니다: 팝업 띄우기  
{8} 즐겨찾기 ID 단계로 돌아가서, 만약 서비스가 전체 리스트를 리턴한 경우 `Favorite` 객체의 상세 정보를 획득하기 위해 `favoriteService`로 가야 합니다. 우리는 단지 다섯 개의 결과만을 원하기 때문에 우선 리스트의 스트림을 다섯 개로 제한합니다.  
{9} 또, 콜백입니다. 이번에는 UI 쓰레드의 UI에 푸시하기 위한 모든 정보를 가진 `Favorite` 객체를 얻습니다.    
  
상당한 양의 코드입니다. 이러한 코드는 따라가기도 어렵고 반복되는 코드가 존재합니다. `Reactor`를 이용한 동일한 동작의 코드를 봅시다.
*Example of Reactor code equivalent to callback code*
```java
userService.getFavorites(userId)                               {1}
           .flatMap(favoriteService::getDetails)               {2}
           .switchIfEmpty(suggestionService.getSuggestions())  {3}
           .take(5)                                            {4}
           .publishOn(UiUtils.uiThreadScheduler())             {5}
           .subscribe(uiList::show, UiUtils::errorPopup);      {6}
```
{1} 즐겨찾기 ID의 flow로 시작합니다.  
{2} 즐겨찾기 ID를 자세한 `Favorite` 객체로 비동기적인 변환(flatMap)을 시도합니다. 이제 `Favorite`의 flow를 가집니다.  
{3} `Favorite` flow가 비어있는 경우, `suggestionService`를 통한 fallback으로 전환합니다.  
{4} 오직 최대 다섯 개의 결과에만 관심이 있습니다.  
{5} 최종적으로 각각의 데이터 조각이 `UI thread`에서 처리 되기를 원합니다.  
{6} 데이터의 최종형태를 위해 해야하는 것과 에러 케이스일 때 해야 하는것에 대해 정의 함으로써 이 flow를 트리거링합니다.  
  
즐겨찾기 ID가 800ms 미만에서만 검색 되도록 하고, 처리 시간이 초과되는 경우 cache에서 가져오도록 하려면 어떻게 해야할까요? Callback 기반으로는 복잡한 작업이지만, `Reactor`를 이용하면 method chain에 timeout operator를 추가하는 것 처럼 쉽게 할 수 있습니다.

*Example of Reactor code with timeout and fallback*
```java
userService.getFavorites(userId)
           .timeout(Duration.ofMillis(800))                          {1}
           .onErrorResume(cacheService.cachedFavoritesFor(userId))   {2}
           .flatMap(favoriteService::getDetails)                     {3}
           .switchIfEmpty(suggestionService.getSuggestions())
           .take(5)
           .publishOn(UiUtils.uiThreadScheduler())
           .subscribe(uiList::show, UiUtils::errorPopup);
```
{1} 윗 부분(userService.getFavorite())에서 800ms 동안 아무런 아웃풋이 없을 경우 에러를 전파합니다.  
{2} 에러 케이스인 경우, `cacheService`로 fall back 합니다.  
{3} 이후의 method chain은 이전 예제와 유사합니다.  

`Future`는 `Callback`에 비해 꽤 낫지만, `CompletableFuture`에 의해 Java 8에서 개선 되었음에도 불구하고 여전히 잘 구성하기 어렵습니다. 여러 `Future`를 함께 운용하는 것은 가능하지만 쉽지 않습니다. 또한, `Future`는 다른 문제점을 가지고 있습니다. 

