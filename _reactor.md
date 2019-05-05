> 이 문서는 `Project Reactor`의 [공식 참고 문서](https://projectreactor.io/docs/core/release/reference/)를 학습 목적으로 번역한 것입니다. 

# 2. Getting Started
이 섹션은 Reactor를 활용하기 위한 도움을 주는 정보들을 포함하고 있습니다. 이 정보들은 아래 내용을 포함합니다.
- Introducing Reactor
- Prerequisites
- Understanding the BOM
- Getting Reactor

## 2.1 Introduing Reactor
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
코드네임은 전통적인 MAJOR.MINOR 번호로 표현됩니다. 그들은 알파벳순으로 증가하는 
