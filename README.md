# STM32 Mixed-Signal Controller Board Design Project

> STM32 기반의 Mixed-Signal Embedded Controller Board를 직접 기획하고,  
> Requirement 분석부터 부품 선정, Schematic 설계, PCB Layout, 3D 검토, 제조 산출물 준비까지 수행한 HW 설계 프로젝트입니다.

---

## 프로젝트 개요

이 프로젝트는 STM32를 메인 MCU로 사용하는 임베디드 시스템 보드를 설계하는 과정에서,  
단순 MCU 최소회로 수준이 아니라 **전원부, Ethernet PHY, Motor Driver, ADC/DAC, MIC, LDO, ESD/EMI 보호회로, Mixed-Signal PCB Layout**까지 통합적으로 고려하여 설계한 프로젝트입니다.

특히 다음과 같은 실무형 설계 흐름을 중심으로 진행했습니다.

- System-level Requirement 분석
- STM32 MCU 및 주변 부품 선정
- Power Budget 및 Power Tree 설계
- Ethernet PHY / 보호회로 설계
- Motor Driver / MOSFET 설계
- ADC / DAC / MIC / LDO 아날로그 회로 설계
- Mixed-Signal PCB Layout 설계
- 3D Board Viewer를 통한 실장 구조 검토
- Gerber / BOM 등 제조용 산출물 준비

---

## 프로젝트 목표

- STM32 기반 메인 컨트롤 보드 설계 경험 확보
- Mixed-Signal 시스템에서의 전원/접지/리턴패스 설계 이해
- 데이터시트 기반 부품 선정 및 회로 설계 능력 강화
- Ethernet, Motor, Analog 블록이 공존하는 PCB 설계 경험 축적
- 실제 제작 가능한 수준의 PCB 아트웍 및 산출물 정리

---

## 개발 기간

- 진행 기간: `YYYY.MM ~ YYYY.MM`
- 설계 툴: `KiCad / Altium Designer / 기타 사용 툴명`
- 대상 MCU: `STM32Fxxx / STM32Hxxx / STM32 시리즈명 기입`

---

## 프로젝트 핵심 역량

- **STM32 MCU Schematic 설계**
- **Power Budget / Power Tree 설계**
- **Ethernet PHY 및 ESD/EMI 대응 설계**
- **Motor Driver / MOSFET 회로 설계**
- **ADC / DAC / MIC / LDO 아날로그 회로 설계**
- **Mixed-Signal PCB Layout**
- **Power Plane / Ground Plane / Return Path 고려**
- **Differential Pair / Impedance / Noise Suppression 기초 적용**
- **3D Viewer 기반 기구 간섭 및 실장성 검토**
- **Gerber / BOM 등 제조 산출물 정리**

---

## System Architecture

이 보드는 STM32 MCU를 중심으로, 디지털 통신부와 아날로그 신호처리부, 전원부, 보호회로, 모터 구동부를 하나의 시스템으로 통합하는 것을 목표로 설계했습니다.

주요 블록은 다음과 같습니다.

- **Main MCU Block**
  - STM32 Core
  - Clock Tree
  - Reset / Boot Configuration
  - Debug Interface (SWD / JTAG)

- **Power Block**
  - 입력 전원
  - DC-DC / LDO 기반 전원 분배
  - Bulk Capacitor / Decoupling Capacitor 배치
  - Analog / Digital 전원 품질 고려

- **Communication Block**
  - Ethernet PHY
  - RMII / MII 인터페이스 고려
  - RJ45 연결 및 ESD/EMI 보호

- **Motor Control Block**
  - Motor Driver / MOSFET / H-Bridge 구조 고려
  - Load Current, Gate Drive, Switching 특성 검토

- **Analog Front-End Block**
  - ADC / DAC / MIC 인터페이스
  - LDO를 활용한 저노이즈 전원 공급
  - 아날로그 신호 품질 확보를 위한 Layout 분리

---

## 전체 설계 흐름

### 1. Requirement Sheet 분석
프로젝트 초기에 보드에 필요한 기능, 인터페이스, 전원 레일, 통신 방식, 보호회로 요구사항 등을 정리했습니다.

