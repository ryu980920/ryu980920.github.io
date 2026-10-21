---
layout: default
title: "2026 Chip Master Project | 28 nm FD-SOI SPAD"
---

# 2026 Chip Master Project — 28 nm FD-SOI SPAD Co-Design

**Research topic:** 940 nm dToF LiDAR용 28 nm FD-SOI SPAD의 광학–공정 공동설계  
**Period:** 2026.08–Present  
**Type:** 숭실대학교 차세대반도체 Chip Master Project · 2인 공동연구  
**My role:** **B Track — SProcess 기반 공정 프로파일 보정, TCAD 시뮬레이션 및 DOE 분석**  
**Tools:** Synopsys Sentaurus SDE, SProcess, SNMesh, SDevice, SVisualPy, Sentaurus Workbench, Python  
**Status:** In Progress · **2026.10.21 연구기록 기준**

[← 포트폴리오 홈으로](../) · [공동 연구 기록 저장소](https://github.com/jujushmaterial/2026-chip-master-project-record) · [개인 연구일지](https://github.com/jujushmaterial/2026-chip-master-project-record/tree/main/members/ryu980920/timeline)

---

## 1. 프로젝트 개요 — 공통 연구

본 프로젝트는 **940 nm 근적외선을 사용하는 direct Time-of-Flight(dToF) LiDAR용 SPAD(Single-Photon Avalanche Diode)**를 대상으로 한다. 원거리·저반사율 환경에서는 검출 가능한 광자 수가 제한되므로 광생성 효율을 높여야 하지만, 동시에 주변부 조기 항복(PEB)과 dark event 위험을 고려해야 한다.

산업적 출발점은 CES 2026에서 소개된 dToF 센서의 요구사항이다. 그러나 본 연구는 특정 상용 센서의 내부 구조나 양산 레시피를 복원하는 프로젝트가 아니다. **Gao et al.(2024) 등의 공개 문헌을 기준으로 28 nm FD-SOI quasi-circular SPAD의 대체 기준 구조(literature-constrained surrogate baseline)를 설정**하고, 광학과 공정 관점에서 각각 개선 가능성을 검토하는 공동연구다.

### 공통 기준 구조와 연구 방향

- **소자:** 28 nm FD-SOI 기반 P-Well(PW) / Deep N-Well(DNW) 접합 SPAD
- **구조적 특징:** BOX, 상부 얇은 Si층, active-zone STI 및 peripheral STI를 포함하는 quasi-circular reference
- **공통 출발점:** SDE 기준 구조 및 analytic doping profile을 바탕으로 한 광학·전기 비교 기준 설정
- **비교의 원칙:** 공개되지 않은 실제 STMicroelectronics 공정 수치는 추정해 '실제 공정'이라고 주장하지 않으며, surrogate 가정과 문헌상의 기준값을 구분

| 연구 구분 | 담당 영역 | 핵심 연구 질문 |
|---|---|---|
| **A Track — 팀원** | 소자 구조·광학 설계 | 기존 square Si/STI pattern을 annular pattern으로 확장했을 때, 940 nm에서 유효 photogeneration이 향상되는가? |
| **B Track — 본인** | 공정·전기 TCAD | SDE 기준 도핑 분포를 SProcess 공정 변수로 재현하고, 이후 항복·전계·dark-current 관련 특성을 개선할 수 있는가? |
| **공동 후속 단계** | 광학–전기 결합 | A의 공간적 광생성률 `G`와 B의 avalanche triggering probability `ATP`를 결합해 상대적인 dToF 성능 변화를 평가할 수 있는가? |

**현재 아래에 정리한 내용은 B Track에서 직접 수행한 연구 진행 과정**이다. A의 광학 연구 결과나 최종 공동 시스템 개선 결과를 본인의 완료 성과로 포함하지 않았다.

---

## 2. 내가 담당한 연구의 핵심 문제

**SDE에서 정의한 도핑 프로파일을 실제 공정 변수(implant energy/dose, RTA)를 사용하는 SProcess에서 얼마나 가깝게 재현할 수 있는가?**

기준 SDE 구조의 도핑 분포를 SProcess로 옮기면, 단순히 주입 Dose 또는 Energy를 하나 바꾸는 것만으로 **Main Peak의 위치·농도, 분포 폭, junction-side tail, deep tail**을 동시에 맞추기 어렵다.

따라서 목표값에 가장 가까운 **단일 SCORE 최소 조건**을 찾는 데서 멈추지 않고, 깊이별 농도곡선을 직접 비교해 부족한 부분의 원인을 추정하고, 필요한 implant component와 DOE 자체를 재설계하는 방식으로 연구를 진행했다.

> **연구의 중심:** Parameter sweep → 수치 비교 → 프로파일 불일치 진단 → 공정 구조 수정 → 새로운 DOE → 실제 PLX 대조

### 사용한 분석 흐름

`공통 SDE 구조/기준 프로파일` → `SProcess (implant + anneal)` → `SNMesh` → `SDevice` → `SVisualPy / DOE 분석`

연구 중기 이후에는 **전기적 성능 최적화에 앞서 SProcess–SDE 도핑 프로파일 정합**을 우선하는 단계별 검증으로 범위를 분리했다. 따라서 아래의 DNW/PW 결과는 우선 **공정 도핑 분포에 대한 결과**이며, 그 자체로 VBD·SCR·DCR 개선을 입증하지 않는다.

---

## 3. 개인 연구 진행 흐름

| 시기 | 단계 | 관찰한 문제 · 판단 | 수행한 변경 및 현재까지의 확인 |
|---|---|---|---|
| **8월 말 ~ 9월 초** | TCAD Baseline 연결 | SDE와 SProcess 사이 좌표·메시 의존성, dark-current/ATP 계산 흐름을 분리할 필요 | SProcess–SNMesh–SDevice–SVisualPy 연결과 진단용 VBD/SCR·feedback ON/OFF 흐름을 점검 |
| **9월 24일** | **Single-DNW · 250조건** | 가장 낮은 점수의 후보도 기준 DNW 폭·접합부 tail에 미달 | LHS 250조건 분석 후 단일 DNW 주입 방식의 현재 탐색범위 한계를 확인하고 Dual-DNW로 전환 |
| **9월 말 ~ 10월 초** | **Dual-DNW · 64 → 108조건** | Main Peak와 얕은 tail 조정 자유도는 늘었으나 1.4–1.8 µm deep tail이 부족 | DNW Main + Shallow Trim 구성, refinement DOE 및 SVisualPy 진단 지표 확장 |
| **10월 6~12일** | **Triple-DNW · 45 → 25 → 81조건** | 깊은 tail을 보완하면 다른 깊이의 과도 농도·분포 폭과 trade-off 발생 | DNW Deep implant 추가, 분포 폭·지점별 tail·SCORE를 분리 비교하고 후속 27조건까지 확장 |
| **10월 15일** | **DNW 후보 선정 · 27조건** | SCORE 최저 조건이 모든 깊이에서 가장 적합하지 않음 | Case 15를 **PW 보정용 임시 DNW 기준**으로 선택. 실제 SDE PLX와 직접 비교해 잔여 오차 확인 |
| **10월 15~20일** | **PW 재설계 · Dual-PW → Deep PW** | 얕은 PW peak를 맞추면 junction-side Boron 부족 또는 두 번째 peak가 발생 | PW 전용 SVisualPy 지표 재설계, Deep PW 29조건과 저Dose 18조건 결과 분석 |
| **10월 20~21일** | **Triple-PW · Fixed + Mid + Deep** | 저Dose Deep만으로는 150~350 nm 분포와 깊이별 잔차를 동시에 조절하기 어려움 | Mid implant를 추가한 18조건 실행·분석. 결과의 한계를 확인하고 고Energy·저Dose Mid 18조건 후속 DOE 설계 |

이 표는 주요 의사결정만 발췌한 것이다. 실제 결과 수, 설계만 완료한 DOE와 후속 실행 여부는 [날짜별 개인 연구일지](https://github.com/jujushmaterial/2026-chip-master-project-record/tree/main/members/ryu980920/timeline)에서 확인할 수 있다.

### 3-1. Single-DNW — 점수를 낮추는 것만으로는 해결되지 않은 문제

9월 24일 분석한 **250조건 LHS**에서 Single-DNW의 가장 넓은 equivalent sigma는 약 **279.7 nm**였지만, SDE 기준은 약 **380.6 nm**였다. DNW Dose를 올리면 일부 오차 지표는 개선되었으나 Peak 농도 증가와 폭 감소가 함께 나타났다. RTA 변경만으로도 이 불일치를 충분히 줄이지 못했다.

여기서 내린 결정은 **현재 탐색 범위의 단일 주입 구조를 계속 미세조정하지 않고, Main Peak와 tail을 분리해서 제어할 수 있도록 공정 모델을 바꾸는 것**이었다.

[근거: 2026-09-24 연구일지](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/members/ryu980920/timeline/2026-09/2026-09-24.md)

### 3-2. Dual-DNW — 얕은 tail을 분리했지만, 깊은 영역이 남았다

DNW를 **Main + Shallow Trim** 두 phosphorus implant component로 나눴다. 실제 초기 64조건을 확인하며 기준 폭에 아직 미달함을 확인했고, 후속 108조건에서도 **deep-side tail 부족**이 남았다.

이를 해결하려면 기존 주입 변수만 넓히는 것보다 깊은 영역을 담당할 별도 component가 필요하다고 판단했다. 이때 Single/Dual/Triple은 **DNW 웰의 개수가 아니라 surrogate 공정에서 사용하는 implant component의 수**를 의미한다.

[근거: 2026-09-28](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/members/ryu980920/timeline/2026-09/2026-09-28.md) · [2026-10-06](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/members/ryu980920/timeline/2026-10/2026-10-06.md)

### 3-3. Triple-DNW — Deep tail 개선과 새로운 trade-off

세 번째 **Deep implant**를 추가하여 Main Peak, shallow tail, deep tail을 상대적으로 독립적으로 조정하도록 바꿨다. 45조건 screening에서 깊은 Energy가 tail에 미치는 영향을 확인한 뒤, 25조건·81조건 refinement로 범위를 좁혔다. 이후 27조건을 추가 분석해 **Case 15**를 PW calibration 중의 임시 기준으로 정했다.

Case 15의 DNW Peak 농도비는 약 **1.00008**, equivalent sigma는 **376.415 nm**로 SDE의 기준 폭(~380.6 nm)에 가까웠다. 그러나 300 nm, 600 nm, 1.3 µm 등의 **깊이별 오차는 남았다**. 따라서 이 수치만으로 공정·전기적 baseline 재현이 완료되었다고 해석하지 않았다.

[근거: 2026-10-08](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/members/ryu980920/timeline/2026-10/2026-10-08.md) · [2026-10-12-1](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/members/ryu980920/timeline/2026-10/2026-10-12-1.md) · [2026-10-15-1](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/members/ryu980920/timeline/2026-10/2026-10-15-1.md)

### 3-4. PW calibration — Main Peak와 junction-side tail의 충돌

DNW를 어느 정도 정합하더라도 **PW Boron의 Main Peak, 200~300 nm의 shoulder/second peak, 350~500 nm의 junction-side tail**은 별도 문제로 남았다.

처음 Dual-PW(Fixed + Trim) 결과에서는 일부 후보의 Main Peak가 기준에 근접했지만, 두 번째 Peak가 발생하고 462.7 nm 인근의 Boron이 부족했다. 이를 놓치지 않도록 **SVisualPy 점수 체계에 원본 SDE 프로파일 비교, shoulder/deep-tail 오차 및 second-peak/rebound 진단**을 반영했다.

Deep PW 29조건에서는 깊은 Boron 형성이 가능해졌으나 second peak 문제가 여전히 남았다. 이후 저Dose 18조건을 분석하고 **155 keV / 2.8×10¹² cm⁻²**를 Triple-PW의 *임시 Deep 고정 조건*으로 선택했다. 이것은 최종 우월성을 입증한 공정 조건이 아니다.

[근거: 2026-10-15-2](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/members/ryu980920/timeline/2026-10/2026-10-15-2.md) · [2026-10-19](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/members/ryu980920/timeline/2026-10/2026-10-19.md) · [2026-10-20](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/members/ryu980920/timeline/2026-10/2026-10-20.md)

### 3-5. Triple-PW — 2026년 10월 21일 현재

기존 Fixed + Deep 구성에 **Mid implant**를 추가하고, Mid Energy 65~90 keV와 Dose 3/6/9×10¹² cm⁻²를 조합한 **18조건을 실제 실행·분석**했다.

| 항목 | 1차 Triple-PW 18조건 결과 |
|---|---:|
| 결과 분석 가능한 조건 (RUN_OK) | **18/18** |
| 내부 SPLIT_OK 기준 통과 | **16/18** |
| 내부 ALL_OK 기준 통과 | **12/18** |
| Second Peak 탐지 | **11/18** |
| 내부 SCORE 최저 후보 | **Mid 80 keV / 9×10¹² cm⁻²** |

Dual-PW의 일부 형상 문제는 완화되었지만, **SPLIT_OK 통과가 두 번째 Peak의 완전 소멸을 의미하지는 않는다.** SCORE 최저 후보도 SDE와 PLX를 직접 겹쳐 보니 깊이별 부족·초과가 남아 있었다.

따라서 Main Peak 한 점의 정합을 우선하는 대신, **Mid의 위치/폭을 조정하고 Shallow Peak 보정을 후속 단계로 분리**하기로 했다. 이에 따라 **87.5~100 keV의 고Energy·저Dose Mid 18조건**을 새로 설계·검산했다. **이 후속 18조건은 10월 21일 기준 실행 결과가 확보되지 않았다.**

[근거: 2026-10-21 연구일지](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/members/ryu980920/timeline/2026-10/2026-10-21.md)

---

## 4. 이 연구에서 직접 수행한 역할

- **공정 모델링:** SDE 기준 도핑 분포를 추적하기 위한 SProcess surrogate implant/RTA 모델의 구성과 수정
- **시뮬레이션 연결:** SWB에서 SProcess–SNMesh–SDevice–SVisualPy dependency, output 및 진단 흐름 점검
- **DOE 설계와 검증:** 변수 조합 구성, SWB 입력 Parameter와 Dose fraction의 일관성 검산, 실제 실행 결과와 입력 설계 자료 구분
- **후처리 코드 개선:** SVisualPy에서 Peak 위치·농도·sigma·junction-side/deep-tail·second peak 및 rebound를 분리 분석하도록 지표 수정
- **연구 의사결정:** 최저 SCORE뿐 아니라 SDE 원본 PLX와 깊이별 잔차를 대조해 다음 공정 구조 및 DOE 방향 결정
- **재현성 기록:** 날짜별로 문제, 가설, 실행, 확인 결과, 수정 이유, 미완료 작업을 원본 코드·데이터와 연결해 관리

---

## 5. 현재 상태와 다음 검증

**완료한 범위 (2026.10.21 기준)**

- Single/Dual/Triple-DNW DOE 실행 결과 분석 및 PW 보정용 임시 DNW 후보 도출
- Dual-PW, Deep PW 및 첫 Triple-PW 18조건 결과를 통한 Boron profile mismatch 진단
- 공정 구조 변경과 함께 SWB 입력·SVisualPy 평가 지표를 개선한 이력 정리
- 다음 **고Energy·저Dose Mid 18조건 DOE 설계 및 입력 검산**

**미완료·후속 연구**

1. 새 Mid 18조건의 **실제 SProcess 실행** 및 SDE 원본 대비 깊이별 잔차 분석
2. 필요 시 Deep·Shallow implant의 순차적 보정과 PW/DNW crossover 확인
3. 최종 공정 후보의 **SDevice VBD, SCR 위치/폭, 중심·주변부 전계, avalanche, PEB, reverse I–V, dark-current proxy, ATP 및 온도 의존성** 검증
4. A Track의 광학 결과와 결합한 최종 상대 dToF 시스템 평가

**현재의 결과는 도핑 프로파일 보정 단계의 중간 성과다.** 최종 VBD·DCR·PDP·dToF 성능 개선을 실증하거나 양산 공정을 재현했다고 주장하지 않는다.

---

## 6. 연구 기록 및 재현 자료

| 자료 | 내용 |
|---|---|
| [공통 SPAD 연구계획서](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/shared/decisions/SPAD_%EC%97%B0%EA%B5%AC%EA%B3%84%ED%9A%8D%EC%84%9C.md) | 공통 reference, A/B 분담, 결과 검증 및 결합 원칙 |
| [개인 Timeline](https://github.com/jujushmaterial/2026-chip-master-project-record/tree/main/members/ryu980920/timeline) | 날짜별 실험 목적 → 문제 → 수행 → 판단 → 다음 작업 |
| [DOE 데이터 안내](https://github.com/jujushmaterial/2026-chip-master-project-record/blob/main/members/ryu980920/assets/data/README.md) | Single/Dual/Triple-DNW와 PW 조건표·결과 구분 |
| [TCAD 코드 및 설명](https://github.com/jujushmaterial/2026-chip-master-project-record/tree/main/members/ryu980920/assets/tcad) | SProcess command, SVisualPy post-processing 및 버전별 변경 이력 |

**프로젝트 접근:** 결과를 나열하는 것보다, **관측한 프로파일 불일치가 다음 실험 설계에 어떻게 반영되었는지** 추적 가능한 형태로 남기는 데 중점을 두었다.

<sub>마지막 반영 기록: 2026-10-21. 원본 연구일지의 후속 결과가 추가되면 본 요약도 해당 근거를 확인해 갱신한다.</sub>
