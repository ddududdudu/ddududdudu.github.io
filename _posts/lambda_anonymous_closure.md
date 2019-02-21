---
layout: post
title: "First-class object, anonymous function, clousre, lambda-expression"
description: "First-class object, 익명 함수, 클로저, lambda-expression에 대한 정리"
categories: blog
tags: [first-class object, anonymous function, closure, lambda-expression.]
image:
feature:
date: 2019-02-20T21:31:50-04:00
---

뭔가 서로 연관이 있어 보이면서도 명확히 정리 해보지 못했던 것들에 대해 정리해본다.

### First class object
프로그래밍 언어에서 `일급 객체(first-class object)`란 무엇일까? [정의](https://ko.wikipedia.org/wiki/%EC%9D%BC%EA%B8%89_%EA%B0%9D%EC%B2%B4)는 아래와 같다.
> 특정 언어의 일급 객체 (first-class citizens, 일급 값, 일급 엔티티, 혹은 일급 시민)이라 함은 컴퓨터 프로그래밍 언어 디자인에서 일반적으로 다른 객체들에 적용 가능한 연산을 모두 지원하는 객체를 가리킨다. 함수에 매개변수로 넘기기, 변수에 대입하기와 같은 연산들이 여기서 말하는 일반적인 연산의 예에 해당한다. *- Wikipedia*
  
`Object` 라는 개념에 대한 언어별 해석이 다를 수 있기 때문에 `First-class citizen` 과 같이 좀 더 추상화 시킨 표현도 사용하지만, 이 포스팅에서는 `object`를 유지하겠다.  
  
`일급 객체`로서의 조건을 좀 더 상세히 기술하면 아래와 같다.
1. 변수나 데이터 구조안에 담을 수 있음.
2. 파라미터로 전달할 수 있음.
3. 반환값으로 사용할 수 있음.
4. 할당에 사용된 이름과 관계없이 고유한 구별이 가능함.
5. 동적으로 프로퍼티 할당이 가능함.

예를 들어, `C`의 `array`는 `일급 객체`일까? 아래와 같이 예제를 작성해서 확인 해보자.
```c     
#include <stdio.h>

int getSizeOfArray(int array[]) {
    return sizeof(array);
}

int getSizeOfFixedArray(int array[10]) {
    return sizeof(array);
}

int main() {
    int array[10];

    printf("Size of array is %d\n", sizeof(array));
    printf("Size of calculated array is %d\n", getSizeOfArray(array));
    printf("Size of calculated fixed array is %d\n", getSizeOfFixedArray(array));
}

// Size of array is 40
// Size of calculated array is 8
// Size of calculated fixed array is 8
```
`C`에서의 `pointer`와 `array`의 관계에 대해서는 이 [포스팅]()을 참고하자.  
우선, `getSizeOfArray()` 함수의 시그니처에서 확인할 수 있듯 이 함수는 `int[]` 타입의 파라미터를 받고 있다. 그렇다면 결과로 `40`이 나와야 할 것 같지만(4 bytes * 10개) ~~당연하게도~~ 포인터의 크기인 `8`이 출력된다. 이에 더해서, (컴파일러 설정에 따라 다르겠지만) 위 코드를 컴파일 하면 아래와 같은 경고 메시지를 확인할 수 있다.  
> warning: 'sizeof' on array function parameter 'array' will return size of 'int *' [-Wsizeof-array-argument] return sizeof(array);

`array의 이름`은 `array의 첫 번째 요소를 가리키는 포인터`이기 때문이다. `sizeof` operator는 단지 compile-time에 결정 가능한 값을 출력할 뿐이므로 해당 `array`가 선언 된 code block에서는 예상한 값을 얻을 수 있는 반면, 단순히 포인터가 전달 되는 매개 변수로서의 배열은 알 방법이 없다. 이는 아예 고정 크기 배열로서의 파라미터를 표현 하고자 한 `getSizeOfFixedArray()` 함수일지라도 마찬가지이다.  
따라서 `C`에서 `array`는 `array` 자체로서 파라미터 전달이 불가능하므로 `일급 객체`가 아님을 알 수 있다.  

### Function as a first-class object
`일급 객체`로서의 `함수`란 무엇일까? 아래 `Go` 예제를 통해 확인해보자.
```go
package main

import (
    "fmt"
)

type CalcType string
const (
    add CalcType = "add"
    sub CalcType = "sub"
)

func main() {
    a := 10
    b := 5
    
    calcFunc := CalcFunction(add)
    fmt.Printf("add : %d\n", calcFunc(a, b))
    
    calcFunc = CalcFunction(sub)
    fmt.Printf("sub : %d\n", calcFunc(a, b))
}

func CalcFunction(calcType CalcType) func(int, int) int {
    switch calcType {
    case add:
        return AddFunc
    case sub:
        return SubFunc
    default:
        fmt.Println("unsupported calculation type: " + calcType)
        return nil
    }
}

func AddFunc(a, b int) int {
    return a+b
}

func SubFunc(a, b int) int {
    return a-b
}

// add : 15
// sub : 5
```
`CalcFunction()`은 전달 받은 인수에 따라 `func(int, int) int` 라는 시그니처를 가지는 함수를 리턴하고 있다. `AddFunc()` 이든 `SubFunc()` 이든 시그니처가 일치 하므로 리턴 된 함수는 변수 `calcFunc`에 할당 되고 이후 `calcFunc()` 호출을 통해 사용할 수 있다. 즉 함수를 변수에 대입하고, 인자로서 전달하고, 함수의 리턴 값으로 반환 할 수 있으므로 `Go` 의 `함수`는 `일급 객체`이다.  
`Java`의 경우는 어떨까? `Java`의 세계에서는 `primitive type`을 제외한 모든 것들은 `Object`로 표현된다. `Function`이라는 개념도 `Java`에서는 특정 `object`를 추상화하는 `class`의 `method`로서만 존재할 수 있을 뿐이다. 즉, 함수라는 형태의 코드 블록 자체만을 다루는 것을 언어적으로 지원하지 않는다.  
하지만 `C`의 경우는 조금 고민이 필요할 것 같다. `C`에서는 `function`을 가리키는 `pointer`인 `function pointer`를 이용하면 동일하게 함수를 대입/전달/반환 할 수 있다. 그럼 `C`에서도 `function`은 `일급 객체`인걸까? 

### First-class function and anonymous function
`First-class function`이란 말 그대로, `First-class object` 로서의 `function`을 의미한다. 다섯 가지 `first-class object`의 조건에 더해 아래 두 가지 조건을 추가로 충족해야 한다.
1. 런타임 생성이 가능해야 함.
2. 익명으로 생성 가능해야 함.
위에서 사용한 `Go`예제를 조금 바꾸어 보자.
```go
package main

import (
    "fmt"
)

type CalcType string
const (
    add CalcType = "add"
    sub CalcType = "sub"
)

func main() {
    a := 10
    b := 5
    
    calcFunc := CalcFunction(add)
    fmt.Printf("add : %d\n", calcFunc(a, b))
    
    calcFunc = CalcFunction(sub)
    fmt.Printf("sub : %d\n", calcFunc(a, b))
}

func CalcFunction(calcType CalcType) func(int, int) int {
    switch calcType {
    case add:
        return func(a, b int) int {
            return a+b
        }
    case sub:
        return func(a, b int) int {
            return a-b
        }
    default:
        fmt.Println("unsupported calculation type: " + calcType)
        return nil
    }
}

// add : 15
// sub : 5
```
`First-class function` 설명에서 사용한 예제와 동일하지만, 명시적으로 named function이었던 AddFunc()와 SubFunc()을 정의 하는 대신, 동일한 시그니처와 구현부를 가진 코드 블럭을 리턴하고 있다. 



따라서 `C`에서 `array`는 `array` 자체로서 파라미터 전달이 불가능하므로 `일급 객체`가 아님을 알 수 있다.  가지 
따라서 `C`에서 `array`는 `array` 자체로서 파라미터 전달이 불가능하므로 `일급 객체`가 아님을 알 수 있다.

### Anonymous function


