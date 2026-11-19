## 생체신호 기반 제어 시스템을 만드는 임베디드 펌웨어 · 하드웨어 엔지니어 지망생입니다

전자공학과 4학년입니다. 사람의 신호를 측정해 MCU에서 판단하고, 그 결과로 기기를 움직이는 시스템에 관심이 많습니다.
졸업작품 ReHand에서 표면 근전도 수집 → 신호처리 → MCU 위 추론 → 의수 제어까지를 만들었고, PC에서 만든 결과가 실제 보드에서도 같게 동작하는지 확인하는 과정을 중요하게 생각합니다.
그 밖에 MCU 주변장치 제어와 PCB·기구 설계(POV Display), TTL 논리회로와 아날로그 회로 제작을 해 왔습니다.

- **관심 분야**: 생체신호 측정과 처리, 실시간 임베디드 제어, MCU 기반 추론
- **관심 산업**: 의료기기, 웨어러블·재활 로봇, 로보틱스

---

### Projects

#### [ReHand — EMG 기반 의수 제어](https://github.com/qetad13/rehand-emg-prosthesis)
<sub>졸업작품 · 2026 · 4인 팀 팀장 · 근전도 센서 파트 담당</sub>

사용자의 전완부 근전도로 의수 손가락을 하나씩 움직이는 재활 보조기기. 표면 근전도 4채널을 XIAO ESP32-S3로 1 kHz 샘플링하고, 보드 위에서 직접 CNN 추론으로 손가락 동작 8가지를 분류해 ESP-NOW로 의수에 명령을 보냅니다.

- 생체신호 품질 관리: 60 Hz 전원 잡음 제거, 전극 접촉 불량 세션 자동 제외, 채널별 잡음·반응 점검 모드, 사용자별 보정 기능 구현(v2)
- 근전도 보드 PCB(KiCad, v1·v2), PySide6 수집 GUI, PyTorch 학습 → 가중치·필터 계수를 C 헤더로 자동 생성
- 듀얼코어 분리: 1 kHz 샘플링(Core 1)과 FreeRTOS 추론·버튼·전송 태스크(Core 0), 녹화용 16k 샘플 링 버퍼
- PC와 같은 계산을 보드에서 재현: 인과 IIR 필터(SOS) 계수 공유, 골든 테스트 벡터로 PC ↔ ESP32 결과 비교
- 결과 (v1, 한 사람·한 착용 상태 녹화 데이터의 **PC 오프라인 평가**): 정확도 96.4%, 제어 지연 중앙값 356 ms. ESP32 골든 테스트 8/8 통과, 실제 의수 연동은 데모 영상으로 확인
- v2(스트리밍 특징 + TCN)는 같은 데이터 PC 평가 98.7% / 226 ms, 펌웨어 C 코드 ↔ PyTorch 대조까지 완료. **실제 보드·의수 시험은 아직 하지 않음**

`ESP32-S3` `FreeRTOS` `C/C++` `ESP-NOW` `PyTorch` `KiCad`

#### [ATmega128 POV Display](https://github.com/qetad13/atmega128-pov-display)
<sub>마이크로프로세서 수업 · 2025 · 4인 팀 팀장 · 회로·PCB·펌웨어·기구 담당</sub>

회전하는 RGB LED 16개로 글자와 그림을 그리는 잔상 디스플레이.

- 74HC595 6개 데이지 체인으로 LED 48채널을 GPIO 3개(SER · SRCLK · RCLK)로 구동, 래치로 한 번에 출력
- 외부 인터럽트 INT0~3로 색·밝기·패턴 선택, Timer0 Fast PWM + L298N 모터 속도 제어
- MCU와 시프트 레지스터를 한 장에 넣은 2층 LED 보드 PCB 설계·제작, 회전판·지지대 3D 모델링
- 한계: 회전 위치 센서가 없어 이미지 위치를 모터 속도로 수동 조정

`AVR C` `ATmega128` `74HC595` `KiCad` `Fusion 360`

#### [8-Bit BCD Adder/Subtractor](https://github.com/qetad13/8bit-bcd-adder-subtractor)
<sub>디지털 회로 수업 · 2025 · 5인 팀 팀장 · 회로 설계·제작·디버깅 담당</sub>

74LS 시리즈 TTL IC 12개로 만든 두 자리 BCD 가감산기. XOR 보수 + Cin으로 2의 보수 감산, 조합 논리 + 멀티플렉서로 자리별 BCD 보정.
Multisim 시뮬레이션 → 브레드보드 → 만능기판 납땜까지 진행하며 입력 플로팅, 시뮬레이션과 실물 차이, 자리 올림 보정 누락 문제를 디버깅.

`TTL Logic` `Multisim`

#### [Auto Plant Grower](https://github.com/qetad13/analog-auto-plant-system)
<sub>아날로그 회로 수업 · 2025 · 5인 팀 · 외관 3D 모델링, 회로 구성·측정 보조</sub>

MCU 없이 Op-Amp(LM741) 비교기와 NE555만으로 급수·조명·환기를 자동 제어하는 재배기. 센서 출력을 오실로스코프로 측정해 비교기 기준 전압을 정함.

`Op-Amp` `NE555` `Fusion 360`

---

### Tech Stack

프로젝트에서 직접 사용한 것만 적었습니다.

| 분야 | 사용 경험 |
|---|---|
| MCU / 펌웨어 | ESP32-S3 (Arduino-ESP32, FreeRTOS 태스크 · 코어 고정 · 태스크 알림 · 임계 구역), ATmega128 (GPIO, 외부 인터럽트, 타이머 PWM) |
| 언어 | C, C++, Python |
| 통신 | ESP-NOW, UART(USB 시리얼 데이터 수집), 74HC595 직렬 시프트(GPIO 비트뱅잉) |
| 신호처리 · ML | IIR 필터 설계(SciPy), STFT, PyTorch 소형 CNN/TCN 학습과 C 코드 이식 |
| 하드웨어 | KiCad PCB 설계(2층), 74LS TTL 논리회로, Op-Amp · NE555 회로, 브레드보드 · 만능기판 납땜 |
| 기구 · 도구 | Fusion 360, 3D 프린팅, Multisim, 오실로스코프 |

아직 프로젝트로 다루지 않았지만 다음으로 공부하려는 기술: STM32(HAL·레지스터 수준), SPI · I2C · CAN 통신.

---

### Contact

| 항목 | 내용 |
|---|---|
| 소속 | 전자공학과 |
| 이메일 | [kjoonh1@gmail.com](mailto:kjoonh1@gmail.com) |
