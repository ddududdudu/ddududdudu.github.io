---
layout: post
title: "[spring] Major concepts"
description: "Spring을 처음 접하면서 마주치는 주요 개념들에 대한 정리"
categories: blog
tags: [spring, concepts]
image:
feature:
date: 2019-11-03T21:31:50-04:00
---

### Beans
#### Java Bean
정확히 `Java Bean`이란 무엇일까? `Java Bean`은 [표준](https://www.oracle.com/technetwork/java/javase/documentation/spec-136004.html)이다. 즉, 아래의 특성을 만족하는 `class`는 `Java Bean`이라고 부를 수 있다.
- 모든 property는 `private` 접근자로 정의 되어야 한다.
- property 들에 대해 `getter/setter` 메서드가 정외 되어야 한다.
- `public` 접근자의 `NoArgsConstructor`가 정의 되어야 한다.
- `Serializable` interface를 구현해야 한다.

표준이기 때문에, 이러한 조건들(convention)을 만족 한다면 더 고민 할 필요 없이 `Java Bean`이라고 이해하면 된다.  
위의 `Java Bean`이 되기 위한 조건에서 알 수 있듯, 이 조건을 만족하는 class는 단순히 값을 가지는 일종의 구조체와 다를 게 없다. 여러 타입의 값을 포함하는 일종의 `software component`로서 정의 되어 이러한 data를 전달하고 가공하고 해석하는 것에 스펙에 따른 공통적인 방법(convention)으로 접근하고 재사용 할 수 있게 된다. Data를 다루는 스펙을 따름으로서 여러 가지 툴에 의해 `Java Bean`을 해석하고 정보를 제공할 수도 있게 된다.  
즉, `Java Bean`이란 특정 스펙을 따르면서 여러 타입의 오브젝트를 포함하고 있는 Java 클래스이다.

#### EJB(Enterprise Java Beans)
`EJB`는 Java platform 상에서 큰 규모의 분산 시스템을 개발하기 위해 Sun Microsystems에서 정의한 스펙이다. Enterprise급 개발에서 처리해야 하는 공통적인 부분들(security, transaction, distributed computing, multi-threading, resource pooling 등)을 제공하고 비즈니스 로직 자체에만 집중할 수 있게 해준다. `EJB`는 크게 아래의 세 가지 요소로 구성 된다.
- EJB
- EJB container
- Java Apllication Server  

`EJB`는 `EJB container`내에서 실행 되며, `EJB container`는 `Java Application Server`내에서 실행 된다.  
`EJB` 자체는 두 가지 타입의 `bean`으로 구분 되는데, 하나는 `session bean`이고 또 하나는 `message driven bean`이다.  
결과적으로 `EJB`는 현재 개발 트렌드에서는 거의 사장 된 개념으로 그 이유를 찾아보면 대부분은 [over-engineering](https://limmmee.tistory.com/8?category=654011)이라고 꼽고있다. `EJB`에서 제공하는 편의성이 상당하지만 개발 목적에 따라 필요하지 않은 경우가 있는 경우에도 그 기능을 사용해야만 한다든지, `EJB`를 지원하는 application server가 필수 이므로 비용의 문제가 존재 한다든지, container 라는 제약 사항으로 인해 개발 과정에서 불필요한 시간이 추가 된다든지 하는 문제점이 있었다고 한다.  
하지만 가장 큰 문제점은 `EJB` 표준을 따르기 위해 class를 설계 하는 순간 상속이나 다형성 같은 OOP의 장점을 포기해야 한다는 것이다.  
결국 이러한 복잡도와 낮은 생산성, 그리고 느린 성능 까지 여러 문제점이 제기 되면서 결국 `EJB`가 없으면서도 이러한 이점을 살릴 수 있는 개발 방법론을 원하게 되는 계기가 되었다.

#### POJO(Plain Old Java Object)
어떠한 스펙을 따르지 않는 단순하고 평범한 Java Object이다.

#### Spring Beans

### WAS(Web Application Server)

#### Web Server와 Web Application Server

#### Servlet

#### Container
