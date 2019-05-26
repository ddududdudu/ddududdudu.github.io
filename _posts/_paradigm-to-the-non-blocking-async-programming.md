## 왜 비동기 프로그래밍을 해야 하는가?
리소스의 효율적인 사용, 리소스 사용률 극대화

## Asynchronous == Non-blocking?
관점(aspect)의 문제. `Synchronous`/`Asynchronoys`는 호출 하는 쪽(caller)의 관점이고 `Blocking`/`Non-blocking`은 호출 당하는 쪽(callee)의 관점. `Non-blocking`인 API를 호출 할 때는 `Asyncronous`와의 구분이 모호 해질 수 있지만, `Blocking` API를 호출 할 때는 그대로 블록 될 것인가 아닌가에 대한 문제가 생김. `Blocking` API를 호출 하는 경우라도 프로그램의 그대로 제어권을 가지고 flow를 계속하기 위해서는 `Blocking` API를 wrapping해서 별도의 쓰레드로 `Blocking` 코드를 실행하고 제어권은 바로 리턴하도록 해야 함. 이대로 라면 호출 한 코드에서는 제어권을 거의 즉시 돌려 받으므로 블록 되지 않지만, 백그라운드에서는 별도의 쓰레드로 블로킹 코드를 실행하게 할 수 있음. 단지 실행만 비동기적으로 하게 하는 경우는 이대로라도 상관 없겠지만, 비동기적으로 실행 시킨 블로킹 동작의 결과를 사용해야 하거나 상태에 따른 처리를 해야 하는 경우가 대다수임. 이럴 경우 비동기로 실행 시킨 코드를 생성할 때 해당 상태 결과에 대해 접근할 수 있는 일종의 `handler`를 리턴 받고 필요한 시점에 핸들러를 통해 상태/결과를 `물어보던가`, 혹은 각 상태/결과 별 처리해야 하는 행위에 대한 코드를 비동기 코드 블록을 생성하는 시점에 `미리 넘겨주는` 방법이 있음. 

## Java NIO(New IO? Non-blocking IO?)
https://homoefficio.github.io/2016/08/06/Java-NIO%EB%8A%94-%EC%83%9D%EA%B0%81%EB%A7%8C%ED%81%BC-non-blocking-%ED%95%98%EC%A7%80-%EC%95%8A%EB%8B%A4/

## 왜 리액티브 라이브러리를 사용해야 하는가?
### 리액티브 라이브러리가 해줄 수 있다고 하는 것
### 기존 방법과의 비교

## Spring WebFlux 아키텍쳐에서 Reactive-Netty, Reactor, WebFlux 각각의 역할은?
### Reactor
### Reacive-Netty
### WebFlux



- WebFlux handler function에서 리턴하는 Mono<HttpResponse>는 결국 어디서 처리 되는가?
- Thread의 경우 OS 스케쥴러에 의해 context-change가 일어나며 전환 된다면, async 모델에서 각각의 reactor method chain간의 context chain은 어떻게 이루어 지는가?
  즉, event-loop에 의해 여러 이벤트를 처리하고, 하나의 이벤트에 I/O로 인한 blocking이 발생하는 경우 어떻게 event-loop이 그것을 catch하고 저장하고 다른 이벤트 처리로 전환하고 다시 복귀하는가?
- Reactor를 사용하는 것이 어떻게 async 한 처리와 연관 되는가?


