Q. Bluetooth role 이 바뀔수 있는건지?

```
[Advertiser]       [Scanner]
     ↓                ↓
  Advertising     →  Scanning
     ↓                ↓
     ↓←←←←←←←←←←←←←←←←
    Initiating (연결 요청)
        ↓
     Connection (연결 상태)
```

```
                   scanning
                      ↑
                      ↓
advertising < -- > standby < --> initiating
     ↓                ↑               ↓
     -----------> connection < --------
```

---

L2CAP, RFCOMM, SDP 모두 bluretooth classic에서 사용되는 요소. BLE 에서는 GATT와 ATT로 대체되는 부분.

RFCOMM: Radio Frequency Communication. Bluetooth 위에서 작동하는 가상 시리얼 포트. SABM, UA, UIH, DISC, UA의 5가지 프레임을 이용해 통신한다.

SDP: Service Discovery Protocol. SDP client와 SDP server가 다른 역할을 한다. 장치가 서로 어떤 서비스를 비원하고, 어떤 매개변수를 사용하여 연결되는지 파악하는데 사용.
```
PDUID: 0x02(서비스 UUID 검색), 0x05(속정 정보 응답) 등등...
Transaction ID: response - request pairing
Parameter length
Paramater
```
> 1. Device A가 Device B의 A2DP 서비스를 이용하려함.
> 2. A --> B 0x110D 서비스 탐색 요청(0x02)
> 3. B --> A 0x110D 서비스의 handle 반환(0x03)

<img width="670" height="423" alt="image" src="https://github.com/user-attachments/assets/c2073224-77ce-45b6-a8cd-66a276349cd3" />

출처: https://www.bluetooth.com/wp-content/uploads/Files/Specification/HTML/Core-54/out/en/host/logical-link-control-and-adaptation-protocol-specification.html 

L2CAP: SDP, RFCOMM 등 상위 프로토콜과 HCI 등 하위 프로토콜 사이에 위치. 중간계층으로 데이터 관리 등을 담당.
```
Multiplexing: 상위 프로토콜들에서 여러 profile을 동시에 사용할 수 있도록 채널 관리
Segmentation / Reassembly
QoS: 전송 속도, 우선순위 등 관리
Group Management: 여러 장치를 그룹화 해 데이터 전송 등에서 효율 증대
```

---

블루투스 통신 예시.
1. 스마트폰이 주변 bluetooth 기기 검색
- LL: 물리신호 송수신. 
- LMP: 장치 주소, class 등 전달.
2. 이어폰과 연결 시도 + ACL 링크 수립
- LL: 신호 송수신. Role 등이 바뀜
3. 페어링 / 인증
- LMP: 인증 수행
4. 서비스 탐색
- L2CAP: SDP 요청을 위한 채널 개성.
- SDP: 이어폰 지원 프로파일 확인. 서비스 요청 및 전송
5. 채널 오픈
- L2CAP: RFCOMM 채널 오픈
- RFCOMM: SABM(세션 초기화) --> UA(응답)
6. 데이터 송수신
- RFCOMM과의 데이터 교환 분할 / 재조립
-  RFCOMM: --> UIH(데이터 송수신) --> DISC(종료)