검토 항목 예시:
- MCU 성능 및 필요한 Peripheral
- Ethernet 지원 여부
- ADC / DAC / MIC 인터페이스 필요성
- Motor 구동 회로 포함 여부
- 입력 전압 및 전원 레일 구성
- EMI / ESD 대응 필요성
- PCB Layer 수 및 Stack-up 방향성

---

### 2. MCU 및 주요 부품 선정
STM32 시리즈를 검토하면서 Core 성능, Flash/RAM, Peripheral Interface, Clock Tree Architecture를 기준으로 적절한 MCU를 선택했습니다.

또한 아래와 같은 방식으로 부품을 선정했습니다.

- **STM32 MCU**
  - 필요한 Peripheral 충족 여부
  - UART / SPI / I2C / ADC / DAC / Ethernet 지원 여부
  - 패키지 / 핀 수 / 확장성 고려

- **Ethernet PHY**
  - Link Speed
  - MCU 인터페이스 호환성
  - 주변 저항/트랜스/ESD 부품 구성 가능성

- **Motor Driver / MOSFET**
  - 구동 전류
  - Switching 특성
  - 발열 및 Thermal 고려

- **ADC / DAC / MIC / LDO**
  - Resolution / Sampling 특성
  - 저노이즈 전원 품질
  - PSRR / Ripple / Dropout 특성 고려

---

## 데이터시트 분석 포인트

이 프로젝트에서는 단순히 부품만 선택하는 것이 아니라, 데이터시트를 읽고 설계에 직접 반영하는 과정을 중요하게 다뤘습니다.

중점적으로 분석한 항목:
- Functional Block Diagram
- Pinout 및 Alternate Function
- Electrical Characteristics
- Absolute Maximum Ratings
- Timing Diagram
- Recommended Operating Conditions
- Typical Application Circuit
- Layout Guideline
- Power Supply Decoupling Requirement

이를 통해 단순 연결이 아니라, **부품의 동작 조건과 한계를 이해한 상태에서 회로를 설계**하려고 했습니다.

---

## Power Budget 및 Power Tree 설계

전원 설계는 시스템 안정성과 직결되므로, 각 블록의 전력 소모를 추정하고 Power Tree를 구성하는 방식으로 접근했습니다.

주요 고려 사항:
- 입력 전원 조건 정의
- 각 전원 레일 분리
- DC-DC / LDO 역할 분담
- Bulk Capacitor와 Decoupling Capacitor 배치
- Analog / Digital 전원 간 간섭 최소화
- 부하 변동에 따른 전압 안정성 확보

### 설계 시 고려한 포인트
- **Bulk Capacitor**
  - 저주파 리플 및 순간 부하 대응
- **Decoupling Capacitor**
  - 고주파 전류 루프 최소화
  - IC 전원핀 근처 배치
- **LDO**
  - 아날로그 회로용 저노이즈 전원 확보
- **Ferrite Bead**
  - 고주파 노이즈 격리
- **Power Plane**
  - 각 전원 레일을 안정적으로 분배하기 위한 구조 고려

---

## Ethernet PHY / ESD / EMI 대응 설계

Ethernet 블록은 외부 커넥터를 통해 신호가 들어오므로, 단순 통신 연결뿐 아니라 **ESD/EMI 대응 설계**가 매우 중요했습니다.

주요 설계 내용:
- Ethernet PHY 인터페이스 검토
- PHY 주변 매칭 저항 구성
- RJ45 연결 구조 고려
- TVS 다이오드 적용
- Ferrite Bead / EMI 필터링 구조 검토
- Ground 및 Return Path 고려
- 고속 신호의 품질 유지를 위한 배치/배선 방향성 검토

### 중점 설계 포인트
- 외부 노출 단자 근처에 보호소자 우선 배치
- 고속 신호는 불필요한 굴곡 및 Stub 최소화
- 차동 신호는 Pair 형태로 가깝게 Routing
- 리턴패스가 끊기지 않도록 Ground 구조 고려

---

## Motor Driver / MOSFET 설계

Motor 관련 회로는 일반 디지털 회로보다 전류 소모가 크고 스위칭 시 노이즈와 발열이 크므로 별도로 주의하여 설계했습니다.

주요 검토 항목:
- Load Current
- MOSFET의 Switching 특성
- Body Diode 영향
- Gate Drive Voltage
- 발열 및 Thermal Path
- Heat Sink / Thermal Pad 고려

