Bluetooth Stack
===============
&nbsp;
Protocol stack이란, 컴퓨터 네트워킹 프로토콜 모음(protocol suite)에 대한 software implementation이다. Bluetooth stack이라고 함은, bluetooth protocol들의 implementation.

&nbsp;
---

### 블루투스?
&nbsp;

- Bluetooth Classic: 가장 이른 형태의 블루투스. 이어폰, 스피커, 키보드 등에 적용되는 방식. 대용량 데이터 전송을 지원함.
- Bluetooth Smart(Bluetooth Low Energy): 센서 등 저전력 장치에 사용되는 블루투스.
- Bluetooth Smart Ready: BLE에 대한 통신도 지원하는 Classic 장치.

&nbsp;

각 블루투스 종류에 따라, 그리고 같은 종류여도 장치별로 implementation이 다 다르다. 즉, bluetooth stack은 정해진 일정한 양식이 존재하지는 않는다.

&nbsp;
---

### 기초적인 구조
```
┌────────────────────────────┐
│       Application          │  ← 사용자의 프로그램/앱
├────────────────────────────┤
│           Host             │  ← 상위 프로토콜 (소프트웨어)
├────────────────────────────┤
│         Controller         │  ← 하드웨어 또는 펌웨어
└────────────────────────────┘
```
&nbsp;
블루투스 스택은 크게 위의 3가지 계층으로 분류된다.
- Application: 최상위 계층. Host 계층의 API를 호출하여 장치 연결, 데이터 전송 등을 수행하는 계층.
- Host: 소프트웨어로 구현되는 통신 처리, 데이터 송수신 등을 담당
- Controller: 실제 물리적 동작을 하는 하드웨어 계층. USB, SPI 등으로 host 계층과 연결됨.

&nbsp;
---

### Core Specification

https://www.bluetooth.com/specifications/specs/core-specification-6-1/

하지만 블루투스에 대한 아주 기초적인 규격은 존재한다. 가장 최신의 규격은 Bluetooth Core Specifiacation 6.1이다.

```
┌────────────────────────────────────────────────────────────┐
│                         GAP                                │ ← Application
└────────────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────┐
│                         GAP                                │
│ ┌──────┐  ┌────────────┐  ┌──────────────────────────────┐ │
│ │ SDP  │  │  Security  │  │    Generic Attribute Profile │ │
│ │      │  │  Manager   │  │           (GATT)             │ │
│ └──────┘  │   (SM)     │  └──────────────────────────────┘ │ ← Host
│           └────────────┘  ┌──────────────────────────────┐ │
│                           │    Attribute Protocol (ATT)  │ │
│                           └──────────────────────────────┘ │
│ ┌────────────────────────────────────────────────────────┐ │
│ │  Logical Link Control and Adaptation Protocol (L2CAP)  │ │
│ └────────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────┘
├────────────── Host Controller Interface (HCI) ─────────────┤
┌────────────────────────────────────────────────────────────┐
│ Link Manager Protocol (LMP) │     Link Layer (LL)          │ ← Controller
└─────────────────────────────┴──────────────────────────────┘
```

- Generic Access Profile (GAP): Bluetooth를 사용하는 application에 대한 규정. 연결할 디바이스를 발견 및 연결하는 과정, 보안 등에 관한 규정. 기기에 따라 Profile이 다 다름. 이하에 Serial port profile, Generic object exchange profile 등의 통신 등에 대한 규정이 포함됨.
> https://www.bluetooth.com/specifications/specs/hands-free-profile-1-6/
> 가장 최신 핸즈프리 profile
&nbsp;
-  Service Discovery Profile (SDP): Service 검색에 사용. Classic에서만 사용함.
&nbsp;
-  Security Manager (SM): 암호화 통신에 필요한 보안 알고리즘. Pairing, Bonding, Ecryption reestablishment의 세가지 절차가 진행된다. GATT, ATT와 연동하여 데이터 보안 유지.
&nbsp;
-  Attribute Protocol (ATT): GATT를 포함해 서비스와 기타 통신에 사용괴는 데이터 규정. 데이터 + 속성정보 (UUID, 접근권한, 핸들 등)로 구성되어 있다. Client-server 구조로 되어 있다.
> GATT Client: Server에게 request를 보낸다. 연결이 되는 순간 service discovery를 통해 서버가 어떤 서비스를 제공하는지 탐색한다.
> GATT Server: 서비스를 제공하는 주체. Client 에게 request를 받아 상응하는 response를 제공한다. 
&nbsp;
-  Generic Attribute Profile (GATT): ATT 기반으로 service와 characteristic을 구성.
&nbsp;
-  Logical Link Control and Adaptation Protocol (L2CAP): Host의 가장 핵심 기능. 데이터 전송에 대한 규약 포함.
&nbsp;
-  Link Layer (LL): 물리적 계층과 상호작용하며 블루투스 연결을 관리하는 중간 계층. 연결 상태 관리, 암/복호화 등을 담당. Role과 State를 통해 디바이스를 제어함.
> Role
> - Master: 연결 시도. 전체 연결 상태 관리
> - Slave: Master의 요청을 받아들이고 연결되는 대상.
> - Advertiser: Advertising packet을 보내는 역할
> - Scanner: Advertiser를 스캔하는 역할. Passive scanning, active scanning 두가지가 있다.
>
> State
> - Standby: 대기상태
> - Advertising: Advertising Packet을 보내거나, 상대 기기와 요청, 응답을 주고받을 수 있는 상태
> - Scanning: Advertising Channel 에서 스캔 중인 상태
> - Initiating: Advertising Packet을 받고 연결 요청을 보낸 상태
> - Connenction: 연결~
&nbsp;
- Link Manager Protocol (LMP): Classic에서만 사용. 장치간의 물리적 링크를 설정하고 제어하는 프로토콜.

&nbsp;
---

### 블루투스 통신

Advertising, Scanning, Connection Request, Service Discovery, GATT Operation 등의 과정을 거친다.

&nbsp;

1. Advertising: Peripheral(server)이 advertising packet을 마구 전송함. GAP 계층에서 정의됨. 장치 이름, UUID, 연결 가능 여부 등을 송신
2. Scanning: Central(client)이 advertising packet 수신 후 연결 여부 판별.
3. Connection Request: Connection Request 패킷을 보내 연결을 진행. LL Connection.
4. Authentication: SM에서 장치간 보안 연결.
5. Service Discovery: Central이 GATT의 서비스 검색.
6. GATT Operation: 데이터 송수신...
