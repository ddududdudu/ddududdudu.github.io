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
일반적인 **HTTP** 서버와 클라이언트의 동작은 요청/응답 기반으로 이루어지며, **HTTP**는 기본적으로 _**stateless**_ 하게 동작한다. 각각의 요청과 응답은 다른 요청과 응답에 대해 독립적으로 동작하고, 따라서 각각의 connection은 관리될 필요가 없다. 즉 하나의 요청에 대한 응답이 발생하면 전통적인 `short-lived connections` 모델에서는 해당 연결은 종료 되며, 브라우저는 하나의 페이지를 렌더링하기 위한 여러가지 리소스들을 반복해서 요청할 때 마다 하나 이상의 연결이 다시 생성 되어야 한다.  

사용자의 명시적인 요청(refresh) 없이도 업데이트 된 페이지를 표시하고, 전체 리소스가 아닌 업데이트가 필요한 부분만을 요청함으로써 성능의 개선을 가져온 것이 AJAX이다. 요청을 위해 javascript를 사용하지만, 매 요청 마다 새로운 connection이 생성 되는 것은 마찬가지이다. Server의 입장에서는 명시적인 요청이 없다면 클라이언트가 갱신된 정보를 필요로 한다는 사실을 알 수 없기 때문에, 어쨌든 명시적인 요청이 있어야 응답을 받을 수 있다는 한계는 동일하다. 일정 시간을 주기로 client가 반복적인 요청을 함으로써 갱신된 내용을 확인하는 방법도 있겠지만 효율적인 구조는 아니다.

실시간 채팅 서버를 위해서는 사용자의 요청 없이도 갱신된 내용을 확인 가능해야 하므로 이러한 구조적 제약을 해결할 필요가 있다.

