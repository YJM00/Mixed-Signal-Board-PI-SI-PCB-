# STM32 Mixed-Signal Controller Board Design Project

> STM32 기반의 Mixed-Signal Embedded Controller Board를 설계한 프로젝트입니다.  
> Requirement Sheet 분석부터 부품 선정, Power Budget 산출, Main Controller MCU 및 Debugger MCU 회로 설계, Ethernet PHY / Motor Driver / ADC·DAC·MIC / LDO 설계, Mixed-Signal PCB Layout, 3D Viewer 검토까지 전체 흐름을 정리했습니다.

---

## 프로젝트 개요

이 프로젝트는 단순한 STM32 최소 시스템 보드 설계를 넘어서,  
**메인 제어용 MCU와 디버깅용 MCU를 포함한 시스템 수준의 보드 설계**를 목표로 진행한 프로젝트입니다.

보드에는 다음과 같은 주요 블록이 포함됩니다.

- **Main Controller MCU**
- **Debugger MCU**
- **Power Tree / Power Budget**
- **Ethernet PHY**
- **Motor Driver / MOSFET**
- **ADC / DAC / MIC 아날로그 인터페이스**
- **LDO 기반 저노이즈 전원부**
- **Mixed-Signal PCB Layout**
- **ESD / EMI / Return Path / Ground Structure 고려**

즉, 이 프로젝트는 단순 회로 작성이 아니라  
**System-level Requirement를 기반으로 실제 제작 가능한 형태의 STM32 Mixed-Signal 보드를 설계한 HW 설계 프로젝트**입니다.

---

## 프로젝트 목표

- STM32 기반 메인 컨트롤 보드 설계 경험 확보
- Main Controller MCU와 Debugger MCU를 포함한 시스템 구조 이해
- Requirement Sheet를 바탕으로 실제 부품 선정 및 회로 설계 수행
- Power Budget 및 Power Tree 설계 경험 확보
- Ethernet / Motor / Analog / Power 블록이 공존하는 Mixed-Signal 보드 설계 경험 축적
- PCB Layout과 3D Viewer 검토까지 포함한 제조 지향 설계 흐름 정리

---

## 개발 환경

- **설계 툴**: `KiCad / Altium Designer / 기타 사용 툴명`
- **Main MCU**: `STM32Fxxx / STM32Hxxx / 사용 MCU 기입`
- **Debugger MCU**: `STM32F103 / ST-LINK 계열 / 사용 MCU 기입`
- **프로젝트 기간**: `YYYY.MM ~ YYYY.MM`

---

## 전체 설계 흐름

1. Requirement Sheet 분석  
2. 최종 선정 부품 정리  
3. Power Budget 및 Power Tree 설계  
4. Debugger MCU 설계  
5. Ethernet PHY 설계  
6. Motor Driver 설계  
7. ADC / DAC / MIC 설계  
8. LDO 설계  
9. Mixed-Signal PCB Layout 설계  

---

## 1. Requirement Sheet 분석

업체는 PLC제조사로서 센서신호처리 + 모터제어 + 이더넷 기능이 포함된 보드를 요구하였습니다

주요 분석 항목은 다음과 같습니다.

- 메인 MCU 성능 및 필요한 Peripheral
- Debugger MCU 성능 및 디버깅 인터페이스 구성
- Ethernet PHY 칩 구성
- ADC / DAC / MIC 인터페이스 구성
- DC Motor 구동 모터드라이버 2개 구성
- SMPS, LDO , DC-DC 레귤레이터로 전원레일 구성 
- EMI / ESD 대응 필요성

---

## 2. 최종 선정 부품

Requirement를 바탕으로 각 기능 블록에 필요한 주요 부품을 선정했습니다.  
선정 시에는 데이터시트의 Functional Block Diagram, Electrical Characteristics, Recommended Operating Conditions, Application Circuit 등을 중점적으로 참고했습니다.

### 주요 선정 부품 고려 사항

- **Main Controller MCU: STM32F407**
  - Core 성능
  - Flash / RAM 용량
  - Peripheral 개수 (UART, SPI, I2C, ADC, DAC, Ethernet 등)
  - 입력 전원전압

- **Debugger MCU: STM32F103**
  - SWD / JTAG 기반 디버깅 연결
  - Main MCU 다운로드 및 디버그 지원
  - 메인 컨트롤러보단 저성능 MCU 활용

- **Ethernet PHY: DP838261RHBR**
  - MII 프토코콜 기준 통신 속도
  - Main MCU와의 인터페이스 호환성

- **UART to USB: CH340C**
  - PC<->MCU 간 시리얼 디버깅을 위해
  - UART신호를 USB신호로 변환

