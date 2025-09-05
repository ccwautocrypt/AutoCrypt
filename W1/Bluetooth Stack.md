BLE Stack
```
BLE Protocol Stack
┌────────────────────────────────────────────────────────────────────────┐
│                     Generic Access Profile (GAP)                       |
├────────────────────────────────────────────────────────────────────────┤
│                   Generic Attribute Profile (GATT)                     |
├────────────────────────────────┬───────────────────────────────────────┤  
│    Attribute Protocol (ATT)    |    Security Manager Protocol (SMP)    │
├────────────────────────────────┴───────────────────────────────────────┤
│           Logical Link Control & Adaptation Protocol (L2CAP)           |
├────────────────────────────────────────────────────────────────────────┤
                     Host Controller Interface (HCI)
├────────────────────────────────────────┬───────────────────────────────┤
│  Isochronous Adaptation Layer (ISOAL)  │                               │
├────────────────────────────────────────┤        Channel Sounding       │
│            Link Layer (LL)             │                               │
├────────────────────────────────────────┴───────────────────────────────┤ 
│                             Physical Layer                             │
└────────────────────────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────────┐
|                                 Radio                                  |
└────────────────────────────────────────────────────────────────────────┘

- Physical Layser (PHY): Any BLE(or BT) technology related to use of RF. (EX. modulation scheme, frequency band, transmitter/reciever...)
# BLE, BT는 거의 모든 사항에 대해 RF로 통신을 하는데, 그냥 물리적 형체가 있는 모든 것을 포함하는 듯 함.
# OSI 7 layer 에서 말하는 Physical Layer랑 그냥 거의 똑같다고 해석해도 될 것 같다.

- Link Layer: Definition of air interface packet format, bit processing procedure... etc (EX. Error check, State Machine, Protocols...)

- Channel Sounding: Distance measurement system. Utilizes PBR(Phase Based Ranging) and RTT(Round Trip Timimg)
# PBR은 파형의 위상차를 통해 거리측정하는 방식
# RTT는 패킷의 회신까지 걸리는 시간으로 측정하는 방식

- Isochronous Adaptation Layer: Enables isochronous communication. (even between different frames)
# 원래는 없던 계층인데, A2DP등 동시성이 필요한 경우가 생겨 추가됨
# Service Data Unit <-> Protocol Data unit 변환을 주요하게 하는 듯함.

- Host Controller Unit: Functional interface for bi-directional communication between Host and Controller layer

- L2CAP: 
```



---

### 나중에 정리할 내용들
```
✅ 2. Profile 설계 자체에서 발생하는 보안 문제

Profile은 GATT를 기반으로 “어떤 데이터를 누구나 읽을 수 있게 할지, 쓰려면 권한이 필요한지” 등을 정하는데, 여기서 설계가 잘못되면 보안 취약점이 생깁니다.

예시:

민감 데이터에 보호 속성 미설정

Custom Profile에서 Health 데이터(예: 혈압, 위치, 인증 토큰)를 “읽기 권한 = Open”으로 설정

→ 암호화 세션 없어도 누구나 읽을 수 있음

쓰기 권한 오용

Device Control Characteristic에 인증 절차 없이 “Write without Response” 허용

→ 공격자가 임의로 디바이스 동작 제어 가능

Notification/Indication 남용

구독(Subscribe) 기능에 권한 검증이 없으면, 아무 장치나 민감 이벤트 알림을 받을 수 있음

표준 Profile 확장 시 취약성

표준 Battery Service를 확장하면서 Custom Field를 추가했는데, 해당 필드에 권한 보호 미적용

→ 비표준 부분에서 취약점 발생
```
```
정리하면: Bluetooth 6.0이라는 건 “BT Classic vs BLE” 중 하나를 의미하는 게 아니라, Bluetooth Core Specification의 버전을 뜻합니다.

✅ Bluetooth Core Spec 버전과 기술

Bluetooth 1.0 ~ 3.0 → Classic (BR/EDR) 위주

Bluetooth 4.0 (2010) → 처음으로 BLE (Bluetooth Low Energy) 도입

Bluetooth 5.0 (2016) → BLE 성능 크게 향상 (2 Mbps PHY, Long Range, Advertising 확장)

Bluetooth 5.2 (2020) → LE Audio, Isochronous Channels, LC3 코덱 도입

Bluetooth 5.3 (2021) → 전력 최적화, Enhanced Advertising

Bluetooth 6.0 (2024 예정/개발 중) → LE Audio 확산, 새로운 QoS, 보안 개선, 저전력 최적화

✅ 핵심 포인트

“Bluetooth 6.0”은 BLE만 있는 게 아님 → Classic + BLE 둘 다 포함한 Core Spec 버전

하지만 5.0 이후부터는 BLE 중심으로 진화

오디오도 Classic A2DP → LE Audio로 전환 중

IoT, 자동차, 웨어러블은 전부 BLE 기반

즉, Bluetooth 6.0 = BLE 중심 사양이지만, 표준 안에 Classic(BR/EDR)도 여전히 포함돼 있어요.

✅ 정리

Bluetooth 6.0은 “버전”이지, BLE/Classic 중 하나를 지칭하는 게 아님

Core Spec 6.0 안에는 BLE + Classic 모두 정의

하지만 산업 추세는 → 점차 BLE 중심 (특히 LE Audio) 으로 이동
```

```
✅ 1. 전력 소비

Classic BT: 지속적인 연결(ACL, SCO 채널 유지)이 기본 → 배터리 소모 큼

BLE: 광고/스캔 기반, 필요할 때만 연결 → 초저전력

👉 스마트워치, 이어버드, IoT 센서처럼 배터리 구동 기기에 BLE가 압도적으로 유리

✅ 2. 기능 확장성

Classic BT: 초기엔 오디오/파일 전송, HID 같은 고정된 프로파일 중심

BLE: ATT/GATT 구조 → 유연하게 서비스 정의 가능

→ 헬스케어, 스마트홈, 자동차 센서, 위치 기반 서비스 등 다양한 IoT 응용 가능

✅ 3. 오디오 기술 진화

과거: Classic A2DP/HFP 기반 → 높은 대역폭 스트리밍 지원

현재: LE Audio (BLE 5.2)

새로운 LC3 코덱 → 더 낮은 전력으로 더 나은 음질

멀티스트림 지원 → 이어버드 L/R 동기화

브로드캐스트 오디오 → 여러 사용자에게 동시에 송출

👉 오디오까지도 BLE로 대체 가능해지면서 Classic의 주요 영역마저 BLE가 차지

✅ 4. 데이터 전송 효율

Classic BT: 최대 3 Mbps (EDR) → 실제는 더 낮음

BLE 5.x: 최대 2 Mbps PHY, Long Range 모드(수백 m) 지원, 확장된 광고 패킷 → 효율적

→ IoT, 자동차, 스마트홈 등 낮은 지연·긴 거리·적은 전력 모두 커버

✅ 5. 생태계/표준 방향

Bluetooth SIG도 BLE를 차세대 표준 중심으로 개발 중 (5.x, 6.0 기능 대부분 BLE 확장)

스마트폰, 웨어러블, 자동차 IVI 시스템 전부 BLE 기본 지원

Classic은 여전히 일부 backward compatibility 때문에 유지되지만, 신규 개발은 BLE 중심

🔗 정리

Classic BT: 전력 소모 크고, 오디오/HID 위주, 유연성이 떨어짐

BLE: 초저전력, 확장성 높음, 오디오까지 커버(LE Audio)

→ 그래서 산업계가 Classic을 점차 버리고 BLE 중심으로 이동 중
```
