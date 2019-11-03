---
layout: post
title: "[spring] Major concepts"
description: "Spring을 접하면서 마주치는 주요 개념들에 대한 정리"
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

표준이기 때문에, 이러한 조건들(convention)을 만족 한다면 더 고민 할 필요 없이 `Java Bean`이라고 이해하면 된다. 그렇다면 이러한 표준은 어떤 필요에 의해 정의 되었고, 어떻게 활용 되고 있을까?  
위의 `Java Bean`이 되기 위한 조건에서 알 수 있듯, 이 조건을 만족하는 class는 단순히 값을 가지는 일종의 구조체와 다를 게 없다. 여러 타입의 값을 포함하는 일종의 `software component`로서 정의 되어 이러한 data를 전달하고 가공하고 해석하는 것에 (미리 정의 된) 공통적인 방법(convention)으로 접근할 수 있게 된다. Data를 다루는 스펙을 따름으로서 여러 가지 툴에 의해 `Java Bean`을 해석하고 정보를 제공할 수도 있게 된다.

#### EJB(Enterprise Java Bean)


#### POJO

#### Spring bean Vs EJB

### Servlet

### Container