#### Web Socket이란?
**IETF**(Internet Engineering Task Force)에 의해 정의 된 Web Socket에 대한 명세는 아래와 같다. [RFC6455](https://tools.ietf.org/html/rfc6455)
> The WebSocket Protocol enables two-way communication between a client running untrusted code in a controlled environment to a remote host that has opted-in to communications from that code.  
...  
The goal of this technology is to provide a mechanism for browser-based applications that need two-way communication with servers that does not rely on opening multiple HTTP connections (e.g., using `XMLHttpRequest` or `iframe`s and `long polling`).

정리해보면,  
* Web Socket은 protocol이며,
* Server와 client간의 양방향 통신을 가능하게 하고,
* 복수의 HTTP connection에 의존하지 않는다.  

즉, 항상 열려있는 연결을 통해 필요한 정보를 server와 client가 주고 받을 수 있게 해주는 `protocol` 이라는 것이다. WebSocket의 handshake 과정은 아래와 같다.  

<p align="center">
  <img width="" height="" src="https://media.springernature.com/lw785/springer-static/image/art%3A10.1007%2Fs11042-018-5821-z/MediaObjects/11042_2018_5821_Fig2_HTML.gif">
</p>

WebSocket은 HTTP와 마찬가지로 80번 포트를 통해 통신하며(Sercure 버전인 WSS는 443번 포트), `Upgrade` header를 이용하여 프로토콜 전환을 요청한다. 그 뒤 `Protocol-Overhead`방식(TCP 커넥션을 여러 개 생성하지 않고, 80번 포트의 커넥션 하나 만을 사용 하면서 별도의 헤더 등을 이용해 logical 하게 여러 개의 커넥션을 맺는 효과를 내는 방식)으로 데이터를 주고 받는다. 이후 주기적으로 heartbeat 패킷을 발생 시키면서 커넥션이 정상적으로 유지 되고 있는지 확인 하게 된다.


#### golang의 WebSocket package
실제 구현을 위해 Web Socket protocol의 golang 구현체를 찾아본다. golang의 장점인 안정적이고 강력한 standard library를 활용하기 위해 godoc에서 WebSocket으로 검색하면 아래 패키지를 찾을 수 있다.  
[https://godoc.org/golang.org/x/net/websocket](https://godoc.org/golang.org/x/net/websocket) 

하지만 description에 아래와 같은 내용을 찾아볼 수 있다.

> This package currently lacks some features found in an alternative and more actively maintained WebSocket package: [**https://godoc.org/github.com/gorilla/websocket**](https://godoc.org/github.com/gorilla/websocket)

즉, 더 잘 관리되고 더 많은 기능을 가진 WebSocket 패키지가 저기 있다는 말이다. 
>1. Standard websocket library로 상용 서비스 구현이 불가능한 수준인가?
>2. 왜 standard library에 미지원 기능을 추가 하지 않고 있는가?
>3. 여러 WebSocket library가 있을 텐데 왜 저것을 추천하는가?  

하는 의문도 생기고 추후 직접 WebSocker 구현체를 만들어보면 좋겠지만, 지금의 목표는 golang + WebSocket를 이용한 채팅 서비스 구현이므로 ~~추천 받은~~ gorilla/websocket 패키지의 실제 사용 방법을 먼저 확인해본다.

#### [gorilla/websocket package](https://godoc.org/github.com/gorilla/websocket)
##### Overview
gorilla/websocket 패키지는 [RFC6455](https://tools.ietf.org/html/rfc6455)에 정의된 WebSocket 프로토콜을 구현하고 있다. `Conn` type을 통해 WebSocket 커넥션을 관리한다. Server application은 `Conn`을 얻기 위해 HTTP request handler에서 Upgrader.Upgrade 메서드를 호출한다.

```go
var upgrader = websocket.Upgrader{
    ReadBufferSize:  1024,
    WriteBufferSize: 1024,
}

func handler(w http.ResponseWriter, r *http.Request) {
    conn, err := upgrader.Upgrade(w, r, nil)
    if err != nil {
        log.Println(err)
        return
    }
    ... Use conn to send and receive messages.
}
```

Byte slice 형태의 메시지를 송/수신 하기 위해 `conn`의 `WriteMessage` 또는  `ReceiveMessage` 메서드를 사용한다. 아래 code snippet은 이 메서드를 이용해 간단한 echo message 동작을 보여준다.

```go
for {
    messageType, p, err := conn.ReadMessage()
    if err != nil {
        log.Println(err)
        return
    }
    if err := conn.WriteMessage(messageType, p); err != nil {
        log.Println(err)
        return
    }
}
```

위 code snippet에서 `p`는 `[]byte`이고 `messageType`은 `websocket.BinaryMessage` 또는 `websocket.TextMessage` 값을 가진 `int`이다.  
Application은 `io.WriteCloser` 또는 `io.Reader` 인터페이스의 구현체를 통해서도 메시지를 송/수신 할 수 있다. 메시지를 송신하기 위해, `NextWriter` 메서드를 호출해 `io.WriteCloser` 값을 얻은 후 메시지를 write하고 완료 시 close한다. 마찬가지로 메시지 수신을 위해, `NextReader` 메서드를 호출해 `io.Reader` 값을 얻은 후 메시지를 read 한다. (`io.EOF`가 리턴 될 때 까지) 아래 code snippet은 `NextReader`와 `NextWriteCloser` 메서드를 이용한 echo message 동작을 보여준다.

```go
for {
    messageType, r, err := conn.NextReader()
    if err != nil {
        return
    }
    w, err := conn.NextWriter(messageType)
    if err != nil {
        return err
    }
    if _, err := io.Copy(w, r); err != nil {
        return err
    }
    if err := w.Close(); err != nil {
        return err
    }
}
```

##### Date Message
WebSocket 프로토콜은 message type을 `Text message`와 `Binary data message` 두 가지로 구분한다. Text nessage의 경우 UTF-8 encoded text로 해석하며, Binary data message의 해석은 application의 구현에 의존한다.  
gorilla/websocket 패키지는 두 가지 메시지 타입을 구분하기 위해 `TextMessage`와 `BinaryMessage`로 정의 된 integer constants를 사용한다. 위에서 설명한 `ReadMessage`와 `NextReader` 메서드는 현재 메시지의 type을 리턴한다. 이 메시지 타입은 `WriteMessage`와 `NextWriter` 메서드들이 현재 메시지 타입을 구분할 수 있도록 인자로 넘겨진다.  
Text message가 적합한 UTF-8 encoded value임을 보장하는 것은 application의 영역이다.

##### Control Messages
WebSocket 프로토콜은 아래와 같이 세 가지 타입의 control message를 정의하고 있다 :
> close  
> ping  
> pong  

`SetCloseHandler` 메서드를 통해 등록한 handler function를 호출하고, `NextReader`나 `ReadMessage`와 같은 메시지 수신 method에서 `*CloseError`를 리턴함으써 수신한 `close` 메시지를 처리한다. Default close handler의 경우 상대방에게 `close` 메시지를 전송한다.  
`ping` 메시지의 경우 `SetPingHandler` 메서드를 이용해 등록한 handler function을 이용해 처리하며, default ping handler는 상대방에게 `pong` 메시지를 전달한다.  
`SetPongHandler` 메서드를 이용해 등록한 handler function을 통해 `pong` 메시지를 처리하게 되는데, default pong handler의 경우 아무런 동작도 수행하지 않느다. 만약 application에서 `ping` 메시지를 전달하는 경우, 돌아오는 `pong` 메시지를 의도대로 처리하기 위한 handler function을 설정해야 한다.  
Control message handler function들은 NextReader, ReadMessage 그리고 message reader Read 매서드에서 호출된다. 기본 `close`, `ping` handler의 경우 connection에 handler가 connection에 write 할 때 이러한 메서드들을 짧은 시간 동안 블록 시킬 수 있다.  
Application은 `close` 또는 `ping` 메시지를 처리하기 위해 반드시 connection에서 메시지를 read 해야 한다. 만약 application이 상대방으로부터 전달 된 메시지를 처리하고 싶지 않은 경우, 수신한 메시지를 읽어서 폐기하기 위한 고루틴을 수행해야 한다. 아래 코드는 간단한 예제이다.
```go
func readLoop(c *websocket.Conn) {
    for {
        if _, _, err := c.NextReader(); err != nil {
            c.Close()
            break
        }
    }
}
```

##### Concurrency
Connection은 하나의 `concurrent reader`와 `concurrent writer`를 지원한다.  
Application들은 동시에 하나를 초과하는 go routine이 write method(e.g. `NextWrite`, `SetWriteDeadline`, `WriteMessage`, `WriteJSON`, `EnableWriteCompression`, `SetCompressionLevel`) 호출을 하지 않고, 마찬가지로 같은 순간에 하나를 초과하는 go routine이 read method(e.g. `NextReader`, `SetReadDeadline`, `ReadMessage`, `ReadJSON`, `SetPongHandler`, `SetPingHandler`) 호출을 하지 않음을 보장해야 한다.  
`Close`와 `WriteControl` 메서드들은 다른 모든 메서드들과 함께 동시에 호출 가능하다.

##### Origin Considerations
웹 브라우저는 자바 스크립트 application이 어떤 host든 WebSocket connection을 열 수 있도록 허용한다. 웹 브라우저로 부터 전달 된 `Origin request header`를 사용하는 `origin policy`를 강제하는 것은 서버에 의존한다.  
`Upgrader`는 origin을 검사하기 위해 `CheckOrigin` 필드에 명시 된 function을 호출한다. 만약 `CheckOrigin` function이 fail을 리턴하는 경우 `Upgrade` 메서드는 403 HTTP status code와 함께 WebSocket handshake를 실패하게 된다.  
만약 `CheckOrigin` filed의 값이 nil 인 경우, `Upgrader`는 안전하게 default 값을 사용하게 되며 default 동작은 아래와 같다.  
> 만약 `Origin request header`의 값이 존재하고, `Origin Host`가 `Host request header`와 동일하지 않을 경우 WebSocket handshake는 실패함.

Deprecated package-level `Upgrade` function은 origin 확인을 수행하지 않았으며, application은 `Upgrade` function을 호출하기 전에 origin header를 확인할 책임이 있다.

#### Web Socket Server

#### Web Socket Client
