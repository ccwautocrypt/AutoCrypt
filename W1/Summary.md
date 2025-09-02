### OSI 7 Layer
```
┌────────────────────────────┐
│      Application Layer     │  --> App!
├────────────────────────────┤
│     Presestation Layer     │  --> Data translation on application - network service
├────────────────────────────┤  
│        Session Layer       │  --> Comunication session management
├────────────────────────────┤
│       Transport Layer      │  --> Transmission of data segment (segment)
├────────────────────────────┤
│        Network Layer       │  --> Inter-network data transfer (packet)
├────────────────────────────┤
│        Datalink Layer      │  --> node to node(intra-network) data taransfer (frame)
├────────────────────────────┤ 
│        Physical Layer      │  --> Physical signal, circuit, ... (bit)
└────────────────────────────┘
```

---
### ECU

- Electronic Control Unit
- 차량 내부 각종 제어 장치
- Replay Attack(6.3) / Denial of Service(24.1)

---

### AVN

- Audio, Video, Navigation
- Navigation => AVN => Vehicle Infotainment
- OTA, BT 등등 다양한 통신을 사용

---

### IVI

- In vehicle Infotainment

---

### OTA

- Over-the-air
- Wireless Network를 통한 firmware update
- Compromise update에 대한 위협이 있음 (12.1)

---

### CAN

```
CAN bus frame
┌───┬────────────┬───┬───┬─────┬───//───┬───────────┬─────┬───────┐
|SOF|     ID     |RTR|IDE| DLC |  DATA  |    CRC    | ACK |  EOF  |
└───┴────────────┴───┴───┴─────┴───//───┴───────────┴─────┴───────┘

SOF: Start of Frame. 1 dominant bit
ID: Frame identifier. 신호 우선순위 결정. Classical CAN은 11bit, CAN-FD 같은건 29bit도 지원
RTR: 데이터 송신 / 수신 여부
IDE: ID가 11bit(0) 인지 extended(1)인지 결정
DLC: Data length
DATA
CRC: For DATA integrity check
ACK: ACK check. Remains recessive if none got signal
EOF: End of Field
```
- Controller Area Network
- 0(Dominant)와 1(recessive)로 신호 전달

```
CAN Error Senario
#1 Bit error
- CAN node는 CAN bus를 항상 listen하고 있음.
- 자기가 전송한 데이터와 비트가 다를때.

#2 Bit stuffing error
- CAN bus protocol은 동일한 비트가 5번 연속으로 나오면 반드시 그 다음 비트는 반대를 보내도록 명시함.
- 같은 비트를 6번연속 수신할 경우 bit stuffing error 발생

#3 Form error
- CAN frame의 특정 부분이 잘못된 경우
- SOF가 recessive 일때, EOF가 dominant일 때 등등

#4 ACK error
- Transmitter는 ACK를 항상 recessive로 유지.
- 어떤 node든 message를 수신한경우 ACK 부분에 dominant를 전달
- Transmitter가 ACK에 recessive인 것을 CAN bus에서 listen했다면, ACK error

#5 CRC error
- CRC 값을 보내 수신자와 확인.
- CRC가 다르면 CRC error 발생
```

- 배선이 간단하고 가벼워 차량에서 사용중

---

### OBD

```
OBD2onCAN Frame
┌─────┬────────┬────┬─────┬─────┬─────┬─────┬─────┬────────┐
| CID | #bytes |MODE| PID |  A  |  B  |  C  |  D  | Unused |
└─────┴────────┴────┴─────┴─────┴─────┴─────┴─────┴────────┘

- CID: CAN ID
- #bytes: # of bytes in remaining data
- MODE / PID: Defining requesting data. SAE J1979에 있음.
- A ~ Unused: Data bytes. Last one unused.
```
- On-Board Diagnostics
- 차량 상태 진단 가능 (속도, 온도, 센서, 고장 등등. Mode 변경해 다양한 정보 요청)
- DoCAN(ISO-TP?)로 통신 (자세한건 3주차 UDS 확인)

---

### UDS

```
UDS on CAN Frame
┌─────┬─────┬─────┬─────┬──────────┬─────────┐
| CID | PCI | SID | Sub |   DATA   | Padding |
└─────┴─────┴─────┴─────┴──────────┴─────────┘

- CID: CAN ID
- PCI: ISO-TP(확장된 통신)에 사용
- SID: Service ID. OBD의 MODE-PID와 유사한 역할
- DATA
- Padding
```

- Unified Diagnostic Service
- 차량 상태 진단 가능
- OBD보다 더 광범위한 데이터를 다룰 수 있음.

---

### Automotive Ethernet

---

### DoIP

---

### SOME/IP

---

### BT Classic

---

### BLE

---

### USB

---

### RF

---

### GNSS

---

### NFC

---

### V2G

---
