# MCU Board Design Project


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

## 시스템 구성도
<img width="896" height="739" alt="image" src="https://github.com/user-attachments/assets/e6b512b4-6f4d-46a0-933b-508aa60fc313" />

---
## 전원 흐름
<img width="1050" height="759" alt="image" src="https://github.com/user-attachments/assets/c0da8177-1341-4c51-b305-8165a2e5dd81" />

---
## 설계 흐름

1. Requirement Sheet 분석  
2. 최종 선정 부품 정리  
3. Power Budget 및 Power Tree 설계  
4. Debugger MCU 설계  
5. Ethernet PHY 설계  
6. Motor Driver 설계  
7. ADC 설계
8. DAC / MIC 설계    
9. SMPS,LDO 설계
10. Main MCU Schematic 설계
11. Mixed-Signal PCB Layout 설계  

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

### 부품 선정시 고려 사항

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
 
   **MOSFET:DMHT6016LFJ**
  -4개의 MOSEFET 으로 구성된 H-브리지 회로
  -최대허용 Vds, Vgs, Id 여유를 두어 선택
  -낮은 Rds(ON)저항 선택
  
- **ESD TVS DIODE: ESD3V3D9**
  - 커패시턴스: 고속신호품질 저하방지로 작은 커패시턴스 선정
  - Working Voltage/ Breakdown Voltage/ Clamping Voltage
    
- **Ferrite Bead: MPZ1608**
  - 임피던스: 차단하고자하는 고주파수대역의 임피던스값 최대
  - Rdc: 발열요소 이므로 최소로
  - 전류 정격: 흐르는 전류보다 여유를 두어 크게
  - 임피던스-주파수곡선: 낮은주파수대역에서의 임피던스도 고려

- **SMPS: RS-100**
  - 출력 전류/전압: Power Budget을 참고하여 마진을 두어 결정
  - 효율이 좋은 PS 선정
  - 보호기능 탑재 여부

- **DeCoupling CAP: MLCC**
  - 작은 ESL,ESR을 가지도록
  - SRF 고려
  - 정격 전압: MLCC는 정격전압 가까이 걸렸을떄 용량이 작아진다

- **Bulk CAP: MHS**
  - 리플전류정격 고려
  - CV=IT 공식 적용
  - LOW ESR

- **LDO: LD1117**
  - 권장 입출력 캐패시터 용량/ESR 고려
  - 전류에따른 Dorp-out 특성 고려
  - 주파수에따른 PSRR
  - 발열 P=(VIN-VOUT)I 

***공통적으로 필요한 요구성능에 부합하고 온도범위와 COST를 고려하여 선정하였습니다
---

## 3. Power Budget 작성

선정한 부품을 바탕으로 Datasheet를 참고하여 평균전력, 피크전력을 구하여 전체 시스템의 전력요구를 정리하였습니다

### 주요 고려 항목

- 시스템의 피크전력 기준으로 최소 20~30% 마진들 두어 SMPS를 선정
- DataSheet의 AMR(Absolute Maximum Ratings)을 참고
- 전력효율최적화: DVFS, Power Gating 기법 적용
- 방열대책: 히트싱크, 냉각팬적용

---

## 4. Debugger MCU 설계
<img width="1407" height="756" alt="image" src="https://github.com/user-attachments/assets/b35b9acb-7d3f-41ec-b4ed-34696610ee52" />

이 보드는 메인 제어용 MCU 외에도,  다운로드 및 디버깅을 위한 **Debugger MCU** 구조를 포함하도록 설계했습니다.

### 주요 설계 항목

- 외부 디버거 활용위한 4x1핀헤더
- Reset 및 Boot 관련 제어
- Main MCU와의 디버그 인터페이스 연결
- 3.3V전원 공급 및 디커플링
- 외부 크리스탈과 부하캡

---

## 5. Ethernet PHY 설계
<img width="1385" height="786" alt="image" src="https://github.com/user-attachments/assets/fabacced-9e6e-4a69-aa2e-210f615b2c5f" />

Ethernet 블록은 외부와 연결되는 대표적인 고속 통신 블록이므로,  
신호 연결뿐 아니라 **보호회로, 노이즈 억제, 리턴패스**까지 함께 고려했습니다.

### 주요 설계 항목

- Main MCU와 Ethernet PHY 인터페이스 구조
- 아날로그 전원입력에 비드와 디커플링캡 구성
- RJ45 커넥터 TVS다이오드 적용
- 외부 크리스탈과 부하캡
- RJ45로 들어오는 고주파노이즈를 Earth접지로 우회

---

## 6. Motor Driver 설계
<img width="1214" height="685" alt="image" src="https://github.com/user-attachments/assets/af6982d9-aeb7-4880-8144-0c2e695f5415" />


