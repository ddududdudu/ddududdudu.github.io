- WebFlux handler function에서 리턴하는 Mono<HttpResponse>는 결국 어디서 처리 되는가?
- Thread의 경우 OS 스케쥴러에 의해 context-change가 일어나며 전환 된다면, async 모델에서 각각의 reactor method chain간의 context chain은 어떻게 이루어 지는가?
  즉, event-loop에 의해 여러 이벤트를 처리하고, 하나의 이벤트에 I/O로 인한 blocking이 발생하는 경우 어떻게 event-loop이 그것을 catch하고 저장하고 다른 이벤트 처리로 전환하고 다시 복귀하는가?
- Reactor를 사용하는 것이 어떻게 async 한 처리와 연관 되는가?