### 설계 포인트
- 고전류 경로는 넓은 패턴으로 설계
- 전력 Loop를 짧게 유지
- Switching Noise가 아날로그 영역으로 전파되지 않도록 분리
- Thermal Via, Copper Area 확보 고려

---

## ADC / DAC / MIC / LDO 설계

이 보드는 단순 디지털 제어 보드가 아니라, 아날로그 신호 처리까지 포함하는 Mixed-Signal 시스템을 목표로 했습니다.

그래서 ADC / DAC / MIC / LDO 설계 시 다음 내용을 중점적으로 반영했습니다.

- ADC Resolution / Sampling Rate 고려
- DAC 출력 품질 고려
- 마이크 입력 신호 품질 확보
- 아날로그 전원 노이즈 최소화
- LDO를 통한 깨끗한 전원 공급
- 아날로그 Ground와 디지털 Switching Noise 간섭 최소화

### 설계 포인트
- 아날로그 블록은 디지털 고속 스위칭 영역과 물리적으로 분리
- LDO 출력단 및 입력단 캐패시터 조건 반영
- ADC 입력 라인은 가능한 짧고 깨끗하게 배치
- 민감한 신호는 Return Path가 안정적인 영역 위로 Routing

---

## Schematic 설계

전체 Schematic은 기능 블록 단위로 구성했습니다.

예시 블록:
- Main MCU Block
- Power Input / Regulation Block
- Ethernet PHY Block
- Motor Driver Block
- ADC / DAC / MIC Block
- Protection Block
- Debug / Programming Block

설계 시 중요하게 본 항목:
- Power Tree와 Signal Tree를 명확히 분리
- Clock, Reset, Boot, Debug 회로의 안정성 확보
- TVS, Fuse, Ferrite Bead 등 보호 부품 위치 고려
- 회로도 단계에서부터 PCB Layout까지 연결되는 구조를 염두에 둔 Net 구성

---

## 메인 MCU 회로도

아래에는 메인 MCU 중심 회로도 이미지를 삽입할 예정입니다.

> 파일 경로 예시: `docs/images/main_mcu_schematic.png`

<p align="center">
  <img src="docs/images/main_mcu_schematic.png" width="850">
</p>

<p align="center">
  <sub>Main MCU schematic (STM32 core, clock, reset, debug, power decoupling)</sub>
</p>

---

## PCB Layout 설계

PCB Layout은 단순 배선이 아니라, **전원 무결성(PI), 신호 무결성(SI), 리턴패스, EMI/EMC 대응, Mixed-Signal 분리**를 고려하여 설계했습니다.

주요 적용 내용:
- Multi-layer PCB 구조 고려
- Ground Plane / Power Plane 분리
- Analog / Digital 영역 분리
- Decoupling loop 최소화
- 고전류 / 고속신호 / 민감신호 Routing 분리
- Differential Pair Routing 기초 적용
- Thermal Via 및 발열 경로 고려
- Mounting Hole 및 시스템 Ground/Shield 연결 방향 검토

### Layout 설계 포인트

#### 1) Ground / Return Path
- 신호 아래에 연속적인 Ground 참조면 확보
- 리턴 전류가 멀리 우회하지 않도록 배치
- 고속/고주파 신호의 loop area 최소화

#### 2) Analog / Digital 분리
- ADC, DAC, MIC 등 아날로그 블록은 디지털 스위칭 영역과 분리
- 아날로그 전원은 LDO 기반으로 안정성 확보
- Ground 분리 시 불필요한 분단이 아니라, 전류 경로를 고려한 구조 지향

#### 3) Power Integrity
- IC 전원핀 근처 Decoupling Capacitor 배치
- Bulk Capacitor는 전원 유입부 및 부하 변화가 큰 지점에 배치
- 전원 레일별 Plane 혹은 넓은 Copper 영역 활용

#### 4) Protection / EMI
- 외부 커넥터 근처에 TVS 우선 배치
- Ferrite Bead로 노이즈 분리
- 필요 시 Via Stitching을 통해 Ground 연속성 강화

#### 5) Differential / High-Speed Signal
- 차동 Pair는 길이 차이, 간격, 참조면 연속성을 고려
- 규정 임피던스를 목표로 Stack-up과 폭/간격을 조정하는 방향으로 설계
- 불필요한 Layer 변경 최소화
- Layer 변경 시 Return Path 연속성을 고려한 GND Via 보강 검토

