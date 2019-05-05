> 이 문서는 `Project Reactor`의 [공식 참고 문서](https://projectreactor.io/docs/core/release/reference/)를 학습 목적으로 번역한 것입니다. 

# 2. 시작하기.
이 섹션은 Reactor를 활용하기 위한 도움을 주는 정보들을 포함하고 있습니다. 이 정보들은 아래 내용을 포함합니다.
- Introducing Reactor
- Prerequisites
- Understanding the BOM
- Getting Reactor

## 2.1 Introduing Reactor
`Reactor`는 효율적인 요구 관리(demand management, `배압(backpressure)의 형태로 관리되는)를 통해 `JVM`에 충분한 `non-blocking reactive programming` 토대를 제공합니다.
`Reactor`는 `CompletableFuture`, `Stream` 그리고 `Duration`으로 대표되는 Java 8 functional API들을 직접적으로 통합하고 있습니다.
`Raactor`는 조합 가능한 비동기 sequence API들인 `Flux`(N개 이상의 요소들)와 `Mono`(0 혹은 한 개의 요소)를 제공하며, 넓은 의미로 [`Reactive Streams`](https://www.reactive-streams.org/) 스펙을 구현하고 있습니다.
