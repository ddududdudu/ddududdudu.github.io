---
layout: post
title: "[golang] Ways of initializing map"
description: Map을 초기화 하는 방법들.
categories: blog
tags: [golang, go, map]
image:
feature:
date: 2018-12-31T16:08:50-04:00
---

`golang`에서 `map`을 초기화 하는 방법에는 크게 아래 두 가지 방법이 있다. 각 방법에 따른 동작의 차이점이나 장단점 같은 것은 없다. 단지 목적에 따라 필요한 방법을 사용하면 된다.
### `make`를 사용하는 초기화
```go
testMap := make(map[string]string)
```
`make`를 사용해 초기화 하는 방법이다. 결과적으로는 `emtpy map`이 생성된다. (앞서 말했듯 차이점이 없다)  
다만 `make`를 사용할 경우, 아래와 같이 두 번째 인자를 통해 `map`의 `initial entry capacity`를 정할 수 있다.  
```go
testMap := make(map[string]string, 10)
```
이것은 단지 `default entry capacity`를 오버라이딩 할 뿐이므로, 이후 capacity를 넘어서게 된다고 해서 `map`에 아이템을 추가할 수 없다는 말이 아니다.  
즉 `상한(upper limitation)`이 아니며, 현재 capacity를 넘어서는 경우 재 할당이 일어나면서 추가할 수 있다.  
`initial entry capacity`를 지정하는 경우의 장점은 무엇일까? `map`은 그 크기에 제한이 없으며, 내부적으로 `map`의 아이템을 처리하기 위한 `hash table` 형태의 자료구조를 가지고 있다.  
동작 중에 아이템의 추가/삭제 등의 변경이 있을 경우 `map`은 `go runtime`에 의해 재 할당을 하게 되는데, 만약 `map`에 의해 다루어지는 아이템의 크기가 정해져 있거나, 상한을 알 수 있다면 미리 지정함으로서 runtime에 빈번한 재할당이 일어나는 것을 방지할 수 있다.
> 하지만 현실적으로 런타임에 발생하는 재할당이 큰 부담이 될지는 모르겠지만 한 번 측정 해봐야겠다.
### `map literal`을 사용하는 초기화.
```go
testMap := map[string]string{}
```
`map literal`을 이용해 `empty map`을 바로 선언하여 사용할 수 있다. (물론 아래와 같이 선언한 `map`은 `nil map`이므로 panic이 발생한다)
```go
var testMap map[string]string
testMap["test"] = "test" //panic
```
이 방법으로 `map`을 초기화 하는 경우 아래와 같이 초기 값(아이템)을 함께 선언할 수 있다.
```go
testMap := map[string]string{
    "key1": "value1",
    "key2": "value2",
}
```
미리 정해진 아이템을 다루는 `map`을 사용한다면 위 방법으로 초기화 함으로써 `map`과 그 값들의 정의를 한 번에 처리할 수 있어 코드의 가독성을 높일 수 있다.