- **ADC:ADS122C04IPW / DAC:CS43L22 / MIC:IMP34DT05**
  - 요구 Resolution / Sampling Rate 고려
  - 입력전압, 기준 전압
  - DNL, SNR 
    
- **Motor DriverX2: DRV8701E /MOTOR: Burshed DC-Motor**
  - 구동 모터 타입에따른 드라이버 선정
  - 구동 전압, 전류
 
- **MOSFET:DMHT6016LFJ**
  -4개의 MOSEFET 으로 구성된 H-브리지 회로
  -최대허용 Vds, Vgs, Id 여유를 두어 선택
  -낮은 Rds(ON)저항 선택
  
 **ESD TVS DIODE: ESD3V3D9**
  - 커패시턴스: 고속신호품질 저하방지로 작은 커패시턴스 선정
  - Working Voltage/ Breakdown Voltage
    
 **Ferrite Bead: MPZ1608**
  - 임피던스: 차단하고자하는 고주파수대역의 임피던스값 최대
  - Rdc: 발열요소 이므로 최소로
  - 전류 정격: 흐르는 전류보다 여유를 두어 크게
  - 임피던스-주파수곡선: 낮은주파수대역에서의 임피던스도 고려

- **SMPS: RS-100**
  - 출력 전류/전압: Power Budget을 참고하여 마진을 두어 결정
  - 효율이 좋은 PS 선정
  - 보호기능 탑재 여부

- **DeCoupling CAP: MLCC**
  - Dropout Voltage
  - Output Current
  - PSRR
  - Noise / Ripple 특성

- **Bulk CAP: MHS**
  - Dropout Voltage
  - Output Current
  - PSRR
  - Noise / Ripple 특성

- **LDO: LD1117**
  - 권장 입출력 캐패시터 용량/ESR 고려
  - 전류에따른 Dorp-out 특성 고려
  - 주파수에따른 PSRR
  - 발열 P=(VIN-VOUT)I 

***공통적으로 필요한 요구성능에 부합하고 COST를 고려하여 선정하였습니다
---

## 3. Power Budget 및 Power Tree 설계

전체 시스템의 안정성을 확보하기 위해 각 블록의 소비 전력을 가정/분석하고, 이를 기반으로 Power Budget과 Power Tree를 구성했습니다.

### 주요 고려 항목

- 입력 전원 조건 정의
- Main MCU / Debugger MCU 전원 분리 여부
- Ethernet PHY 전원 요구사항
- Motor Driver 전원 요구사항
- ADC / DAC / MIC 아날로그 전원 품질
- LDO와 DC-DC의 역할 분담
- 각 전원 레일별 디커플링 및 벌크 캐패시터 배치

### 설계 시 반영한 포인트

- **Bulk Capacitor**
  - 순간 부하 변화 대응
  - 저주파 리플 완화

- **Decoupling Capacitor**
  - 고주파 전류 루프 최소화
  - IC 전원핀 근접 배치

- **Power Plane / Wide Copper**
  - 전원 분배 안정성 확보
  - 고전류 라인의 전압 강하 최소화

- **Analog / Digital Power 분리**
  - 민감한 아날로그 회로에 저노이즈 전원 공급
  - 디지털 스위칭 노이즈 간섭 저감

---

## 4. Debugger MCU 설계

이 보드는 메인 제어용 MCU 외에도,  
다운로드 및 디버깅을 위한 **Debugger MCU** 구조를 포함하도록 설계했습니다.

Debugger MCU 블록은 다음과 같은 목적을 갖습니다.

- Main MCU 프로그램 다운로드
- 디버깅 인터페이스 제공
- 개발 단계에서의 Bring-up 편의성 향상
- 보드 단독 디버그 환경 구성

### 주요 설계 항목

- SWD / JTAG 연결 구조
- Reset 및 Boot 관련 제어
- Main MCU와의 디버그 인터페이스 연결
- 전원 공급 및 디커플링
- 디버그 커넥터 연결 구조

### 설계 포인트

- 메인 MCU와 디버거 MCU 간 신호 연결을 명확히 분리
- 디버그 라인의 길이와 배치를 과도하게 복잡하게 만들지 않도록 구성
- 디버깅 중 Main MCU 전원 안정성이 깨지지 않도록 전원 구조 고려
- Bring-up 단계에서 접근이 쉽도록 디버그 포트 위치 고려

---

## 5. Ethernet PHY 설계

Ethernet 블록은 외부와 연결되는 대표적인 고속 통신 블록이므로,  
신호 연결뿐 아니라 **보호회로, 노이즈 억제, 리턴패스, 배치 전략**까지 함께 고려했습니다.

### 주요 설계 항목

