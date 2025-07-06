ICSim: Virtual Vehicle Network
==============================

### ICSim 이란?
**ICSim(In-Car Simulator)** 은 차량 내부의 CAN 통신을 시뮬레이션하기 위한 도구로, 리눅스 환경에서 실행되며 차량 대시보드와 운전 제어를 가상으로 구현한 프로그램이다. 
CAN 해킹 실습을 위해 설계되었으며, 실제 차량처럼 도어, 방향지시등, 브레이크 등의 기능을 CAN 메시지를 통해 제어하고 분석할 수 있다.

&nbsp;

### 구동환경
```
VirtualBox 7.1.10
uduntu 24.04.2
```

&nbsp;

### 설치과정
```
// 필요 라이브러리 및 패키지 설치
sudo apt update
sudo apt install libsdl2-dev libsdl2-image-dev
sudo apt install git
sudo apt install gcc
sudo apt install make

// ICSim Clonw
git clone https://github.com/zombieCraig/ICSim.git
cd ICSim
make

// 인터페이스 활성화
sudo modprobe vcan
sudo ip link add dev vcan0 type vcan
sudo ip link set up vcan0
```

&nbsp;

![image](https://github.com/user-attachments/assets/5b5b14ee-7a3b-491f-a8a8-28b9e0a043b4)

```
./icsim vcan0      // <-- 가상 차량 구동
./controls vcan0   // <-- 가상 컨트롤러 구동
```
필요한 리소스 설치 후, 위 명령어들로 차량과 컨트롤러를 구동할 수 있다.
컨트롤러에서는 간단한 키보드 동작으로 차량을 조종할 수 있다.

&nbsp;

```
i: 시동 켜기/끄기
w: 와이퍼
t: 방향 지시등
h: 경적
l: 헤드라이트
1: 좌측 방향 지시등
2: 우측 방향 지시등
space: 비상등
방향키: 전진 및 좌/우 회전 (후진 없음)
RShift + A/B/X/Y: 순서대로 앞쪽 좌/우, 뒤쪽 좌/우 문 잠금 해제
LShift + A/B/X/Y: 위와 동일한 순서로 잠금
RShift + LShift: 모든 문 잠금
LShift + RShift: 모든 문 잠금 해제
```
&nbsp;
&nbsp;
&nbsp;
# CAN Protocol "훔쳐"보기

![image](https://github.com/user-attachments/assets/edd1c80a-ebc4-4a8c-aa3e-5a69042d082f)

```
candump vcan0
```
candump 명령어로 차량에서 오가는 CAN 통신 신호들을 모두 확인할 수 있다.

&nbsp;

![image](https://github.com/user-attachments/assets/036f98ad-3144-4f5d-967e-3ac2dcf43ed3)

```
cansniffer vcan0
```
그러나, 굉장히 많은 신호들이 빠르게 발생하기에 candump를 주시하면서 유의미한 수확을 거두기 어렵다. 따라서, cansniffer라는 유용한 명령어를 사용해볼 수 있다. cansniffer는 신호를 ID별로 정리해 보여준다. 또한, c + Enter로 변화하는 바이트를 붉게 표현할 수 있으며, Shift + 3 + Enter로 상시변화하는 바이트는 붉게 표현하는 바이트에서 제거할 수 있다. 이 상태에서 차에 어떤 변화가 생긴다면, 쉽게 신호를 포착할 수 있을 것이다.

&nbsp;

![image](https://github.com/user-attachments/assets/da6944e7-daa0-459b-b484-3ceaeaf0b6ee)

RShift + LShift로 모든 문을 열어보았다. 19B ID로 00 00 00 00 00 00 시그널이 전송된 것을 확인할 수 있다.

&nbsp;

![image](https://github.com/user-attachments/assets/bbeba720-9ae8-4a05-a33c-46a94ce7265d)

다시 LShift + RShift 로 문을 다 닫고,

&nbsp;

![image](https://github.com/user-attachments/assets/ec8e47c0-1573-4e00-8cc5-45fa1193a181)

```
cansend vcan0 [ID]#[Data]
```
위에서 알아낸 신호를 cansend로 보내주면 손쉽게 문을 다 열 수 있다. (모두 닫는 신호는 00 00 0F 00 00 00을 보내면 된다.) 비슷한 형태로 차량의 다양한 ECU에서 보내는 신호들을 도청하거나, 알아내서 원하는 명령을 내릴 수 있다.
