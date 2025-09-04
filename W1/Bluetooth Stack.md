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
