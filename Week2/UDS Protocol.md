UDS Protocol
============

UDS(Unified Diagnostic Services) 프로토콜은 ECU 점검(Diagnosis)을 위해 사용되는 표준 프로토콜이다. 주로 위부 장치와 자동차 내부 ECU들을 연결해 점검, 펌웨어 업데이트 등을 할 수 있게 해준다. 정식명칭은 ISO 14229로, 국제표준으로 차량 내 통신 프로토콜이 표준화 되어있다. 

&nbsp;

### OSI 7 layer model

![image](https://github.com/user-attachments/assets/983f4913-4088-446c-ab16-fb1179bbf3f2)
이미지 출처: CSS Electronics

네트워크 통신을 7개의 계층으로 나눈 모델로, 순서대로 Physical, Data Link, Network, Transport, Session, Presentation, Application 계층으로 정의되어 있다. CAN이나 LIN 같은 차량 통신 프로토콜은 1~4의 종단 통신부터 실제 기계신호 전달에 속해 있고, UDS의 경우 5, 7의 상위 계층을 모두 활용한다. UDS는 UDSonCAN, UDSonLIN과 같이 각 하위 계층 프로토콜마다 변환해주는 역할과, 해당 변환을 모두 종합해주는 서로 다른 기준, 표준들을 모두 포괄적으로 지칭한다. 그림에서 session 계층과 application 계층을 모두 통틀어 UDS라고 지칭한다. 

&nbsp;

### UDS 데이터 프레임: Request

![image](https://github.com/user-attachments/assets/64d941ca-a100-469a-bb26-0a1c13c27d8d)

UDS 데이터 프레임은 위와 같은 구조로 되어있다.
+ CAN ID: 각 ECU 식별용 ID
+ PCI (Protocol Control Information): ISO-TP(후술)응답에 필요한 부분으로, 전체 데이터 길이 혹은 시퀀스 번호 등을 포함
+ SID (Service Identifier): ECU에 요청하는 기능을 설명. 가령, 0x27의 경우 보안 엑세스 요청을 의미함. 응답할 때는 통상적으로 받은 SID에서 0x40을 더한 값이 SID가 됨.
+ SFB (Sub-Fuctional Byte): 필요한 경우 쓰이는 부분. 가령, 0x19로 DTC(Diagnostic Trouble Code, 진단 문제 코드)를 요청하는 경우, 전체 DTC를 받을지, 가장 첫 DTC를 받을지, 가장 빈번한 DTC를 받을지 등 세부 설정을 이 부분을 통해 요구할 수 있다.
+ Data: 실제 데이터 부분
+ Padding: 특정 프로토콜은 데이터의 크기를 맞추어야 하기에, 0x00 등으로 채움

&nbsp;

### UDS 데이터 프레임: Response

![image](https://github.com/user-attachments/assets/adfff2a7-4370-42b1-859f-92807347d98e)

+ DID (Data Identifier): 응답 ID. 이후에는 Padding이라 적어놓았지만 필요한 데이터가 들어간다.
+ Negative Response SID: 0x7F로, negative response를 나타낸다.
+ Rejected SID: 거부된 SID
+ NRC (Negative Response Code): 어떤 이유로 negative response가 나왔는지 명시한다. 가령, 0x13의 경우 메시지 길이가 맞지 않는 경우를 뜻한다.

응답의 경우 조금 다른 데이터 프레임을 띈다. 위쪽은 positive, 아래쪽은 negative 응답이다. Positive의 경우, 요청받은 SID에 대응하는 DID와 함께 필요한 데이터를 돌려준다. 하지만 negative의 경우, negative임을 알리기 위한 SID(0x7F)와 함께 거절된 요구와 왜 거부되었는지를 함께 반환한다. 

&nbsp;

### ISO-TP (ISO Transport Protocol)

UDS의 경우 CAN보다 더 긴 데이터 프레임을 지원한다. 따라서, 상황에 따라 CAN의 데이터 길이에 허용되지 않는 크기의 데이터를 수신할 때도 있는데, 이와 같은 경우 긴 데이터를 분할 전송해 재조립하는 방식으로 데이터를 송수신한다. 이러한 방식을 ISO-TP라고 한다. 

![image](https://github.com/user-attachments/assets/04c57794-2a2b-4ab6-9c31-54c89d061e68)

초기 client의 요청(Single Frame)에, 보내야할 데이터 양이 CAN single frame에 모두 들어가면 한번의 응답으로 종료된다. 하지만, 그렇지 못할 경우에, ECU는 우선 전체 패킷 길이와 데이터 청크에 관한 정보(First Frame)를 우선 보낸다. 그 이후, client가 FF를 수신하면 FC를 보내 나머지 데이터를 어떻게 보낼지 알려준다. FC를 수신한 ECU는 그 이후 데이터를 분할해 여러개의 CF들로 나누어 송신한다. 

&nbsp;
---
&nbsp;

### 통신 예시

Single Response. 보안 인증 요구.
```
Server               Client

                  <-- 10 03   // Extended Session 요청. SID 0x10

50 03 -->                     // 허가. DID 0x50

                  <-- 27 01   // seed 요청. SID 0x27

67 01 seed -->                // 허가. DID 0x67

              <-- 27 02 key   // key 송신. SID 0x27. sub-function byte로 seed 수신과 구분.

67 01 -->                     // positive인 경우.
7F 27 35 -->                  // negative인 경우. 0x27(key 확인)요청이 0x35(invalid key)로 인해 거절(0x7F)됨.
```

&nbsp;

Multiple Response. 특정 데이터(들) 요구
```
Server                           Client

            <-- 22 F1 90 F1 87 F1 11 ..    // 데이터 읽어(0x22)들이기. 0xF190, 0xF187, 0xF111 ... 요구.

62 10 14 -->                               // positive. 총 응답 길이 0x14 = 20 bytes. (예시)

                          <-- 30 00 0A     // FC: OK to continue, no block size limit, 10ms delay

21 62 F1 90 57 4D 5A 5A -->                // CF #1: SID(0x62) + DID F190 데이터

22 5A 32 33 34 35 36 F1 -->                // CF #2: F1 87부터 계속

23 87 12 34 56 78 9A BC -->                // CF #3: DID F187

24 F1 11 00 01 02 03 04 05 -->             // CF #4: DID F111
```

&nbsp;
---
&nbsp;

### 보안상 취약점?

+ No Encryption. Plaintext로 보냄
+ Seed-Key 방식의 인증이 단순함
+ Denial of Service