---

## PCB 아트웍 결과

아래에는 PCB 아트웍 완료 이미지를 삽입할 예정입니다.

> 파일 경로 예시: `docs/images/pcb_layout_final.png`

<p align="center">
  <img src="docs/images/pcb_layout_final.png" width="850">
</p>

<p align="center">
  <sub>Final PCB artwork (placement, routing, power/ground strategy, mixed-signal partitioning)</sub>
</p>

---

## 3D Board Viewer 검토

PCB 설계 이후 3D Viewer를 통해 실장 구조를 검토했습니다.

주요 확인 항목:
- 부품 간 간섭 여부
- 커넥터 위치 적절성
- 기구물과의 Clearance
- 발열 구조 시각적 검토
- 조립성 및 생산 전 검토

3D Viewer 단계는 단순 미리보기가 아니라,  
실제 제작 전에 **배치 실수, 높이 간섭, 커넥터 방향 문제를 줄이는 중요한 검토 과정**으로 활용했습니다.

---

## 3D Viewer 완성 이미지

아래에는 3D Viewer 기반 완성 보드 이미지를 삽입할 예정입니다.

> 파일 경로 예시: `docs/images/board_3d_view.png`

<p align="center">
  <img src="docs/images/board_3d_view.png" width="850">
</p>

<p align="center">
  <sub>3D board view (component placement, connector orientation, assembly feasibility)</sub>
</p>

---

## 설계 과정에서 중점적으로 학습한 내용

### 1. STM32 MCU 중심 시스템 설계
- MCU 선정 기준
- Peripheral 요구사항 분석
- Clock / Reset / Boot / Debug 회로 구성
- 데이터시트 기반 설계 흐름

### 2. 전원 설계
- Power Budget 산출
- DC-DC / LDO 역할 분담
- Bulk / Decoupling Capacitor 활용
- Power Plane과 전원 분배 구조 이해

### 3. 보호회로 설계
- TVS Diode
- Fuse
- Ferrite Bead
- ESD / EMI 대응 구조

### 4. Mixed-Signal PCB Layout
- Analog / Digital 분리
- Ground / Return Path 설계
- Plane 구조 및 Via 활용
- Noise Suppression과 Signal Integrity 고려

### 5. 제조 지향 설계
- Schematic → PCB → 3D 검토 → Gerber/BOM 연결
- 실제 제작 가능한 산출물 정리 경험

---

## 수강 및 프로젝트를 통해 얻은 역량

- 고객/시스템 요구사항을 HW Requirement로 해석하는 능력
- System-level HW Block Diagram 작성 능력
- Power Budget 및 Power Tree 설계 기초
- MCU + Ethernet PHY + Analog + Motor 구동을 하나의 보드로 통합하는 설계 경험
- ESD/EMI 대응을 위한 TVS, Ferrite Bead, 보호소자 적용 경험
- ADC/DAC 및 저노이즈 전원 회로 설계 이해
- 4~6 Layer PCB Stack-up 및 Mixed-Signal 영역 분리 개념 이해
- PCB 제조를 위한 Gerber / BOM 산출 경험

---

## 사용 기술 및 설계 키워드

`STM32` `Schematic Design` `PCB Layout` `Mixed-Signal Board` `Ethernet PHY` `Motor Driver` `MOSFET` `ADC` `DAC` `MIC` `LDO` `Power Budget` `Power Tree` `Decoupling Capacitor` `Bulk Capacitor` `TVS` `Ferrite Bead` `ESD` `EMI` `Ground Plane` `Power Plane` `Return Path` `Differential Pair` `Impedance Control` `3D Viewer` `Gerber` `BOM`

---

## 폴더 구조 예시

```bash
STM32-Mixed-Signal-Controller-Board/
├─ README.md
├─ docs/
│  ├─ images/
│  │  ├─ system_block_diagram.png
│  │  ├─ main_mcu_schematic.png
│  │  ├─ pcb_layout_final.png
│  │  └─ board_3d_view.png
│  ├─ datasheets/
│  ├─ reports/
│  └─ bom/
├─ schematic/
├─ pcb/
├─ gerber/
└─ bom/
