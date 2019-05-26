## 정의
- Signature  
  `public static <T> Mono<T> defer(Supplier<? extends Mono<? extends T>> supplier)`  
  `Supplier`를 인자로 받고 `Mono`를 리턴한다.
- RxMarble  
![](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/doc-files/marbles/deferForMono.svg)

##
1. 실제 값 생성 시점을 구독 시점 이후로 최대한 늦추고 싶을 때
https://softwaree.tistory.com/29

2. Subscriber의 구독 시점에 따라 값 생성을 최신화 하고 싶을 때
https://nittaku.tistory.com/203
