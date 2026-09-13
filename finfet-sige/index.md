# FinFET pMOS Embedded SiGe S/D — Stress Transfer Efficiency & Process Design Window

**Topic:** Embedded SiGe Source/Drain의 Ge 조성과 S/D fin recess depth(FR)가 채널 응력·응력 전달 효율·구동 성능·정전제어·누설에 미치는 영향 분석  
**Period:** 2026.07–2026.09  
**Type:** 차세대반도체 경진대회 · 팀 프로젝트  
**Tools:** Synopsys Sentaurus TCAD, Python  
**Keywords:** FinFET, pMOS, Embedded SiGe, Stress Engineering, STE, Fin Recess, DoE, Process Window

[← 포트폴리오 홈으로](../) · [Competition 기록 저장소](https://github.com/ryu980920/competition) · [Share 실행·데이터 저장소](https://github.com/ryu980920/Share)

---

## Key Contribution

> **25-point Ge × FR DoE를 통해 단순 최대 성능점이 아니라 설계자가 실제로 선택할 수 있는 공정 설계 공간을 만들었다.**

- **Ge 조성 → 절대 채널 압축응력을 지배**
- **Fin Recess depth(FR) → Stress Transfer Efficiency(STE)를 지배**
- 응력 전달 이득이 포화되는 지점과 SS·DIBL·Ioff 열화가 커지는 지점을 함께 비교해 **실용 FR 설계창 15–20 nm**를 제안
- 본 5×5 격자 외에 **FR=15/22 nm 추가 검증**, **Strain Impact ON/OFF**, **Fin width 15→7.5 nm 민감도 분석**으로 결론을 교차 검증

![STE와 정전제어의 trade-off overlay](https://raw.githubusercontent.com/ryu980920/Share/main/analysis/figures/pres_tradeoff_overlay.png)

---

## 1. 문제 정의 (Problem Definition)

Embedded SiGe S/D는 pMOS 채널에 압축응력을 전달해 정공 이동도와 구동 성능을 높이는 대표적인 strain engineering 기법이다. 그러나 **Ge 조성을 높이거나 S/D recess를 깊게 하는 것만으로 최적 설계가 결정되지는 않는다.**

응력이 증가하더라도 다음 문제가 동시에 발생할 수 있다.

- 누설 전류 증가
- SS(Subthreshold Swing) 열화
- DIBL 증가
- 깊은 recess에 따른 형상·정전제어 악화
- 투입한 응력이 실제 채널로 전달되는 효율의 포화

따라서 본 프로젝트의 질문은 단순히 “어느 조건에서 응력이 가장 큰가?”가 아니었다.

> **Ge 조성과 FR이 각각 무엇을 지배하며, 구동 성능과 누설·정전제어를 동시에 고려했을 때 실제로 사용할 수 있는 설계 영역은 어디인가?**

---

## 2. 소자 구조와 설계실험 (Structure & DoE)

### 기준 구조

| 항목 | 값 |
|---|---:|
| Gate length | 25 nm |
| Fin height | 35 nm |
| Fin width (top / bottom) | 15 nm |
| Esd | 7.5 nm |
| S/D Boron concentration | 2 × 10²⁰ cm⁻³ |
| Channel concentration | 2 × 10¹⁸ cm⁻³ |
| Vdd | 0.8 V |
| Gate workfunction | 4.623 eV |
| Channel / substrate orientation | ⟨110⟩ / (100) |

![Ge mole fraction distribution](https://raw.githubusercontent.com/ryu980920/Share/main/runs/attachments/G50_F0/ge_mole_fraction.png)

![Channel-direction stress field](https://raw.githubusercontent.com/ryu980920/Share/main/runs/attachments/G50_F0/stress_ZZ_field.png)

### 5 × 5 본 DoE

두 설계 변수를 다음과 같이 조합해 **25개 격자점**을 계산했다.

| 변수 | 조건 |
|---|---|
| Ge composition | 30, 40, 50, 60, 70% |
| Fin Recess depth | 0, 10, 20, 30, 35 nm |

평가 지표는 절대 채널 응력, STE, gmSat, IdSat, SSlin, SSSat, DIBL, Ioff였다.

[전체 병합 데이터 CSV 보기 →](https://github.com/ryu980920/Share/blob/main/analysis/grid.csv)

---

## 3. 왜 절대 응력만으로는 부족했는가

Ge 조성이 높아질수록 SiGe 자체가 더 큰 mismatch strain을 만들기 때문에 절대 채널 압축응력은 증가한다. 하지만 이 결과만으로는 **“만든 응력이 채널로 얼마나 효율적으로 전달되었는가”**를 분리해서 볼 수 없다.

그래서 본 연구에서는 nominal SiGe strain을 기준으로 채널에 전달된 응력을 정규화한 **Stress Transfer Efficiency(STE)** 를 별도 지표로 사용했다.

이를 통해 **응력의 양**과 **응력 전달 효율**을 분리해 해석했다.

![Absolute channel stress map](https://raw.githubusercontent.com/ryu980920/Share/main/analysis/figures/pres_stress_map.png)

![Stress Transfer Efficiency map](https://raw.githubusercontent.com/ryu980920/Share/main/analysis/figures/pres_STE_map.png)

### 핵심 관계

**Absolute Stress → Ge-dominated**  
**Stress Transfer Efficiency → FR-dominated**

즉 Ge를 높이는 것은 “얼마나 큰 응력을 만들 수 있는가”에 직접적이고, FR을 깊게 하는 것은 “그 응력을 채널로 얼마나 잘 전달하는가”에 더 직접적으로 작용했다.

---

## 4. 25개 격자점에서 확인한 설계 공간

### 4-1. 절대 채널 응력

FR=0에서 Ge 30→70% 변화 시:

| 지표 | 변화 |
|---|---:|
| 채널 압축응력 절대값 | 1.346 → 3.210 GPa |
| gmSat | 약 19% 증가 |
| IdSat_norm | 약 34% 증가 |
| SSlin | 80.4 → 76.6 mV/dec |
| DIBL | 89.3 → 69.3 mV/V |
| Ioff_norm | 약 5.6배 증가 |

Ge 조성 증가는 채널 압축응력과 구동 성능을 높였고, 동일 형상에서는 SSlin·DIBL도 악화되지 않았다. 대신 비용은 **누설 증가**로 나타났다.

### 4-2. STE

| FR (nm) | Ge 30% | Ge 40% | Ge 50% | Ge 60% | Ge 70% |
|---:|---:|---:|---:|---:|---:|
| 0 | 0.593 | 0.596 | 0.598 | 0.602 | 0.607 |
| 10 | 0.649 | 0.647 | 0.648 | 0.650 | 0.654 |
| 20 | 0.672 | 0.667 | 0.667 | 0.668 | 0.671 |
| 30 | 0.677 | 0.671 | 0.670 | 0.670 | 0.673 |
| 35 | 0.676 | 0.670 | 0.668 | 0.668 | 0.670 |

모든 Ge 조건에서 FR=0→20 nm 구간에서는 STE가 증가했다. 그러나 **20 nm 이후 추가 이득은 매우 작아졌다.**

![STE contour](https://raw.githubusercontent.com/ryu980920/Share/main/analysis/figures/contour_ste.png)

### 4-3. 누설과 정전제어

절대 응력이나 STE 하나만으로 최적점을 정하지 않고 Ioff·SS·DIBL 지도를 함께 비교했다.

![Ioff map](https://raw.githubusercontent.com/ryu980920/Share/main/analysis/figures/pres_Ioff_map.png)

![SSlin map](https://raw.githubusercontent.com/ryu980920/Share/main/analysis/figures/pres_SSlin_map.png)

![DIBL map](https://raw.githubusercontent.com/ryu980920/Share/main/analysis/figures/pres_DIBL_map.png)

FR=30–35 nm에서는 대부분의 Ge 조건에서 누설과 정전제어 열화가 커졌다. 즉 **응력 전달 효율의 포화 이후에도 깊은 recess의 전기적 비용은 계속 증가**했다.

---

## 5. Practical Process Window — FR 15–20 nm

5×5 DoE만으로는 FR=20 nm 부근의 경계를 충분히 세밀하게 판단하기 어려워, Ge=50%에서 **FR=15 nm와 22 nm를 추가 계산**했다.

| FR (nm) | Stress (GPa) | STE | gmSat (S/µm) | SSlin (mV/dec) | DIBL (mV/V) | Ioff_norm |
|---:|---:|---:|---:|---:|---:|---:|
| 0 | −2.262 | 0.598 | 1.060×10⁻⁴ | 78.5 | 78.7 | 2.41×10⁻¹⁰ |
| 10 | −2.449 | 0.648 | 1.128×10⁻⁴ | 80.2 | 85.3 | 3.39×10⁻¹⁰ |
| **15** | **−2.493** | **0.660** | **1.143×10⁻⁴** | **82.2** | **90.7** | **5.26×10⁻¹⁰** |
| **20** | **−2.521** | **0.667** | **1.149×10⁻⁴** | **84.7** | **97.3** | **9.68×10⁻¹⁰** |
| 22 | −2.525 | 0.668 | 1.131×10⁻⁴ | 87.5 | 100.0 | 1.93×10⁻⁹ |
| 30 | −2.532 | 0.670 | 1.134×10⁻⁴ | 100.6 | 116.0 | 2.41×10⁻⁸ |
| 35 | −2.525 | 0.668 | 1.131×10⁻⁴ | 112.9 | 129.3 | 1.05×10⁻⁷ |

핵심 변화는 다음과 같다.

- FR=15 nm에서 STE가 Ge=50% 기준 최대값의 **98.5%**
- FR=20 nm에서 STE가 최대값의 **99.6%**에 도달하고 **gmSat 최대**
- FR=20→22 nm에서는 STE가 0.667→0.668로 사실상 포화
- 같은 구간에서 gmSat은 감소하고 Ioff는 약 **2배 증가**
- FR=20→35 nm에서는 SSlin·DIBL·Ioff가 빠르게 악화

## **Practical FR Design Window: 15–20 nm**

이 구간은 대부분의 응력 전달 이득을 확보하면서 깊은 recess의 전기적 비용이 급증하기 전의 영역이다.

[FR 세분화 검증 CSV 보기 →](https://github.com/ryu980920/Share/blob/main/baseline/verification_FR_refine_G50.csv)

---

## 6. 추가 검증 (Verification)

### 6-1. Strain Impact ON/OFF

깊은 FR에서 나타나는 SS·DIBL·Ioff 열화가 단순히 응력 모델 때문인지 확인하기 위해 Strain Impact를 끈 조건과 비교했다.

![Strain Impact ON/OFF FR sweep](https://raw.githubusercontent.com/ryu980920/Share/main/analysis/figures/verify_strain_impact_FRsweep.png)

Strain Impact를 꺼도 FR 증가에 따른 정전제어·누설 열화가 크게 남았다. 따라서 deep-recess 열화는 **응력 자체보다 구조 형상 변화가 1차 원인**이며, strain/band-structure effect가 누설을 추가 증폭하는 것으로 해석했다.

### 6-2. Fin-width sensitivity

기준 fin width 15 nm를 7.5 nm로 줄여 Ge 60/70% 조건에서 FR 전 구간을 다시 비교했다.

얕은 FR에서는 폭 축소가 SSlin·DIBL·STE를 개선했지만 깊은 FR에서는 이 관계가 역전됐다. 따라서 **소자가 미세화될수록 허용 가능한 FR 상한은 더 엄격해질 수 있다**는 추가 설계 시사점을 얻었다.

---

## 7. 이 프로젝트의 차별점

Embedded SiGe S/D와 Ge 조성·recess 최적화 자체는 이미 널리 알려진 주제다. 그래서 본 프로젝트의 차별점을 “새로운 물리 현상 발견”으로 두지 않았다.

대신 다음에 집중했다.

1. **두 변수를 동시에 2D 설계 공간으로 구성**
2. 절대 응력과 STE를 분리해 **Ge와 FR의 역할을 구분**
3. 응력뿐 아니라 gmSat·SS·DIBL·Ioff를 함께 지도화
4. 단일 최대점이 아니라 **사용 가능한 공정 window**를 제시
5. 경계점과 물리 모델·fin width를 추가 검증해 결론의 적용 범위를 확인

즉 결과는 “Ge=몇 %, FR=몇 nm가 최고”가 아니라, **설계 목적에 따라 선택할 수 있는 관계와 경계**를 제시하는 데 의미가 있다.

---

## Competition Feedback & Retrospective

### 2차 발표 피드백

심사 과정에서 가장 중요한 질문은 다음과 같았다.

> “Embedded SiGe와 recess 최적화는 이미 널리 알려진 주제인데, 이 프로젝트의 차별점은 무엇인가?”

이에 대해 본 프로젝트는 단순 parameter sweep으로 최대값을 찾은 것이 아니라, **요구되는 구동 성능·허용 가능한 누설·정전제어를 동시에 고려해 실제 설계 지점을 선택할 수 있도록 2D contour와 trade-off map을 구성했다**는 점을 핵심으로 설명했다.

심사자 역시 이 부분을 프로젝트의 장점으로 평가했지만, 동시에 **“청중은 발표를 듣는 것만으로 핵심을 모두 따라오지 못하므로, 프로젝트의 강점 자체를 슬라이드에 크게 명시해야 한다”**는 피드백을 주었다.

### 발표를 마치고 얻은 교훈

기술적으로는 충분한 데이터와 검증을 확보했지만, 주제 자체의 신규성 측면에서는 아쉬움이 남았다. 이를 통해 이후 연구 주제 선정 기준을 다음처럼 바꾸게 되었다.

- 먼저 **가장 최신 논문**을 확인한다.
- Introduction보다 **Conclusion / Limitation / Future Work**를 중점적으로 본다.
- 아직 해결되지 않은 문제를 TCAD에서 검증 가능한 연구 질문으로 바꾼다.
- “기존 구조에서 변수 몇 개를 최적화”하는 수준을 넘어서, 후속 연구가 필요한 물리적·공정적 질문을 우선한다.

이번 프로젝트는 잘 알려진 기술을 체계적인 설계 문제로 바꾸는 경험이었고, 동시에 **다음 연구에서는 ‘무엇을 최적화할까’보다 ‘아직 무엇이 밝혀지지 않았나’를 먼저 묻는 계기**가 되었다.

---

## 9. Data & Repositories

### 실행·재현 데이터 — Share

- [Share 저장소](https://github.com/ryu980920/Share)
- [25-point 병합 데이터: analysis/grid.csv](https://github.com/ryu980920/Share/blob/main/analysis/grid.csv)
- [FR 15/22 nm 추가 검증](https://github.com/ryu980920/Share/blob/main/baseline/verification_FR_refine_G50.csv)
- [Strain Impact FR sweep](https://github.com/ryu980920/Share/blob/main/baseline/verification_strain_impact_G50_FRsweep.csv)
- [Fin-width 민감도 데이터](https://github.com/ryu980920/Share/blob/main/baseline/verification_finwidth_half_G60_G70.xlsx)
- [분석 코드](https://github.com/ryu980920/Share/tree/main/analysis)

### 연구 과정·판단 기록 — Competition

- [Competition 저장소](https://github.com/ryu980920/competition)
- [최종 프로젝트 요약](https://github.com/ryu980920/competition/blob/main/docs/reports/project-summary.md)
- [주제 선정 이력](https://github.com/ryu980920/competition/blob/main/docs/topic-selection-history.md)
- [개발 로그](https://github.com/ryu980920/competition/blob/main/docs/devlog.md)
- [검증 오류 회고](https://github.com/ryu980920/competition/blob/main/docs/retrospective.md)

두 저장소의 역할을 분리했다. **Share는 현재 유효한 실행 조건·원본 데이터·재현성**, **Competition은 주제 선정·의사결정·검증·회고**를 담당한다.

---

## 10. 한계와 후속 연구

- 본 결과는 Ge 30–70%, FR 0–35 nm 범위의 TCAD 설계 공간에 한정된다.
- STE 절대값은 본 연구의 정규화 기준과 탄성계수 가정에 의존한다.
- deep-recess 영역의 누설 증가에 대해서는 DIBL·OFF-state current로 정전제어 열화를 확인했지만, 공간적인 누설 경로 자체를 직접 규명하지는 않았다.
- 향후에는 OFF-state current-density map, BTBT ON/OFF 비교, 더 미세한 fin/GAA 구조에서의 FR scaling을 추가 검증할 수 있다.
- 연구 주제 선정 단계에서는 최신 논문의 Future Work를 우선 분석해 **미해결 문제 중심의 연구 질문**을 설정하는 방향으로 확장할 계획이다.
