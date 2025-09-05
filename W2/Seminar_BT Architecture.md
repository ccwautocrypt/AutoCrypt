Bluetooth란?
===
- Bluetooth Classic / Bluetooth Low Energy (다른 종류면 서로 통신 안됨) + Bluetooth Smart Ready (둘 다 되는거)
- 차량에서는 거의 모든 장칙에 BT가 들어감.
- 가끔 BLE도 씀. 가령 테슬라 차량의 TPMS(Tire Pressure Monitoring System)는 BLE를 사용중에 있음. --> 보안 취약점 이었음.
- 근데 점차 BLE로 넘어가는 추세라 어캐 될지 모르지 않을까>...?
- .
- Central - Peripherals 연결.
- 이렇게해서 'Piconet'구조를 이루어 통신을 하게 됨

---

Bluetooth Core Specification?
- Bluetooth SIG에서 발표하는 블루투스 표준 문서
- Bluetooth SIG Qualificiation Process를 통과하려면 여기를 준수해야 한다. (아니면 그냥 통신 장비임)
- Core Specification + 내가 원하는 기능 추가 가능. Airpods 같은건 Apple 기기 끼리만 추가 작동하는 기능들이 추가되어 있음.

Core System Archetecture
===

<img width="1528" height="2000" alt="image" src="https://github.com/user-attachments/assets/b0ff850d-8a44-45e7-90cd-077dc2e740fd" />

Architecture - BT
- 2.4GHz ISM band
- 1Mbs(BR), 2/3Mbs(EBR) 통신
- link manager, link controller, BE/EDR radio block (controller)
- L2CAP, SDP, GAP (Host)
- Bluetooth core system protocols: Radio(PHY) protocol, Link control/Link manager protocol, L2CAP, SDP, STT --> 이 프로토콜간의 신호 교환도 specification에 다 나와있음.
- .
- Service: Bluetooth core system 내부에서 외부로 제공하는 기능 집합. (L2CAP은 채널 오픈, 데이터 전송 등의 서비스를 제공.)
- SAP: Service Access Point. SAP를 통해 service에 접근함.
- Device control service(C plane), Transport Control Service(C plane), Data Service(U plane) 이렇게 세 종류 service가 존재한다.
- .
- 임의의 Host <-> Controller 연결을 상정. Controller는 상대적으로 작은 버퍼를 가지고 있을 확률이 높기에, L2CAP 에서 데이터 가공을 담당하게 된다.
- Protocol Data Unit(PDU): 동일 통신 계층 간에 교환되는 데이터량. 데이터 + 헤더
- Service Data Unit(SDU): 상향/하향 통신 계층 간에 교환되는 데이터량. 순수 데이터만 해당.
- L2CAP SDU: 상위 계층에서 L2CAP으로 들어오는 데이터.
- L2CAP PDU: L2CAP이 실제 전송하는 단위.
- .
- Baseband, LE link layer는 ARQ protocol을 포함해야함.
- Acknowledgement/Repeat Request: 에러 검출 후 재전송을 자동으로 요청하는 방식.
- 해당 기능이 L2CAP에 들어갈 수도 있음. (--> window-based flow control)
- 필수는 아니지만 해당 기능을 통해 QoS를 강화할 수 있음.

---

Architecture - BLE (차이점만 기술)
- 500kbs, 2Mbs(LE 2M PHY), ... 다양하게 있음.
- link manager, link controller, LE radio block (controller, BT와 다른 것 유의!)
- L2CAP, SMP, ATT/GATT, GAP 

---

