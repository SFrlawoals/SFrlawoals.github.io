---
layout: default
title: OSI 7계층(기초)
nav_order: 1

parent: Network
grand_parent: Pre-Training Bob

---

##### bob 11th 지원대상자가 이수해야 할 사전 교육에 대한 학습 일지입니다.

-----

# 1. 네트워크

## 컴퓨터 네트워크
- 데이터 전송과 처리를 유기적으로 결합하여 어떤 목적이나 기능을 수행

<br>

## 네트워크의 구성

![image-20220603142521523](../img/image-20220603142521523.png)

<br>
<br>
<br>

-----

# 2. OSI 7계층

## 구조

![image-20220603142637009](../img/image-20220603142637009.png)

※ ISO: International Organization for Standardization

## 1계층: Physical
- 두 시스템간의 물리적 연결을 위한 전기적 메커니즘, 절차, 기능
- 일반 랜케이블(UTP,FTP,STP) 동축 케이블, 광케이블  
※ UTP: Unshielded Twisted Pair  
※ FTP: Foil Screened Twisted Pair  
※ STP: Shielded Twisted Pair 

<br>

## 2계층: Datalink
- 물리적 링크를 통하여 정보전송
- 흐름제어: 수신측에 맞춰서 송신측의 속도, 전송량을 제어 
- 오류제어: 오류가 있을 시 재 전송욜 요청 => 신뢰성 
- MAC 주소의 앞 6자리는 OUI(NIC카드의 회사 정보), 뒤 6자리는 HI(호스트 정보)로 구성
- 스위치 장비, 이더넷   
※ OUI : Organization Unique Identifier  
※ HI : Host Identifier

<br>

## 3계층: Network
- 네트워크를 통한 패킷의 전송을 담당
- 패킷이 목적지에 도착하기 위해  라우팅 알고리즘을 사용하여 경로 설정
- 이 기종 네트워크들의 상호 연결
- IP Address, Routing, Route, Gateway
  1. 공인 IP주소
    - A Class 0~127
    - B Class 128~191
    - C Class 192~223
  2. 사설 IP주소
    - A Class 10.0.0.0 ~ 10.255.255.255
    - B Class 172.16.0.0 ~ 172.31.255.255
    - C Class 192.168.0.0 ~ 192.168.255.255

<br>

## 4계층: Transport
- 종단간 신뢰성 있는 데이터 전송을 담당
- 연결지향(TCP) 또는 비 연결지향(UDP) 
- 흐름제어, 오류제어 수행
- 혼잡제어: 트래픽이 많이 있을 시 송신측의 송신량을 조절  
※ Port(포트): 패킷이 응용프로그램을 찾아갈 통로 번호

<br>

## 5계층: Session
- TCP Protocol
- 두 시스템간의 통신중 동기화와 데이터교환을 관리
- 연결의 설정, 유지, 해제, 전송 데이터 속도조절

<br>

## 6계층: Presentation
- 표현에 대한 방법 결정
- 코딩, 코덱, 암호화, 압축

<br>

## 7계층: Application
- GUI
- Internet Explore, Outlook, messenger
- HTTP, FTP, 터미널 서비스, 디렉토리 서비스

<br><br><br>

-----

# 3. TCP/IP 구조

![image-20220603145620665](../img/image-20220603145620665.png)

## 1계층: Network

## 2계층: Internet

![image-20220603150316254](../img/image-20220603150316254.png)

- 라우터->스위치->컴퓨터 구성에서 2계층인 스위치는 MAC주소 값을 다루기 때문에 3계층인 라우터에서 MAC과 IP주소의 변환이 이루어져야 통신이 가능해진다.
- ARP: Address Resolution Protocol. IP에 해당하는 호스트의 하드웨어 주소(MAC)를 찾는 기능
- RARP: ARP의 반대의 기능.물리적 주소 -> 논리적 주소.
- ICMP: 송신측의 상황과 목적지 노드의 상황을 진단(Ping)
- ARP table: 사전에 어떤 IP가 어떤 MAC주소인지 기록되어 있음
  1. ARP Request(broadcasting)를 이용하여 정보수집
  2. ARP Response,Reply를 확인하여 기록  
  ※ ICMP: Internet Control Message Protocol

<br>

## 3계층: Transport
### TCP(Transmission Control Protocol)
- 접속 지향 프로토콜(Connection Oriented)
- 높은 신뢰성: PAR(Positive Acknowledgement with Retransmission)
- 가상회선 연결 방식
- 연결의 설정과 해제
- 데이터 체크섬
- 시간 초과와 재전송
- 데이터 흐름 제어

![image-20220603162637757](../img/image-20220603162637757.png)

<br>
### 3-Way handshaking: 연결

![image-20220603151936135](../img/image-20220603151936135.png)

<br>

### 4-Way handshaking: 종료

![image-20220603155454309](../img/image-20220603155454309.png)

<br>

### 흐름제어 기법: Sliding Window
- 한번에 전송할 수 있는 연속된 프레임들의 크기
1. 송신된 후에 Ack를 받은 프레임
2. 송신되었지만 Ack를 받지 못한 프레임
3. 아직 송신되지 않았지만 곧 송신할 프레임
4. 송신이 불가능한 프레임

<br>

### UDP(User Datagram Protocol)
- 비연결 지향형(Connectionless Oriented)
- 네트워크 부하 감소
- 비신뢰성
- 전송된 데이터의 일부가 소실됨

![image-20220603162846377](../img/image-20220603162846377.png)

<br>

### Routing
- IGP
  - Interior Gateway Protocol
  - AS 내부 간에서 이루어지는 라우팅 프로토콜
  1. RIP(Routing Intormation Protocol): hop수를 기준으로 탐색(Distance Vector)
  2. OSPF(Open Shortest Path First): Delay time과 같은 요소들을 포함하여 탐색(Link State)

- BGP
  - Border Gateway Protocol
  - AS와 AS 간 이루어지는 프로토콜  
  ※ AS: Autonomous System. 하나의 네트워크 관리자에 의해 관리되는 라우터 집단  
  ※ ISP: Internet Service Protocol. 인터넷에 접속하는 수단을 제공하는 주체

<br>

### IP

![image-20220603164048757](../img/image-20220603164048757.png)

<br>

## 4계층: Application
