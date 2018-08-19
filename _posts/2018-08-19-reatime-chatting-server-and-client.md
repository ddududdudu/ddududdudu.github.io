---
layout: post
title: "[golang/websocket] Chatting server / client"
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
#### Web Socket이란?
**HTTP**는 기본적으로 _**connectionless**_ 방식으로 동작하는 프로토콜이다. 
즉 하나의 요청에 대한 응답이 발생하면 해당 연결은 종료 되며, 브라우저는 하나의 페이지를 렌더링하기 위한 여러가지 리소스들을 반복해서 요청할 때 마다 
하나 이상의 연결이 다시 생성 되어야 한다.

#### golang에서의 Web Socket Server

#### golang에서의 Web Socket Client
