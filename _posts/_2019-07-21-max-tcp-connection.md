# TCP
우선 `TCP`에 대해 간단하게 정리해본다.  

`TCP`는 Transmission Control Protocol의 약자로, 네트워크에서 `패킷`을 교환하는 방법에 관한 스펙을 정의한 프로토콜이다. 여기서 `패킷(packet)`이란 데이터를 일정 크기로 분할하여 패키징한 단위로, `TCP`는 사용자의 데이터를 `패킷` 단위로 분할하여 전송하고 전달 받은 `패킷`을 원래 데이터(여기서는 상위 레이어의 데이터)로 재조립한다.  

![](http://pigbrain.github.io/assets/themes/Snail/img/Network/OSI7/pdu.png) 

네트워크 레이어에 대한 표준 모델인 `OSI 7 Layer`에서 네 번째 레이어(L4)인 `Transport` 레이어에 속한다. L4에 속한 프로토콜은 `TCP` 외에도 `UDP`, `RDP`, `SCTP` 등이 있으며, 각각은 패킷 헤더의 크기, 패킷 전달 순서 보장 여부, 흐름 제어 여부, 혼잡 제어 여부 등의 특징으로 구분된다. 네트워크를 이용하는 상위 계층의 각 서비스를 구분하고, 각각의 서비스에 대해 적절한 프로토콜을 통해 효율적인 통신을 가능하게 하는 것이 목적이다.  

아래는 TCP의 주요 특징을 정리한 것이다.
- Connection oriented  
  : TCP는 서버와 클라이언트가 연결되어 있는 것 처럼(loosely connected) 동작 가능한 뷰를 제공하며, 이를 관리하기 위해 연결 시 `3-way-handshaking`, 연결 해제시 `4-way-handshaking` 방식을 사용.
- Full-duplex/Bi-directional communication  
  : End-to-end 간 양 방향 각각에 대해 동시에 통신이 가능하며, 이를 위해 `송수신용 버퍼`를 가짐.
- Byte stream  
  : 어떠한 논리적 의미를 가지는 `message` 스트림이 아닌 단순한 바이트들의 스트림으로 보고 이를 묶어서 처리.
- Reliablility  
  : 
- 


