1-day attack?
===

공개되었지만 아직 패치되지 않은 취약점.

---

### PerfektBlue

https://pcacybersecurity.com/resources/advisory/perfekt-blue

OpenSynergy의 BlueSDK 블루투스 스택에서 발견된 Chaining을 이용한 RCE 공격. Mercedes-Benx, Volkswagen, Skoda 등 여러 브랜드 차량에 영향. 

---

Chaining 구조
- CVE-2024-45431: L2CAP CID 검증 없음
- CVE-2024-45432: RFCOMM 함수 종료 오류
- CVE-2024-45433: RFCOMM 잘못된 인자 전달ack?
===

공개되었지만 아직 패치되지 않은 취약점.

---

PerfektBlue

OpenSynergy의 BlueSDK 블루투스 스택에서 발견된 Chaining을 이용한 RCE 공격. Mercedes-Benx, Volkswagen, Skoda 등 여러 브랜드 차량에 영향. 

---

Chaining 구조
- CVE-2024-45431: L2CAP CID 검증 없음
- CVE-2024-45432: RFCOMM 잘못된 인자 전달
- CVE-2024-45433: RFCOMM 함수 종료 오류 (예외 처리 시 적절히 return 되지 않는다고 함)
- CVE-2024-45434: AVRCP(블루투스 오디오/비디오 제어 프로파일)에서 발생한 Use-after-free 문제 (가장 심각해요)

1. 45431: null 등 비정상적인 L2CAP 통신 채널 오픈
2. 45433 + 45432: RFCOMM 에 잘못된 데이터를 전달
3. 45434: RFCOMM 접근 권한 취득해 AVCRP의 Use-After-Free 트리거.

---

Root Cause
- 45431: L2CAP 채널 입력값 유효성 검증 부족
- 45432: 파라미터 검증 절차 없음
- 45433: 예외 처리 미흡
- 45434: 메모리 관리 미흡.
