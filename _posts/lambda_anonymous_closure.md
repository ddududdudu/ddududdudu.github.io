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
- 변수나 데이터 구조안에 담을 수 있음.
  > `C의 array`는 일급
     ```c
     
     ```
     array의 이름은 array의 첫 번째 요소를 가리키는 포인터이다. array의 이름만으로는 배열의 크기와 같은 정보를 알 수 없기 때문에 배열 자체를 대입한다고 보기 어려우므로 일급 객체가 아니다. 
- 파라미터로 전달할 수 있음.
- 반환값으로 사용할 수 있음.
- 할당에 사용된 이름과 관계없이 고유한 구별이 가능함.
- 동적으로 프로퍼티 할당이 가능함.