- Main MCU와 Ethernet PHY 인터페이스 구조
- PHY 주변 저항 및 기본 수동소자 구성
- RJ45 연결 방향성
- ESD / EMI 보호회로 적용
- 고속 신호 Routing 고려

### 중점 설계 포인트

- 외부 커넥터 근처에 TVS 등 보호소자 우선 배치
- 불필요한 Stub와 과도한 굴곡 최소화
- 차동 신호는 Pair 형태로 인접 Routing
- Return Path가 끊기지 않도록 Ground 참조면 유지
- PHY와 RJ45 사이의 배치를 단순하고 짧게 유지하도록 고려

---

## 6. Motor Driver 설계

Motor Driver 블록은 상대적으로 큰 전류와 스위칭 노이즈를 다루므로,  
디지털 제어 회로와는 다른 관점에서 설계했습니다.

### 주요 설계 항목

- Load Current
- MOSFET / Driver 소자 선택
- Switching 특성
- Gate Drive 조건
- 발열 및 Thermal 대응
- 고전류 경로 Routing

### 설계 포인트

- 고전류 경로는 가능한 넓은 패턴으로 설계
- Switching Loop 면적 최소화
- Digital / Analog 영역으로 노이즈가 전파되지 않도록 배치 분리
- Thermal Pad / Thermal Via / Copper Area 확보를 고려
- Motor 구동 전류와 제어 신호 Routing을 구분하여 설계

---

## 7. ADC / DAC / MIC 설계

이 보드는 디지털 제어뿐 아니라 아날로그 신호 처리 블록도 포함하므로,  
ADC / DAC / MIC 회로는 신호 품질 중심으로 설계했습니다.

### 주요 설계 항목

- ADC Resolution 및 입력 신호 경로
- DAC 출력 품질
- MIC 입력 신호 처리
- 민감 신호와 스위칭 노이즈 분리
- 아날로그 회로 전원 품질 확보

### 설계 포인트

- ADC 입력 라인은 가능한 짧고 깨끗하게 유지
- DAC 및 MIC 관련 라인은 디지털 스위칭 영역과 물리적으로 분리
- 아날로그 블록 아래 참조 GND의 연속성 확보
- 리턴패스를 고려하여 민감 신호가 불연속한 참조면 위를 지나지 않도록 배치
- 아날로그 입력부는 외부 노이즈 유입 경로를 최소화하도록 구성

---

## 8. LDO 설계

LDO 블록은 아날로그 회로와 민감한 신호 블록에 안정적이고 깨끗한 전원을 공급하기 위해 설계했습니다.

### 주요 설계 항목

- 입력 전압과 출력 전압 조건
- Dropout Voltage
- Output Current
- PSRR
- Output Ripple / Noise
- 데이터시트 권장 입출력 캐패시터 조건

### 설계 포인트

- ADC / DAC / MIC 등 민감한 블록에 저노이즈 전원 공급
- LDO 입력단과 출력단 캐패시터를 데이터시트 권장 조건에 맞게 배치
- LDO 출력 전원은 디지털 고전류 스위칭 경로와 분리
- 전원 품질이 중요한 블록 근처에 배치하여 경로를 짧게 유지

---

## Main MCU Schematic

이 프로젝트의 메인 제어부는 **Main Controller MCU + Debugger MCU** 구조를 갖도록 설계했습니다.  
아래에는 메인 MCU 중심 회로도 이미지를 삽입할 예정입니다.

> 이미지 파일 예시 경로: `docs/images/main_mcu_schematic.png`

<p align="center">
  <img src="docs/images/main_mcu_schematic.png" width="900">
</p>

<p align="center">
  <sub>Main MCU schematic including core power, clock, reset, boot, debug interface, and peripheral connections</sub>
</p>

---

## 9. Mixed-Signal PCB Layout 설계

PCB Layout 단계에서는 단순 연결이 아니라,  
**SI / PI / Return Path / EMI / Mixed-Signal Partitioning**을 종합적으로 고려했습니다.

### 주요 설계 목표

- Digital / Analog / Power / Motor 영역의 분리
- Ground Plane / Power Plane 구조 최적화
- 리턴 전류 경로 안정화
- 디커플링 루프 최소화
- 민감 신호와 고전류/고속 신호 분리
- 고속 차동 신호의 참조면 연속성 확보
- 제조 가능성과 조립성을 고려한 부품 배치

### Layout 설계 포인트

#### 1) Ground / Return Path
- 신호 아래 연속적인 Ground 참조면 확보
- 리턴 전류가 멀리 우회하지 않도록 배치
- 고속/고주파 신호의 Loop Area 최소화

