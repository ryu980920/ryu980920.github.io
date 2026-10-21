---
layout: default
title: "TCAID · TCAD AI Manager | Yongseong Ryu"
---

[← 포트폴리오 홈으로](../) · [전체 프로젝트](../projects/) · [경진대회 수상 기록](../awards/tcaid-2026/)

# TCAID: TCAD AI Manager

**자연어 기반 Synopsys Sentaurus TCAD 연구 자동화 시스템**

| 항목 | 내용 |
| --- | --- |
| 기간 | **2026.07–2026.09** |
| 구분 | **차세대반도체 경진대회 · 팀 프로젝트** |
| 팀명 | **쥬송쥬송** |
| 팀원 | 송민호 · 주상현 · **유용성** · 이선재 · 이택규 |
| 소속 | 숭실대학교 |
| 수상 | **2026 차세대반도체 경진대회 최우수상** (2026.10.01) |
| 주요 기술 | Python · Synopsys Sentaurus TCAD / Workbench · LLM · SSH / SFTP |
| 핵심 접근 | Human-in-the-loop · 기존 프로젝트 보호 · 명시적 승인 · 결과 추적성 |

<figure class="tcaid-figure tcaid-logo"><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/logo.webp" target="_blank" rel="noopener noreferrer" aria-label="TCAID TCAD AI Manager 로고 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/logo.webp" alt="TCAID TCAD AI Manager 로고" loading="lazy"></a><figcaption>프로젝트 로고 · 클릭하면 원본 이미지 확대</figcaption></figure>

TCAID는 자연어를 이용해 반도체 공정·소자 연구를 수행할 때 필요한 **프로젝트 해석, 조건 확인, 코드 제안과 검토, 실행, 데이터 수집, 결과 비교**를 연결하는 연구 지원 시스템이다. TCAD의 물리 계산을 AI로 대체하는 대신, 실제 계산은 Sentaurus가 담당하고 **연구자는 변경 사항을 최종 승인**하도록 설계했다.