Core architectural block: Host
- Channel Manager: L2CAP channel create/manage/close 담당.
- 이 channel을 통해 service protocol, application data stream을 송수신.
- peer device의 channel manager와 직접 interact해 L2CAP channel open.
- 로컬의 Link Manager와 상호작용 해 logical link를 만들기도 함. (필요에 따라)
- .
- L2CAP resource manager: PDU submission(to baseabnd) manage, channel scheduling, traffic policing. 전체적으로 QoS를 담당함.
- Multiplexing, Segmentation, Reassembly... 등등 데이터 관리
- Controller의 버퍼가 작아서, controller buffer가 꽉차 통신이 지연되는 경우를 막기 위함.
- traffic policing: 어떤 channel이 타협한 QoS보다 큰 데이터를 밀어넣으면, 적당히 처리하도록(정확히 어떻게는 개발하는 쪽에서 알아서)
- .
- Security Manager Protocol(BLE): generate encryption key/identity key and store
- peer device의 SMP와 L2CAP channel을 통해 직접 상호작용.
- 생성된 키는 controller에 제공하며 향후에 authentication / pairing에 사용함.
- Why SMP?
- BT에서는 해당 역할을 controller의 Link Manager가 겸임함.
- BT에서는 Profile에 따라 protocol이 다 다름. + 옛날이라 PIN입력 -> 링크 키 생성 -> 암호화가 끝이라 생각해서 controller안에 다 집어넣음.
- BLE는 저전력 요구 + ATT/GATT로 Profile 간의 통신을 포함, 각종 범용적 기능을 모두 규정함. 여기서 보안 관련이 host쪽으로 넘어가게 되면서 SMP가 따로 생기게 됨.
- .
- Attribute Protocol(BLE): peer device의 속성값을 r/w 하기 위한 protocol.
- ATT server, ATT client로 나누어져 있음. ATT client는 perr device의 ATT server와 L2CAP channel(ATT 전용으로 예약되어 있음)을 통해 상호작용.
- (Client) command. request, confirmation --> <-- response, notification, indication (Server)
- .
- Generic Attribute Protocol(BLE): represents functionality of ATT server(opptionally, ATT client).
- Service, Characteristic, Attribute 등을 정의해 ATT를 통해 유의미한 활용성을 갖도록 함.
- 이를 통해 서비스 discover, read, write, indicate 등을 할 수 있는 interface를 제공함.
- .
- Service Discovery Protocol: provides mechanism for service search.
- BT에만 존재하는 계층으로, ATT/GATT가 하는 서비스 탐색의 역할을 담당함.
- .
- Generic Access Profile: represent base functionality for all Bluetooth device.
- Bluetooth 기기 끼리 서로 탐색, 연결 등을 해서 정보 교환을 할 수 있도록하는 framework

---

Core architectural block: Controller
- Device Manager: controls general behavior of bluetooth device.
- Data 송수신을 제외한 거의 모든 부분 관할.
- inquiring, discoverable, connecting 등등...
- .
- Link Manager: creation, modification and release of logical links.
- Physical channel: 2.4GHz 대역에서 실제 주파수-시간 슬롯 hopping 시퀀스를 공유하는 무선 경로
- Physical link: 두 장치가 같은 물리 채널에서 연결된 상태 (master–slave 쌍)
- Logical link: 그 물리 링크 위에서 역할/트래픽 종류별로 나눠진 논리적 경로
- .
- Baseband Resource Manager
- 타임슬롯/주파수 홉핑/패킷 스케줄링을 조율. ACL과 SCO(eSCO)의 슬롯 예약/우선순위를 조정해 충돌 없이 무선 자원을 배분.
- 일단 뭐하는지 잘 모르겠어서 더 찾아봐야 할 듯.
- .
- Link Controller: Encode/Decode bluetooth packet from data payload. (+ parameters related to below layers)
- packet formatting, FEC, AQL, flow control,...
- .
- PHY: Transmitt / Recieve in physical channel.
- 2.4GHz ISM.
- .
- Isochoronous Adaptation Layer: Enables isochronous communication. (even between different frames)
- 원래는 없던 계층인데, A2DP등 동시성이 필요한 경우가 생겨 추가됨
- Service Data Unit <-> Protocol Data unit 변환을 주요하게 함.
- .
- Channel Sounding: Preciese distance measurement.
- PBR(Phase Based Ranging): 파형의 위상차를 통해 거리측정하는 방식
- RTT(Round Trip Timimg): 패킷의 회신까지 걸리는 시간으로 측정하는 방식


Transport Architecture
===
