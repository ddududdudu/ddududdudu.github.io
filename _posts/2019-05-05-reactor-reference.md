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

# 2. Getting Started
이 섹션은 Reactor를 활용하기 위한 도움을 주는 정보들을 포함하고 있습니다. 이 정보들은 아래 내용을 포함합니다.
- Introducing Reactor
- Prerequisites
- Understanding the BOM
- Getting Reactor

## 2.1 Introducing Reactor
`Reactor`는 효율적인 요구 관리(demand management, `배압(backpressure)`의 형태로 관리되는)를 통해 `JVM`에 충분한 `non-blocking reactive programming` 토대를 제공합니다.
`Reactor`는 `CompletableFuture`, `Stream` 그리고 `Duration`으로 대표되는 Java 8 functional API들을 직접적으로 통합하고 있습니다.
`Raactor`는 조합 가능한 비동기 sequence API들인 `Flux`(N개 이상의 요소들)와 `Mono`(0 혹은 한 개의 요소)를 제공하며, 넓은 의미로 [`Reactive Streams`](https://www.reactive-streams.org/) 스펙을 구현하고 있습니다.  
`Reactor`는 또한 [`reactor-netty`](https://github.com/reactor/reactor-netty) 프로젝트를 통해 프로세스간 non-blocking 통신을 지원하고 있습니다. `Micro-Service Architecture`에 적합하며, `Reactor-Netty`는 WebSocket을 포함한 HTTP, TCP 그리고 UDP 통신을 위한 backpressure-ready 엔진을 제공합니다. Reactive encoding과 decoding 또한 충분히 지원합니다.
> `Reactive encoding/decoding` : Reactive Streams back-pressure를 통한 non-blocking I/O로 byte level contents와 상위 레벨의 object간의 직렬화/역직렬화를 지원하는 것을 말하는 것 같습니다.

## 2.2 Prerequisites
`Reactor Core`는 Java 8 이상에서 동작합니다.  
또한 `org.reactivestreams:reactive-streams:1.0.2`에 이행적인 의존성을 가집니다.

> 안드로이드 지원:
> - `Reactor 3`는 공식적으로 안드로이드를 지원하지 않습니다. RxJava 2를 사용하는 것을 고려 해보세요. 
> - 하지만 안드로이드 SDK 26(Android O)와 그 이상 버전에서의 동작에는 문제가 없습니다.
> - 우리는 안드로이드 지원이라는 잇점에 대한 변화를 고려하는 것에 열려 있지만, 그것을 보장할 수는 없습니다. 각각의 경우를 고려하여 결정 되어야 합니다.

## 2.3 Understanding the BOM
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

## 2.4 Getting Reactor
초기에 언급 했던 것 처럼, `Reactor`를 사용하는 가장 쉬운 방법은 `BOM`을 사용하고 프로젝트에 상응하는 의존성을 추가하는 것입니다. 그러한 의존성을 추가할 때, 반드시 버전을 생략함으로써 BOM으로부터 버전을 획득할 수 있도록 해야 함을 주의합니다.  
만약 아티팩트의 특정 버전을 강제하고 싶은 경우에는 버전명을 설정할 수 있숩니다. 또한 `BOM` 사용을 포기하고 전체 의존성에 아티팩트 버전을 명시할 수도 있습니다.

### 2.4.1 Maven Installation
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

### 2.4.2 Gradle Installation
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

### 2.4.3 Milestones and Snapshots
마일스톤과 개발자 프리뷰들은 `Maven Central` 보다는 `Spring Milestones` repository를 통해 배포 됩니다. 빌드 설정 파일에 추가 하기 위해, 아래 코드 조각을 사용하세요.
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
```gradle
repositories {
  maven { url 'https://repo.spring.io/milestone' }
  mavenCentral()
}
```
이와 유사하게, 별도의 지정된 레파지토리에 존재하는 스냅샷의 경우 아래와 같이 적용할 수 있습니다.
```xml
<repositories>
	<repository>
		<id>spring-snapshots</id>
		<name>Spring Snapshot Repository</name>
		<url>https://repo.spring.io/snapshot</url>
	</repository>
</repositories>
```

```gradle
repositories {
  maven { url 'https://repo.spring.io/snapshot' }
  mavenCentral()
}
```

# 3. Introduction to Reactive Programming
`Reactor`는 `Reactive Programming` 패러다임의 구현체이며, `reactive programming`은 아래와 같이 요약할 수 있습니다.
> Reactive programming은 데이터 스트림과 변화의 전파와 관련된 비동기 프로그래밍(asynchrounous programming) 패러다임입니다. 이는 채택한 프로그래밍 언어를 이용해 정적이거나(ex. 배열) 동적인(ex. event emitter) 데이터 스트림을 쉽게 표현할 수 있음을 의미합니다.  
> — https://en.wikipedia.org/wiki/Reactive_programming

`Reactive programming`을 향한 첫 걸음으로써, `Microsoft`는 `.NET` 생태계에 `Reactive Extensions`(Rx) 라이브러리를 만들었습니다. 그리고 `RxJava`는 `JVM` 상의 `reactive programming`을 위해 구현 되었습니다. 시간이 흐르면서, `JVM` 상에서 `reacive library`에 대한 인터페이스와 상호작용에 대한 일련의 규격을 정의하는 `Reactive Streams`라는 노력을 통해 자바 표준화로 나타나게 되었습니다. 이 인터페이스는 Java 9의 `Flow` 클래스 아래로 통합 되었습니다.  
  
`Reactive Programming` 패러다임은 종종 객체 지향 언어에서 `Observer deisgn pattern`의 확장으로서 표현되곤 합니다. 이러한 모든 라이브러리의 `Iterable-Iterator` 쌍에 대한 이중성이 있으므로, 친숙한 `Iterator pattern`과 `main Reactive Pattern`을 비교할 수 있습니다. 이들 간에 가장 주요한 차이점은, `Iterator`는 `pull-based`로 동작하는 반면 `Reactive Streams`는 `push-based`로 동작한다는 점입니다.  
  
`Iterator`를 사용하는 것은 값에 접근하는 방법이 전적으로 `Iterable`에게 달려있음에도 불구하고 명령형(imperative) 프로그래밍 패턴입니다. 순서적으로 
언제 `next()` 아이템에 접근할지를 결정하는 것은 전적으로 개발제의 몫입니다. `Reactive Streams`에서는 `Iteratable-Iterator` 쌍에 대응하는 `Publisher-Subscriber` 쌍이 존재합니다. 그러나 `Subscriber`에게 새로운 값이 발생했을 때 그것이 가용함을 알리는 것은 `Publisher`이며, 이러한 `push` 관점이 `reactive` 하기 위한 열쇠입니다. 또한 푸시 되는 값들에 대해 적용된 동작들이 명령형 프로그래밍에 비해 명시적으로 표현됩니다. 프로그래머는 정확한 제어 흐름을 나타내는 대신 계산 논리를 표현할 수 있습니다. 