**[프로젝트 시연 영상](https://www.youtube.com/watch?v=SZO0Ig2KbU4)** · **[팀 연구 저장소](https://github.com/jujushmaterial/TCAD-AI-Manager)**

> 팀 전체가 공동으로 수행한 프로젝트이며, 제 포트폴리오에는 공동 연구의 시스템 설계와 검증 결과를 정리했습니다.

---

## 01. 프로젝트 개요

연구자의 요청과 실제 TCAD 실행 사이에는 여러 수동 절차가 존재한다. TCAID는 이 절차를 하나의 연속된 흐름으로 묶으면서도, 사람이 검토하고 승인해야 할 단계를 자동화에서 분리하는 데 중점을 두었다.

<div class="tcaid-stats">
  <div><strong>56.4%</strong><span>1-Cycle 시연 작업시간 감소</span></div>
  <div><strong>2D + 3D</strong><span>SProcess 구조 및 메시 생성</span></div>
  <div><strong>2 nodes</strong><span>V<sub>D</sub> = 0.5 / 1.0 V SDevice 비교</span></div>
  <div><strong>4,307</strong><span>자동 회귀 테스트 통과 · 3 skip · 0 fail</span></div>
</div>

여기서 시간 단축 결과는 **대회 시연에서 비교한 한 차례의 작업 흐름**에 해당하며, 모든 소자 연구·모든 서버 환경에서의 평균값은 아니다.

## 02. 연구 배경과 문제 정의

Sentaurus Workbench를 이용한 반복 연구에서는 기존 프로젝트의 CMD/TCL과 dependency 확인, 코드 수정, Windows와 Linux 계산 서버 사이의 자료 이동, 실행 및 오류 점검, 결과 다운로드와 분석을 차례로 진행해야 한다. 일반적인 LLM으로 코드를 받아도 이 과정의 연결과 검증은 상당 부분 연구자가 수행한다.

| 구분 | 작업 순서 |
| --- | --- |
| 일반적인 수작업 | 연구 요구 정리 → 코드 생성 요청 → 프로젝트 파일 직접 변경 → 서버 로그인·실행 → 결과 회수 → 개별 비교 |
| TCAID | 자연어 요청 → 누락 조건 협의 → 현행 프로젝트에 근거한 코드 제안 → 사용자 승인 → **격리된 사본**으로 실행 → 결과·지표 연결 |

개발 목표는 단순한 코드 자동 생성기가 아니라, **기존 연구 프로젝트를 손상시키지 않고 실험 의도부터 결과까지 추적할 수 있는 시스템**을 구현하는 것이었다.

## 03. 시스템 아키텍처

Windows의 TCAID 관리 프로그램과 Linux의 Sentaurus 계산 환경을 구분하고, SSH/SFTP를 통해 연결한다. AI는 연구 의도 해석 및 변경 제안을 수행하지만 반도체 물리 계산의 최종 실행 주체는 Sentaurus이다.

<div class="tcaid-figure-grid"><figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/system.webp" target="_blank" rel="noopener noreferrer" aria-label="Windows TCAID, Linux Sentaurus 및 연구자 역할을 구분한 시스템 구성 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/system.webp" alt="Windows TCAID, Linux Sentaurus 및 연구자 역할을 구분한 시스템 구성" loading="lazy"></a><figcaption>운영체제·프로그램·연구자 사이의 역할 분리 · 클릭하면 원본 이미지 확대</figcaption></figure>
<figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/architecture.webp" target="_blank" rel="noopener noreferrer" aria-label="연구 요청부터 TCAD 실행 및 결과 분석까지의 소프트웨어 아키텍처 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/architecture.webp" alt="연구 요청부터 TCAD 실행 및 결과 분석까지의 소프트웨어 아키텍처" loading="lazy"></a><figcaption>요청 구조화부터 결과 수집까지의 처리 계층 · 클릭하면 원본 이미지 확대</figcaption></figure></div>

| 모듈 | 담당 기능 |
| --- | --- |
| **Project Inspector** | 기존 Workbench의 파일과 노드, 실행 도구, dependency를 읽기 전용으로 분석해 상태를 기록 |
| **Research Intent** | 확정된 조건·AI 제안·추가 확인이 필요한 항목을 서로 구분 |
| **Code Generation / Diff** | 참고 예제와 승인된 요구를 반영해 코드 변경안을 생성하고 수정 전후 차이를 표시 |
| **Approval / Workspace** | 명시적 승인이 확인된 경우에만 프로젝트 원본과 분리된 작업 공간에 적용 |
| **Results / Metrics** | 전송된 결과 파일, 실행 manifest, 추출 지표를 동일한 연구 이력에 연결 |

이 구조에서는 **기본적으로 원본을 읽기 전용으로 취급**한다. 적용 권한이나 파일의 무결성이 확실하지 않으면 실행을 중지하는 *fail-closed* 원칙과, 실험 조건부터 결과까지 기록을 연결하는 *provenance*가 핵심 설계 원칙이다.

## 04. Human-in-the-loop 연구 Workflow

연구자의 자연어 명령을 곧바로 서버에 전달하지 않는다. 조건이 정해지지 않은 상태와 AI가 제안한 사항을 먼저 확인하고, 사용자가 제안된 코드·대상·조건을 검토하도록 작업을 분리했다.

1. 연구자가 공정 또는 소자 목표를 자연어로 입력한다.
2. 시스템이 필수 조건 중 미확정 항목을 찾아 추가로 질문한다.
3. 기존 Workbench 상태와 연결 관계를 변경 없이 점검한다.
4. 참고자료 및 확정 조건을 바탕으로 변경안과 diff를 제시한다.
5. 연구자가 적용 내용을 명시적으로 승인한다.
6. 원본과 분리된 사본을 만들어 Sentaurus를 실행한다.
7. SFTP로 결과를 회수하고 파일 상태와 hash를 확인한다.
8. run별 지표와 실행 정보를 연결해 후속 연구 판단에 사용한다.

<figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/safety.webp" target="_blank" rel="noopener noreferrer" aria-label="코드 승인과 확정 상태를 관리하는 Human-in-the-loop 안전 절차 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/safety.webp" alt="코드 승인과 확정 상태를 관리하는 Human-in-the-loop 안전 절차" loading="lazy"></a><figcaption>명시적 승인 이전에는 실제 코드 적용·실행으로 넘어가지 않는 설계 · 클릭하면 원본 이미지 확대</figcaption></figure>
<div class="tcaid-figure-grid"><figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/workspace.webp" target="_blank" rel="noopener noreferrer" aria-label="격리 실행 공간 및 승인 무효 상태 처리 구조 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/workspace.webp" alt="격리 실행 공간 및 승인 무효 상태 처리 구조" loading="lazy"></a><figcaption>원본 보호와 별도 workspace · 클릭하면 원본 이미지 확대</figcaption></figure>
<figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/provenance.webp" target="_blank" rel="noopener noreferrer" aria-label="연구 의도, 승인, 실행, 파일, 추출 지표를 연계하는 기록 구조 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/provenance.webp" alt="연구 의도, 승인, 실행, 파일, 추출 지표를 연계하는 기록 구조" loading="lazy"></a><figcaption>데이터의 생성·검토·실행 이력 추적 · 클릭하면 원본 이미지 확대</figcaption></figure></div>

## 05. 핵심 기능 구현

### 자연어 요청 및 조건 협의

연구자가 “게이트 길이 15 nm nMOS 구조를 SProcess로 생성”하는 목표를 입력했을 때, 시스템은 필요한 세부 조건을 추가로 확인한다. 사용자가 실제로 지정한 값, 아직 정해지지 않은 값, AI가 제안한 값이 구별되도록 구현하였다.

<figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/intent.webp" target="_blank" rel="noopener noreferrer" aria-label="연구 조건을 질문하고 확정하는 TCAID GUI 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/intent.webp" alt="연구 조건을 질문하고 확정하는 TCAID GUI" loading="lazy"></a><figcaption>자연어 입력에 대한 조건 확인·협의 화면 · 클릭하면 원본 이미지 확대</figcaption></figure>

### 코드 변경 제안과 승인

제안된 CMD/TCL의 설정값, 참고 근거, 수정 대상과 차이점을 검토한 뒤 승인하도록 했다. 사전에 승인한 작업의 범위를 벗어나거나 승인 정보가 유효하지 않으면 해당 변경안으로 실행할 수 없다.

<div class="tcaid-figure-grid"><figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/approval.webp" target="_blank" rel="noopener noreferrer" aria-label="실행 전 코드와 조건 승인 GUI 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/approval.webp" alt="실행 전 코드와 조건 승인 GUI" loading="lazy"></a><figcaption>사용자가 직접 검토하는 승인 단계 · 클릭하면 원본 이미지 확대</figcaption></figure>
<figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/copy.webp" target="_blank" rel="noopener noreferrer" aria-label="승인된 코드가 독립 실행 사본에 적용된 결과 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/copy.webp" alt="승인된 코드가 독립 실행 사본에 적용된 결과" loading="lazy"></a><figcaption>원본 대신 사본에만 반영되는 변경 사항 · 클릭하면 원본 이미지 확대</figcaption></figure></div>

### 서버 연결 및 파일 신뢰성

실제 Linux 연동에는 SSH/SFTP를 이용한다. 서버 host key 검증, 비밀번호 비저장, 결과 파일 전송 상태 확인과 **SHA-256 기반 manifest**를 적용하고, 네트워크 응답이 끊겨 실행 성공 여부가 불확실한 경우에는 성공으로 단정하지 않도록 처리했다.

## 06. 실제 SProcess·SDevice 시연

### SProcess: Gate length 15 nm nMOS

자연어로 공정 조건을 협의한 후 SProcess 입력 파일을 생성해, 분리된 Workbench 실행 사본에서 동작시켰다. SVisual에서 **2D 재료 분포와 3D 구조/mesh 생성 결과**를 확인하였다.

<div class="tcaid-figure-grid"><figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/mos2d.webp" target="_blank" rel="noopener noreferrer" aria-label="Sentaurus SProcess로 생성된 15 nm nMOS 2D 구조 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/mos2d.webp" alt="Sentaurus SProcess로 생성된 15 nm nMOS 2D 구조" loading="lazy"></a><figcaption>2차원 구조 및 재료 분포 · 클릭하면 원본 이미지 확대</figcaption></figure>
<figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/mos3d.webp" target="_blank" rel="noopener noreferrer" aria-label="Sentaurus SProcess 3D nMOS 구조 및 mesh 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/mos3d.webp" alt="Sentaurus SProcess 3D nMOS 구조 및 mesh" loading="lazy"></a><figcaption>3차원 구조와 mesh 결과 · 클릭하면 원본 이미지 확대</figcaption></figure></div>

이 결과는 공정 구조 생성과 실행 경로가 동작했다는 증거이며, 제작 가능성 또는 소자 성능 최적화까지 입증한 결과는 아니다.

### SDevice: 서로 다른 Drain bias 비교

기존 Workbench 프로젝트에 **V<sub>D</sub> = 0.5 V, 1.0 V** 실험 노드를 구성하고, 각각의 SDevice 실행 결과를 회수했다. 이후 동일하게 정의된 **`ion_max` 지표**를 추출해 비교했다.

<figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/sdevice.webp" target="_blank" rel="noopener noreferrer" aria-label="Sentaurus Workbench에서 두 drain bias 실행 노드를 구성한 모습 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/sdevice.webp" alt="Sentaurus Workbench에서 두 drain bias 실행 노드를 구성한 모습" loading="lazy"></a><figcaption>V_D = 0.5 V 및 1.0 V 조건의 실행 구성 · 클릭하면 원본 이미지 확대</figcaption></figure>
<div class="tcaid-figure-grid"><figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/metric.webp" target="_blank" rel="noopener noreferrer" aria-label="SDevice 실행별 ion_max 추출 인터페이스 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/metric.webp" alt="SDevice 실행별 ion_max 추출 인터페이스" loading="lazy"></a><figcaption>동일 정의의 지표 수집 · 클릭하면 원본 이미지 확대</figcaption></figure>
<figure class="tcaid-figure "><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/compare.webp" target="_blank" rel="noopener noreferrer" aria-label="두 SDevice 시뮬레이션의 ion_max 비교 화면 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/compare.webp" alt="두 SDevice 시뮬레이션의 ion_max 비교 화면" loading="lazy"></a><figcaption>회수한 run 결과의 정량 비교 · 클릭하면 원본 이미지 확대</figcaption></figure></div>

단일 실행 결과를 선택해 AI 해석을 요청하는 UI까지 구현했으나, **복수 run의 물리적 원인을 자동으로 통합 분석하는 기능**은 경진대회 검증 범위에 포함되지 않았다.

## 07. 검증 결과와 작업 효율

### 1-Cycle 소요 시간

동일 목표의 연구 작업 흐름을 경진대회 환경에서 비교한 결과, 직접 LLM을 활용하며 각 작업을 연결한 방식에 비해 TCAID 방식에서 전체 소요 시간이 감소했다.

| 비교 방식 | 총 소요 시간 |
| --- | ---: |
| 범용 LLM을 사용한 수동 연결 작업 | **67분 00초** |
| TCAID 연속 Workflow | **29분 13초** |
| 감소율 | **56.4%** |

<div class="tcaid-time-bars" aria-label="작업 시간 비교">
  <div><span>LLM 수동 연결</span><div><i style="width:100%"></i></div><b>67:00</b></div>
  <div><span>TCAID</span><div><i style="width:43.6%"></i></div><b>29:13</b></div>
</div>

### 확인한 검증 단계

| 검증 방법 | 확인 범위 |
| --- | --- |
| **자동 회귀 테스트** | 4,307 pass · 3 skip · 0 fail (네트워크 없이 수행하는 프로그램 로직 및 계약 점검) |
| **실환경 연동** | SSH/SFTP 접속, Linux/Sentaurus에서 SDevice 실행, 결과 파일 회수 |
| **통합 GUI 시연** | 자연어 입력에서 승인, 사본 생성, TCAD 실행, 결과 연결까지 |
| **구조·지표 비교** | 2D/3D mesh, 복수 V<sub>D</sub> 노드, `ion_max` 추출·비교 |

시간·테스트 결과는 **2026년 경진대회 제출 자료와 전시 포스터에서 보고된 수치**이며, 프로그램 자동화의 검증과 생성 소자의 물리적 타당성 검증은 구별한다.

## 08. 프로젝트 시연 영상

자연어로 실험을 정의하고 사용자 승인을 거친 뒤 Workbench와 연동해 결과를 회수하는 과정을 영상으로 확인할 수 있다.

<div class="tcaid-video"><iframe src="https://www.youtube.com/embed/SZO0Ig2KbU4" title="TCAID TCAD AI Manager 시연 영상" loading="lazy" referrerpolicy="strict-origin-when-cross-origin" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe></div>

[YouTube 시연 영상 바로 열기 →](https://www.youtube.com/watch?v=SZO0Ig2KbU4)

영상에는 SWB 프로젝트 연결, 연구 의도와 조건 확인, 코드 변경 검토 및 승인, 별도 사본에서 실행, SProcess 구조 결과, SDevice 두 조건 비교, 지표 추출 과정이 포함된다.

## 09. 결론과 후속 과제

TCAID가 보여준 것은 AI에 소자 물리 계산을 맡기는 방식이 아니라, **실제 연구자의 실행·승인 권한을 유지하면서 TCAD 반복작업을 연결하는 방식**이다.

주요 의미는 기존 SWB 프로젝트의 연속성을 유지했다는 점, 원본을 직접 수정하지 않는 구조에서 명시적 승인과 실행을 분리했다는 점, 실험의 설정·사본·파일·지표가 이어지도록 기록 체계를 만든 점이다.

추가 개발 및 연구 방향은 다음과 같다.

1. PLT, `gvars.dat` 등 다양한 실행 산출물에 대한 공통 분석 기능 확대
2. 다수 run을 비교하는 소자 물리 해석 및 수렴 오류 진단 보강
3. Parameter sweep을 통한 다음 DOE 후보 추천
4. SDE·SVisual을 포함해 공정–소자–회로 분석 흐름으로 적용 범위 확장

이 항목들은 **후속 과제**이며, 본 경진대회 시연에서 모두 구현·검증된 기능이라는 뜻이 아니다.

## 10. 전시 포스터와 수상

<figure class="tcaid-figure tcaid-poster"><a href="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/poster.webp" target="_blank" rel="noopener noreferrer" aria-label="2026 차세대반도체 경진대회 TCAID 전시 포스터 이미지 확대"><img src="https://raw.githubusercontent.com/minhosong-mse/minhosong-mse.github.io/main/projects/tcaid/assets/images/poster.webp" alt="2026 차세대반도체 경진대회 TCAID 전시 포스터" loading="lazy"></a><figcaption>TCAID 프로젝트의 문제 정의, 설계 구성, 시연 결과를 담은 대회 전시 포스터 · 클릭하면 원본 이미지 확대</figcaption></figure>

**최우수상 (대회 최고상)** · 2026년 10월 1일 · 숭실대학교 차세대반도체 혁신융합대학 사업단

[수상 상장 및 증빙 보기 →](../awards/tcaid-2026/)

---

## 관련 자료

- **[TCAID GitHub 연구 저장소](https://github.com/jujushmaterial/TCAD-AI-Manager)** — 저장소 접근 권한에 따라 일부 방문자는 내용을 열람하지 못할 수 있음
- **[프로젝트 시연 영상](https://www.youtube.com/watch?v=SZO0Ig2KbU4)**
- **[본인 포트폴리오의 경진대회 수상 증빙](../awards/tcaid-2026/)**

