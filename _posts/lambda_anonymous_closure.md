---
layout: post
title: "Lambda, Anonymous function, Closure"
description: "람다 표현식, 익명 함수, 클로저에 대한 정리"
categories: blog
tags: [lambda, anonymous function, closure, first class function]
image:
feature:
date: 2019-02-20T21:31:50-04:00
---

### First class object
프로그래밍 언어에서 `일급 객체(first-class object)`란 무엇일까? [정의](https://ko.wikipedia.org/wiki/%EC%9D%BC%EA%B8%89_%EA%B0%9D%EC%B2%B4)는 아래와 같다.
> 특정 언어의 일급 객체 (first-class citizens, 일급 값, 일급 엔티티, 혹은 일급 시민)이라 함은 컴퓨터 프로그래밍 언어 디자인에서 일반적으로 다른 객체들에 적용 가능한 연산을 모두 지원하는 객체를 가리킨다. 함수에 매개변수로 넘기기, 변수에 대입하기와 같은 연산들이 여기서 말하는 일반적인 연산의 예에 해당한다. *- Wikipedia*

`일급 객체`로서의 조건을 좀 더 상세히 기술하면 아래와 같다.
1. 변수나 데이터 구조안에 담을 수 있음.
2. 파라미터로 전달할 수 있음.
3. 반환값으로 사용할 수 있음.
4. 할당에 사용된 이름과 관계없이 고유한 구별이 가능함.
5. 동적으로 프로퍼티 할당이 가능함.

`C`, `Go`, `Java`를 통해서 구체적으로 살펴보자.
#### 1. 변수나 데이터 구조안에 담을 수 있음 : `C`의 `array`는 `일급 객체`일까?
`C`에서 `array`가 `일급 객체`이기 위해서는 변수로 전달이 가능해야 한다. 아래와 같이 예제를 작성해서 확인 해보자.
```c     
#include <stdio.h>

int main() {
    int array[10];
    int sizeOfArray = getSizeOfArray(array);
     
    printf("Size of array is %d\n", sizeofArray);
}

int getSizeOfArray(int[] array) {
    return sizeof(array);
}

[Output] : 
```
우선, 결과는 ~~당연하게도~~ `80`이 아닌 `8`이 출력된다. `getSizeOfArray()` 함수의 시그니처에서 확인할 수 있듯 이 함수는 

(컴파일러 설정에 따라 다르겠지만) 위 코드를 컴파일 하면 아래와 같은 메시지를 확인할 수 있다.  
> `warning: 'sizeof' on array function parameter 'array' will return size of 'int *' [-Wsizeof-array-argument] return sizeof(array);`
  > array의 이름은 array의 첫 번째 요소를 가리키는 포인터이다. array의 이름만으로는 배열의 크기와 같은 정보를 알 수 없기 때문에 배열 자체를 대입한다고 보기 어려우므로 일급 객체가 아니다. 
