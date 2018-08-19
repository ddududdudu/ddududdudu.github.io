---
layout: post
title: "[golang] Chatting server / client"
description: "golang을 이용해 실시간 채팅 server/client 구현."
categories: blog
tags: [golang, websocket]
image:
feature:
date: 2018-08-19T14:37:50-04:00
---

### Goal
**Golang**을 활용하여 서버와 클라이언트간의 실시간 통신을 학습하기 위한 채팅 서버/클라이언트를 개발한다.

### Web Socket
#### HTTP 요청의 한계
일반적인 **HTTP** 서버와 클라이언트의 동작은 요청/응답 기반으로 이루어지며, **HTTP**는 기본적으로 _**connectionless**_ 방식으로 동작하는 프로토콜이다. 즉 하나의 요청에 대한 응답이 발생하면 해당 연결은 종료 되며, 브라우저는 하나의 페이지를 렌더링하기 위한 여러가지 리소스들을 반복해서 요청할 때 마다 하나 이상의 연결이 다시 생성 되어야 한다.  

사용자의 명시적인 요청(refresh) 없이도 업데이트 된 페이지를 표시하고, 전체 리소스가 아닌 업데이트가 필요한 부분만을 요청함으로써 성능의 개선을 가져온 것이 AJAX이다. 요청을 위해 javascript를 사용하지만, 매 요청 마다 새로운 connection이 생성 되는 것은 마찬가지이다. Server의 입장에서는 명시적인 요청이 없다면 클라이언트가 갱신된 정보를 필요로 한다는 사실을 알 수 없기 때문에, 어쨌든 명시적인 요청이 있어야 응답을 받을 수 있다는 한계는 동일하다. 일정 시간을 주기로 client가 반복적인 요청을 함으로써 갱신된 내용을 확인하는 방법도 있겠지만 효율적인 구조는 아니다.

실시간 채팅 서버를 위해서는 사용자의 요청 없이도 갱신된 내용을 확인 가능해야 하므로 이러한 구조적 제약을 해결할 필요가 있다.

#### Web Socket

#### golang의 WebSocket package
golang의 장점인 안정적이고 강력한 standard library를 활용하기 위해 godoc에서 WebSocket으로 검색하면 아래 패키지를 찾을 수 있다.  
[https://godoc.org/golang.org/x/net/websocket](https://godoc.org/golang.org/x/net/websocket) 

하지만 description에 아래와 같은 내용을 찾아볼 수 있다.

> This package currently lacks some features found in an alternative and more actively maintained WebSocket package: [**https://godoc.org/github.com/gorilla/websocket**](https://godoc.org/github.com/gorilla/websocket)

즉, 더 잘 관리되고 더 많은 기능을 가진 WebSocket 패키지가 저기 있다는 말이다.

#### Web Socket Server

#### Web Socket Client
