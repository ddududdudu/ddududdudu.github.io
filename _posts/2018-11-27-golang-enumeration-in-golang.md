---
layout: post
title: "[golang] Enumeration
description: Golang에서 Enumeration과 유사한 형태를 흉내내어 보기.
categories: blog
tags: [golang]
image:
feature:
date: 2018-11-27T21:31:50-04:00
---

`Enumeation`은 결국 특정 type에 대한 값을 지정한 이름으로 사용하겠다는 약속이다.  
이러한 약속을 통해 코드를 좀 더 명확하고 구조화할 수 있다.  
`Golang` 에서는 `enum` type이 존재하지 않지만, 아래와 같이 라벨링을 해서 유사한 형태를 가져볼 수 있다.
  
```go
type EnumValue string

const (
    value1 EnumValue = "value1"
    value2 EnumValue = "value2"
)

func main() {
    printEnum(value1)
    printEnum(value2)
}

func printEnum(val EnumValue) {
    fmt.Println(val)
}
```
[위 코드](https://play.golang.org/p/6svjxGe6SSY)에서 처럼, `type` 키워드를 이용해 라벨링할 타입을 생성한다.  
만약 `printEnum` function에 Eumeration 형태로 사용하고자 지정한`EnumValue`가 아닌 다른 값이 들어올 경우 컴파일 에러가 발생한다.
당연하게도 `type`이 다르기 때문이다. 마찬가지로 명시적인 타입의 정의 없이 값을 할당하여 변수를 생성하는 경우 타입 추론을 통해 타입이 정해지므로 이 경우에도 컴파일 에러가 발생한다.
```go
func main() {
    var value3 string = "value3"
    printEnum(value3) //Compile error : type of 'value3' is string.
    
    value4 := "value4"
    printEnum(value4) //Compile error : type of 'value4' is string.
}

func printEnum(val EnumValue) {
    fmt.Println(val)
}
```