### 주요 설계 항목

- 차지펌프를 위한 외부 캡
- 12V 전원쪽 고전류를 위한 벌크캡과 전압 안정화를 위한 디커플링캡 
- H-브리지 회로의 드레인에 12V 전압 공급

---

## 7. ADC
<img width="1163" height="500" alt="image" src="https://github.com/user-attachments/assets/a3825735-e6d8-487a-97c1-40d54c2ccc79" />


### 주요 설계 항목

- Active Low Reset핀을 위한 풀업저항
- MCU와 I2C통신을 위한 SDA/SCL핀 풀업저항
- 디지털전원 디커플링캡, 아날로그전원 비드와 디커플링캡
- 센서 입력핀에 RC LPF 구성
- 외부센서 연결을 위한 4X1 SPADE 

---

 ## 8. DAC / MIC 설계
 <img width="1042" height="540" alt="image" src="https://github.com/user-attachments/assets/09abe9f0-5444-422f-893b-2b3f759aed6f" />
 
### 주요 설계 항목

- 디지털전원 디커플링캡, 아날로그전원 비드와 디커플링캡
- 4X1 이어폰잭에 RC LPF 필터 구성
- 스피커 연결을 위한 4X1핀헤더 구성 
- 민감 신호와 스위칭 노이즈 분리
- 디지털 출력 마이크 구성


## 9. SMPS,LDO 설계
<img width="1272" height="675" alt="image" src="https://github.com/user-attachments/assets/68ae5105-8fca-4ecd-a7d2-847b9c30a52b" />
SMPS는 240V 60Hz AC입력으로부터 DC정류후 보드에 12V전원을 안정적으로 출력하는 목적입니다.
LDO 블록은 SMPS입력을 받아 아날로그 회로와 민감한 신호 블록에 안정적이고 깨끗한 전원을 공급하기 위해 설계했습니다.

### 주요 설계 항목

- 12V 7.66A 고전류 SMPS를 위한 벌크캡
- 금속 케이스와 나사로 연결되는 마운팅홀에 Earth접지
- LDO 전원쪽 역극성 보호를 위한 쇼트키 다이오드
- LDO 출력쪽 출력 클램프를 위한 제너 다이오드
- 입력 출력쪽 전압안정화를 위한 디커플링 캡

---

## 10. Main MCU Schematic
<img width="1076" height="608" alt="image" src="https://github.com/user-attachments/assets/edc99153-97da-4c93-af26-3a4234631952" />

### 주요 설계 항목

- 디지털전원 디커플링캡, 아날로그전원 비드와 디커플링캡
- 각 IC의 글로벌 라벨과 연결
- 부팅모드를 설정하는 BOOT0/1핀 풀다운
- 외부 장치와 UART, CAN 통신을 위한 4X1 Berg strip
- 외부 클럭 사용을 위한 외부 크리스탈 로드캡 
---

## 11. Mixed-Signal PCB Artwork 설계

PCB Artwork 단계에서는 단순 연결이 아니라,  
**SI / PI / Return Path / EMI / Mixed-Signal**을 종합적으로 고려했습니다.

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

#### 3) Power Integrity
- 각 IC 전원핀 근처 Decoupling Capacitor 배치
- 전원 유입부와 부하 변화가 큰 영역에 Bulk Capacitor 배치
- 전원레일은 직접 선으로 연결x Power plane을 거쳐서 전원 분배

#### 4) EMI / Protection
- 외부 커넥터 근처 보호소자 우선 배치
- Ferrite Bead를 통한 고주파 노이즈 분리
 
#### 5) Via Stitching
- 리턴전류의 지름길을 제공하여 짧을 리턴패스 제공
- GND와 GND PLANE POWER와 POWER PLANE을 연결하여 안정성 제공

#### 6) Differential / High-Speed Signal
- 고속신호의 규정임피던스를 준수
- 규정 임피던스에 맞게 트레이스 폭/넓이, 차동선 사이 간격, plane 간격, PCB재료의 유전율 

#### 7) Mounting Hole / Shield / Chassis 고려
- 마운팅홀을 시스템 Ground 또는 Chassis/Earth 전략과 연계 검토
- 실드/케이스와 연결될 경우 ESD 및 고주파 노이즈 방출 경로를 고려한 구조 검토

---

## PCB 아트웍


<p align="center">
  <img width="832" height="815" alt="image" src="https://github.com/user-attachments/assets/89f1bc82-b259-4760-a914-41cc8bac1c4c" />
</p>


---

## 3D Viewer 

<p align="center">
  <img width="701" height="702" alt="image" src="https://github.com/user-attachments/assets/0655c049-84ff-4f70-9bd2-adc593d9f064" />
</p>

---


