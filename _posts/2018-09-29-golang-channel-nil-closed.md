---
layout: post
title: "[golang] nil / closed channel"
description: "nil channel 혹은 closed channel"
categories: blog
tags: [golang]
image:
feature:
date: 2018-09-29T15:57:50-04:00
---

### Channel
`channel`은 data를 주고 받을 수 있는 통로로, `pipe`와 유사하다.   
`make()` 내장 함수를 통해 생성할 수 있고, `<-` 연산자를 통해 data를 send/receive 할 수 있다. `channel` 생성 시에는 channel을 통해 주고 받을 data의 type을 지정해야 한다.  
golang 에서는 변수가 생성 되고 초기화를 하지 않으면 각 타입에 대한 `zero-value`를 가지게 되며, `channel`의 경우는 `channel`에 지정 된 data type과는 무관하게 `nil` 값을 가진다. 
```go
//int type의 data를 주고 받을 channel을 정의.
var ch chan int

//make()를 통해 channel 초기화.
ch = make(chan int)

//channel에 1을 send.
ch <- 1 

//channel로 부터 값을 receive.
var receiver int
receiver <- ch 
```

그리고 사용이 끝난 `channel`에 대해 `close`를 수행 할 수 있다.  
이 경우 해당 `channel`에 receive 하고 있는 모든 `go routine`에게 두 번째 파라미터(`bool`)를 통해 channel이 closed 되었음을 알릴 수 있다.
```go
//deadlock을 피하기 위해 buffered channel 생성
ch := make(chan int, 1)
ch <- 1
close(ch)

//receive 1, true
receiver, ok := <-ch 

//receive 0, false
receiver, ok = <-ch 
if !ok {
    //false가 리턴 되는 경우 해당 channel은 close 되었음.
}
```
위와 같이 channel을 생성하고 data를 send/receive 할 수 있으며, 최종적으로는 close하여 모든 receiver들에게 더 이상 data 전송이 없음을 알릴 수 있다.

### nil / closed channel에 대한 동작
channel이 가질 수 있는 상태는 아래 세 가지이다.
```go
//nil
var ch int

//initialized
ch := make(chan int)

//closed
close(ch)
```

우리가 생성 된 channel에 대해 수행할 수 있는 동작은 아래와 같다.
> send  
> receive  
> close

초기화 된 channel에 대해 각 동작을 수행할 경우 의도한 대로 동작하겠지만, 만약 나머지 두 가지 상태인 nil channel과 closed channel에 대해 수행한다면 어떻게 동작할까?  
각 동작에 대한 결과를 정리해보면 아래와 같다.

|state |action |behaviour  |
|------|-------|-----------|
|nil   |send   | block     |
|nil   |receive| block     |
|nil   |close  | panic     |
|closed|send   | panic     |
|closed|receive| zero-value|
|closed|close  | panic     |

`channel` 사용 시 `:=` 연산자를 통해 정의와 초기화를 함께 수행하는 경우가 대부분이므로 closed `channel`에 대한 접근에 유의하면 좋을 것 같다.