#### 2) Analog / Digital 분리
- ADC / DAC / MIC 등 민감한 블록은 디지털 스위칭 영역과 물리적으로 분리
- 아날로그 전원은 LDO 기반으로 공급
- GND를 무조건 쪼개기보다, 실제 전류 경로를 고려한 구조 지향

#### 3) Power Integrity
- 각 IC 전원핀 근처 Decoupling Capacitor 배치
- 전원 유입부와 부하 변화가 큰 영역에 Bulk Capacitor 배치
- 전원 레일은 Plane 또는 넓은 Copper로 안정적 분배

#### 4) EMI / Protection
- 외부 커넥터 근처 보호소자 우선 배치
- Ferrite Bead를 통한 고주파 노이즈 분리
- 필요 시 Via Stitching으로 Ground 연속성 강화

#### 5) Differential / High-Speed Signal
- 차동 Pair는 길이 차이와 간격을 고려하여 Routing
- 규정 임피던스를 목표로 Stack-up, 폭, 간격을 조정하는 방향으로 설계
- Layer 변경은 최소화
- Layer 변경 시 GND Via를 통한 Return Path 보강 검토

#### 6) Mounting Hole / Shield / Chassis 고려
- 마운팅홀을 시스템 Ground 또는 Chassis/Earth 전략과 연계 검토
- 실드/케이스와 연결될 경우 ESD 및 고주파 노이즈 방출 경로를 고려한 구조 검토

---

## PCB 아트웍 완료 이미지

아래에는 PCB 아트웍 완료 이미지를 삽입할 예정입니다.

> 이미지 파일 예시 경로: `docs/images/pcb_layout_final.png`

<p align="center">
  <img src="docs/images/pcb_layout_final.png" width="900">
</p>

<p align="center">
  <sub>Final PCB artwork showing placement, routing, power distribution, and mixed-signal partitioning</sub>
</p>

---

## 3D Board Viewer 검토

PCB 설계 이후 3D Viewer를 통해 실제 조립성과 기구적 간섭 가능성을 검토했습니다.

### 주요 확인 항목

- 부품 높이 및 간섭 여부
- 커넥터 방향 및 접근성
- 기구물과의 Clearance
- 발열 구조의 시각적 검토
- 조립 전 부품 배치의 현실성 확인

3D Viewer 검토는 단순한 미리보기가 아니라,  
실제 제작 전에 **실장 오류 가능성을 줄이고 Rework 리스크를 낮추기 위한 단계**로 활용했습니다.

---

## 3D Viewer 완성 이미지

아래에는 3D Viewer 기반 완성 보드 이미지를 삽입할 예정입니다.

> 이미지 파일 예시 경로: `docs/images/board_3d_view.png`

<p align="center">
  <img src="docs/images/board_3d_view.png" width="900">
</p>

<p align="center">
  <sub>3D board view for assembly feasibility, component placement validation, and connector orientation check</sub>
</p>

---

## 프로젝트를 통해 학습한 핵심 내용

### System-level HW 설계
- Requirement를 HW 설계 항목으로 변환하는 방법
- 기능 블록 단위의 시스템 보드 구성

### 부품 선정 및 데이터시트 해석
- MCU / PHY / MOSFET / LDO / 보호소자 선정 기준
- Functional Block Diagram / Electrical Characteristics / Pinout 중심 데이터시트 분석

### 전원 설계
- Power Budget 산출
- Power Tree 구성
- Bulk / Decoupling Capacitor 역할 구분
- 저노이즈 전원 설계 방향 이해

### Mixed-Signal PCB 설계
- Analog / Digital 분리
- Ground / Return Path 설계
- Differential Pair 및 고속 신호 고려
- ESD / EMI 대응용 부품 배치 전략

### 제조 지향 설계
- Schematic → PCB Layout → 3D Viewer → Gerber / BOM으로 이어지는 흐름 경험

---

## 사용 기술 및 키워드

`STM32` `Main MCU` `Debugger MCU` `Schematic Design` `Power Budget` `Power Tree` `Ethernet PHY` `Motor Driver` `MOSFET` `ADC` `DAC` `MIC` `LDO` `Mixed-Signal PCB` `Ground Plane` `Power Plane` `Return Path` `Differential Pair` `Impedance Control` `TVS` `Ferrite Bead` `ESD` `EMI` `Thermal Via` `3D Viewer` `Gerber` `BOM`

---

## 폴더 구조 예시

```bash
STM32-Mixed-Signal-Controller-Board/
├─ README.md
├─ docs/
│  ├─ images/
│  │  ├─ main_mcu_schematic.png
│  │  ├─ pcb_layout_final.png
│  │  └─ board_3d_view.png
│  ├─ reports/
│  ├─ datasheets/
│  └─ bom/
├─ schematic/
├─ pcb/
├─ gerber/
└─ bom/
