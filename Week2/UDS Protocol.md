UDS Protocol
============

UDS(Unified Diagnostic Services) 프로토콜은 ECU 점검(Diagnosis)을 위해 사용되는 표준 프로토콜이다. 주로 위부 장치와 자동차 내부 ECU들을 연결해 점검, 펌웨어 업데이트 등을 할 수 있게 해준다. 정식명칭은 ISO 14229로, 국제표준으로 차량 내 통신 프로토콜이 표준화 되어있다. 


![image](https://github.com/user-attachments/assets/983f4913-4088-446c-ab16-fb1179bbf3f2)
이미지 출처: CSS Electronics

OSI 7 layer model
네트워크 통신을 7개의 계층으로 나눈 모델로, 순서대로 Physical, Data Link, Network, Transport, Session, Presentation, Application 계층으로 정의되어 있다. CAN이나 LIN 같은 차량 통신 프로토콜은 1~4의 종단 통신부터 실제 기계신호 전달에 속해 있고, UDS의 경우 5, 7의 상위 계층을 모두 활용한다. UDS는 UDSonCAN, UDSonLIN과 같이 각 하위 계층 프로토콜마다 변환해주는 역할과, 해당 변환을 모두 종합해주는 서로 다른 기준, 표준들을 모두 포괄적으로 지칭한다. 그림에서 session 계층과 application 계층을 모두 통틀어 UDS라고 지칭한다. 


UDS 데이터 프레임

![image](https://github.com/user-attachments/assets/64d941ca-a100-469a-bb26-0a1c13c27d8d)

UDS 데이터 프레임은 위와 같은 구조로 되어있다.
+ CAN ID: 각 ECU 식별용 ID
+ PCI (Protocol Control Information): ISO-TP(후술)응답에 필요한 부분으로, 전체 데이터 길이 혹은 시퀀스 번호 등을 포함
+ SID (Service Identifier): ECU에 요청하는 기능을 설명. 가령, 0x27의 경우 보안 엑세스 요청을 의미함. 응답할 때는 통상적으로 받은 SID에서 0x40을 더한 값이 SID가 됨.
+ SFB (Sub-Fuctional Byte): 필요한 경우 쓰이는 부분. 가령, 0x19로 DTC(Diagnostic Trouble Code, 진단 문제 코드)를 요청하는 경우, 전체 DTC를 받을지, 가장 첫 DTC를 받을지, 가장 빈번한 DTC를 받을지 등 세부 설정을 이 부분을 통해 요구할 수 있다.
+ Data: 실제 데이터 부분
+ Padding: 특정 프로토콜은 데이터의 크기를 맞추어야 하기에, 0x00 등으로 채움

