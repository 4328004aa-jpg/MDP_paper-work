# 페이퍼워크 — Dehghani, Abbasi & Oliveira (2021)

**"Proactive transshipment in the blood supply chain: A stochastic programming approach"**

| 항목 | 내용 |
|---|---|
| 저자 | Maryam Dehghani (RMIT), **Babak Abbasi** (RMIT, 교신저자), **Fabricio Oliveira** (Aalto University, Dept. of Mathematics and Systems Analysis) |
| 저널 | ***Omega* 98 (2021) 102112** |
| DOI | `10.1016/j.omega.2019.102112` |
| 접수 / 게재승인 / 온라인 | 2018-08-02 / **2019-08-30** / 2019-08-31 |
| Associate Editor | M. Mayorga |
| 키워드 | Blood supply chain · Inventory management · **Lateral transshipment** · Healthcare operations · Stochastic optimization |

> ⭐ **폴더 내 9편 중 "2SSP + RHA"를 가장 정통으로 구현한 논문**입니다. 저자들 스스로 이 조합을 핵심 기여로 내세웁니다.

---

# 1. 문제 상황 (Problem Setting)

## 1-1. 왜 혈액 공급망인가 — 문제의 특수성

| 특성 | 내용 |
|---|---|
| **공급** | ⭐ **준(準)예측불가** — 전적으로 헌혈에 의존, **인공 생산 불가** |
| **부패성** | 적혈구 유효기간 유한 → 과잉 재고는 **즉시 폐기로 직결** |
| **수요** | 불확실, 일별 변동, **평일에 높음** |
| **구조** | 수요가 **병원**에서 발생 → 네트워크에 병원이 다수 |

**헌혈률 통계 (문제의 심각성 근거)**
- 미국: 인구의 **60%가 헌혈 자격**이 있으나 실제 헌혈은 **약 5%**
- 호주: **3.3%만 헌혈**, 그런데 **호주인 3명 중 1명이 평생 수혈을 필요로 함**
- 개발도상국은 헌혈률이 **더 낮음**

## 1-2. ⭐ 핵심 트레이드오프 — 품절(shortage) ↔ 폐기(wastage)

```
재고 과다  →  부패로 인한 폐기 증가
재고 부족  →  인명 위험
```

### 폐기의 특수한 성격 — 경제 문제를 넘어섬

> "wastage is not only an **economic problem** but also has a **normative social effect**, as wasting a unit of blood is a **waste of the donors' time, effort, and contribution** as well."

**Stanger et al.의 관찰**: **병원의 폐기량이 혈액센터보다 현저히 높다**
> "an outdated item at **one hospital could be used at another hospital to help save a person's life**"

→ 이것이 **병원 간 transshipment**의 직접적 동기입니다.

## 1-3. Lateral transshipment — 정의와 선행 근거

**정의**: 재고 시스템의 **동일 에셜론 내 위치 간 재고 이동**. 네트워크 재고를 재배분해 균형을 맞춤.

**선행 연구가 확인한 효과**
| 연구 | 발견 |
|---|---|
| Stanger et al. (영국 혈액공급망 서베이) | 병원 간 transshipment가 **유연성을 높이고 폐기 단위 수를 줄임** |
| Abbasi et al. | 대형 병원 네트워크에서 성능지표 개선 + ⭐ **적혈구 유효기간 단축 가능** → 환자가 **더 신선한 혈액** 수령 |

**문헌 갭**: transshipment 연구는 있으나 ⭐ **부패성 품목, 특히 혈액에 대한 lateral transshipment 연구는 희소**

## 1-4. ⭐⭐ Proactive Transshipment — 이 논문의 핵심 개념

| | **Reactive** (기존) | ⭐ **Proactive** (본 논문) |
|---|---|---|
| 시점 | **수요 실현 후** 비상 대응 | ⭐ **수요 관측 전**, 주문과 **동시에** 결정 |
| 성격 | 사후 응급 조치 | **사전 예방적 재고 재균형** |

> "The **proactive transshipment decisions are made at the same time instant in which hospitals place their orders** to replenish their inventories."
> "at the beginning of each period and **before demand is known** (i.e., observed), the planner can decide to replenish the inventory at the hospitals **either by placing orders to the blood bank or via transshipment** from the other hospitals"

→ 즉 **보충 수단이 2개**(CBB 주문 / 병원 간 이송)이고, 둘 다 **1단계(here-and-now) 결정**입니다.

## 1-5. 시스템 구조와 파라미터

| 항목 | 설정 |
|---|---|
| 의사결정 주체 | ⭐ **중앙집중 계획자(centralized planner)** — 여러 병원을 동시 관리 |
| 병원 집합 | `i ∈ N := {1,…,N}` |
| 기간 | `t ∈ T := {1,…,T}` — **균일 길이, 일반적으로 "일(day)"** |
| 현행 관행 | **(R, S) 주기적 검토 정책**, `R = 1` (일부 소형 병원은 `R = 2`) |
| ⭐ 본 모델 | **(R,S)를 강제하지 않음** — **동적 주기 재고정책** 허용 |
| 주문처 | **중앙 혈액은행 (CBB)** — 매 기간 시작에 발주 |
| 유효기간 | `M` 기간. 미사용 시 폐기, 만료비 `E_i` |
| ⭐ **주문 리드타임** | **1 기간** — `t`에 주문한 것은 **`t+1`부터** 사용 가능 (몇 시간~하루 소요) |
| ⭐ **Transshipment 리드타임** | **즉시 도착** — 일(day) 단위에서 이송 시간은 무시 가능 |
| 미충족 수요 | **응급 주문(emergency order)**으로 충당, 단위비 `G_i` |
| 고정비 | ⭐ **없음** (모델링상 추가는 straightforward하나 **이진변수 필요** — Dillon et al. 방식) |

## 1-6. ⭐ Fig. 1 — 재고 시스템의 시간 동역학

```
[기간 t 시작]
  ① 재고 포지션을 목표 S_i까지 올리도록 주문량 결정  (점선)
  ② Transshipment 결정 → ⭐ 즉시 도착
        ↓
  ③ 수요 D^t_i(ω) 관측  (파선)
        ↓
[기간 t 종료]
  ④ CBB 주문분 도착 → ⭐ 다음 기간부터 가용
  ⑤ 만료 단위 o^t_i(ω) 폐기
```

> ⭐ "Note that **only `y¹_i`, `x_ijk`, `x_jik` are implemented** since the model is optimized at the beginning of each period `t` **in the rolling horizon approach**"

## 1-7. 계산 가능성을 위한 단순화 가정

1. **혈액형 대체(substitution) 없음** — 모든 환자가 자기 ABO형·Rh인자의 혈액 수령
2. **교차적합 거부율 무시**
3. **병원 재고 용량 무제한**

> 이 3가지 덕분에 ⭐ **혈액형을 개별적으로 관리**할 수 있어, 여러 혈액형을 동시에 고려할 필요가 없어집니다.

---

# 2. 기여 (Contributions)

## 저자가 명시한 2대 기여 (§1)

### ⭐ ① 2SSP + Rolling Horizon 결합 프레임워크 — **본 논문의 핵심**

> "we develop a **new 2SSP model** to obtain the optimal order and transshipment quantities using a **flexible methodology to cope with the uncertain nature of demand, i.e., without any assumptions on the demand distribution**. For example, our model can also consider **non-homogeneous demand distribution, which is a novel feature** in the related literature. This is made possible by the **combination of a 2SSP framework with a rolling-horizon strategy** that simulates the daily use of the proposed decision support tool, which together, allows us to benefit from **the flexibility of the 2SSP approach and the computational tractability from the rolling horizon strategy**. The employment of the aforementioned strategy in the context of this research is **novel, to the best of our knowledge**."

**분해하면 3가지 주장**:
1. **수요 분포에 대한 가정 없음** — 분포 자유
2. **비동질(non-homogeneous) 수요분포** 처리 가능 ← 문헌 내 신규
3. **2SSP의 유연성 + RHA의 계산 가능성**을 동시에 확보

### ② Proactive transshipment의 편익 실증

- 혈액공급망 성능지표에 대한 proactive transshipment의 효과 평가
- ⭐ **2개 지점에 한정되지 않은 병원 네트워크**에서 proactive transshipment의 편익에 대한 **최초의 수치적 증거**
- 추가 수치실험: **발주 빈도**, **FIFO 출고정책 강제**, **대안 발주정책**의 영향

## 세분화한 기여

| # | 기여 | 위치 |
|---|---|---|
| **C1** | ⭐ **2SSP를 rolling horizon에 임베딩한 프레임워크** (문헌 최초) | §3, §5 |
| **C2** | MSSP → 2SSP 근사를 위한 ⭐ **2가지 단순화 전제** (2단계 transshipment 배제 + (R,S) 미래 근사) | §3 |
| **C3** | **Proactive transshipment** 개념의 정식화 및 실증 | 전반 |
| **C4** | ⭐ **QMC(Sobol) 시나리오 생성 + in/out-of-sample 안정성 검정** | §4.3 |
| **C5** | 4개 정책 비교 프레임 (TS / TS-FIFO / Current / No Transshipment) | §5 |
| **C6** | ⭐ **(R,S) 근사의 타당성 사후 검증** (Fig. 4) | §5 |
| **C7** | **수혈 시점 혈액 연령(age at transfusion)** 지표 도입 | Fig. 5 |
| **C8** | 보충(replenishment)과 proactive transshipment를 **함께 분석한 최초 연구** | §6 |

---

# 3. 방법론

## 3-1. ⭐⭐ MSSP → 2SSP 근사의 논리 — 이 논문의 방법론적 심장

### 출발점: 왜 MSSP가 아닌가

> "**Multistage stochastic programming is a generalization of the 2SSP that would be more naturally suitable** to represent the dynamics of the problem at hand. However, to avoid problems related to having multiple decision stages and, ultimately, **to make the problem computationally tractable**, very often multistage stochastic programming models are **reformulated and approximated by 2SSP models**."

→ MSSP가 **자연스럽지만**, 계산 가능성을 위해 2SSP로 근사. 그 대가를 **두 가지 아이디어**로 메웁니다.

### ⭐ 아이디어 ①: 미래(2단계) 의사결정의 단순 근사

**전제 1 — 2단계에는 transshipment 결정이 없다**
> "we consider that **no transshipment decisions are available in the second stage** part of the 2SSP model. We stress that this is to **preserve the non-anticipative nature of the transshipment decisions** (as they must be made **before** observing the realization of the demand)."

⭐ **핵심 논리**: transshipment는 **수요 관측 전**에 내려야 하는 결정이므로, 시나리오별로 다르게 정할 수 없습니다(비예측성 위배). 그래서 **2단계에서 아예 배제**하고, 미래의 transshipment는 **롤링 호라이즌에서 다음 기간 재최적화 시** 다룹니다.

**전제 2 — 미래 기간은 (R,S) 시스템처럼 행동한다**
> "we assume that **in future periods, the system behaves as an (R, S) system**, also to enforce non-anticipativity for the decisions made in terms of ordered quantities after the future demand scenarios are observed."

> 이 두 단순화가 **"기대 미래비용의 합리적 근사"**를 갖는 2SSP를 가능하게 합니다.
> 그렇지 않으면 다단계 모델이 되어 *"rendering it an even more computationally challenging problem, and ultimately **compromising its practical appeal**"*

### ⭐ 아이디어 ②: Rolling Horizon 임베딩

> "to guarantee that the **dynamic nature of the decision process is represented**, we embed the 2SSP model into a rolling-horizon approach, meaning that while the decisions are made over a long planning horizon, **only the decisions for the first period (i.e., the first-stage decisions, with the exception of S) are actually implemented**. The process is successively repeated for each period."

### 📐 구조 요약

```
         MSSP (자연스럽지만 계산 불가)
                    ↓
      ┌─────────────┴─────────────┐
      │                           │
  ⓐ 2단계 단순화              ⓑ 롤링 호라이즌
  · transshipment 배제         · 1단계 결정만 실행 (S 제외)
  · (R,S) 미래 근사            · 매 기간 재최적화
      │                           │
      └─────────────┬─────────────┘
                    ↓
        2SSP + RHA = 동역학 복원 + 계산 가능
```

## 3-2. ⭐ 단계 분할 (Stage Split)

| 단계 | 결정변수 |
|---|---|
| **1단계 (here-and-now)** | ① **현재 기간(t=1)의 주문량** `y¹_i` ② **현재 기간의 transshipment량** `x¹_ijm` ③ ⭐ **미래 기간의 목표재고 수준 `S_i`** |
| **2단계 (wait-and-see)** | 각 시나리오 `ω` 하에서 `t ∈ {2,…,T}`의 **주문량** `y^t_i(ω)` |

> ⚠️ **`S_i`는 1단계 변수이지만 "실행되지 않습니다"** — 미래 비용을 근사하기 위한 장치일 뿐이고, 실제로 실행되는 건 `y¹_i`, `x_ijk`, `x_jik` 뿐입니다.

## 3-3. 표기 (Notation)

### 인덱스 · 집합
| 기호 | 의미 |
|---|---|
| `t ∈ T := {1,…,T}` | 시간 호라이즌 |
| `i, j ∈ N := {1,…,N}` | 병원 |
| `ω ∈ Ω := {1,…,Ω}` | **시나리오** |
| `m, k ∈ M := {1,…,M}` | ⭐ **잔여 유효기간 (remaining shelf life)** |

### 결정변수
| 기호 | 의미 |
|---|---|
| `y¹_i` / `y^t_i(ω)` | `t=1` / `t∈{2,…,T}` 시나리오 `ω`의 **주문량** |
| `x^t_ij = (x^t_ij1,…,x^t_ijM)` | ⭐ `i→j` **이송량, 잔여 유효기간 `m`별로 구분** |
| **`S_i`** | 병원 `i`의 **목표 재고 수준** |
| `is^t_im(ω)` | 기간 **시작** 시 유효기간 `m` 재고 |
| `ie^t_im(ω)` | 기간 **종료** 시 유효기간 `m` 재고 |
| `a^t_im(ω)` | 수요 충족에 쓰인 유효기간 `m` 단위 수 |
| `f^t_i(ω)` | **품절량** |
| `v^t_i(ω)` | 기간 종료 시 **총 재고** |
| `o^t_i(ω)` | **만료 폐기량** |
| `b^t_im(ω) ∈ {0,1}` | **FIFO 강제용 이진변수** |

### 파라미터
| 기호 | 의미 |
|---|---|
| `B1_im` | 초기 재고 (유효기간 `m`별) |
| `M` | **최대 유효기간** |
| `G_i` | **응급주문 비용**/단위 |
| `H_i` | **재고유지비**/단위/기간 |
| `E_i` | **만료비**/단위 |
| `R_i` | **주문비**/단위 |
| `C_ij` | `i→j` **이송비**/단위 |
| `P(ω)` | 시나리오 확률 |
| `D(ω)^t_i` | 시나리오 `ω`, 기간 `t`, 병원 `i`의 수요 |

## 3-4. 목적함수 — 5개 비용 요소

**MILP = 2SSP의 결정론적 등가 모델 (deterministic equivalent)**

```
min z = Σ_i R_i y¹_i                            ← ① 주문비 (1단계)
      + Σ_i Σ_{j≠i} Σ_m C_ij x¹_ijm             ← ② 이송비 (1단계)
      + Σ_ω P(ω) [ 기대 재고유지비 H_i           ← ③
                 + 기대 만료비 E_i                ← ④
                 + 기대 품절(응급주문)비 G_i      ← ⑤
                 + t≥2 기대 주문비 R_i ]
```

| 단계 | 포함 비용 |
|---|---|
| **1단계 비용** | 첫 기간의 **주문량 + 이송량** 관련 비용 |
| **2단계 비용** | **전 기간의 기대 재고유지·만료·품절비** + **2기간 이후의 기대 주문비** |

## 3-5. 제약식 구조

| 식 번호 | 역할 |
|---|---|
| **(4.1)–(4.18)** | **기본 모델** — 재고 균형(유효기간별 age profile 추적), 이송 가능량, 수요 충족, **(4.11) = (R,S) 관계식** |
| **(4.19)–(4.22)** | **FIFO 출고정책 강제** — (4.19)(4.20)은 1단계 관련, ⭐ **(4.21)(4.22)는 미래 기간 FIFO로 1단계 결정과 무관** |
| **(4.33)–(4.35)** | 이진변수 `b^t_im(ω)`를 쓰는 FIFO 논리 제약 |
| **(4.36)** | ⭐ out-of-sample 안정성 검정용 대체식 (아래 참조) |

---

# 4. 실험 설정

## 4-1. ⭐ 시나리오 생성 — QMC (§4.3)

### 방법: Quasi-Monte Carlo + Sobol 수열

> "The QMC generates samples known as **low-discrepancy sequences** by employing quasi-random numbers, which are intended to **increase the accuracy of the estimator by generating highly uniform points**. [...] The most widely used low-discrepancy sequence is the **Sobol sequence**."

**채택 이유**: 표본공간을 균일하게 채워 **MC 대비 수렴속도 가속** → ⭐ *"reach stability **without having to consider a prohibitive number of discrete scenarios**"*

> ⚠️ **중요한 성질**: *"the QMC method **generates a unique scenario tree for each sample size since it is not randomized**"* — 무작위화되지 않으므로 표본 크기마다 시나리오 트리가 **유일**하게 결정됩니다.

### ⭐ 안정성 검정 2종

| 검정 | 목적 | 측정 방법 |
|---|---|---|
| **In-sample** | 시나리오를 더 늘려도 **목적함수값이 유의하게 변하지 않는** 최소 개수 결정 | 주어진 시나리오 세트 표본에 대한 **최적 목적함수값의 평균·표준편차** |
| **Out-of-sample** | 얻은 해가 **특정 시나리오 세트에 의존하는지** 검증 | ⭐ 주어진 1단계 해를, **확률분포에서 직접 무작위 추출한 1,000개 시나리오 표본**에서 평가한 목적값의 평균·표준편차 |

> ⚠️ out-of-sample은 **해를 구할 때 쓴 표본(QMC)과 다른 표본**에서 평가한다는 것이 핵심

### 검정 설계
- 비용 데이터·파라미터는 §5와 동일, **`T = 7`** 고정
- ⭐ **20개 시나리오 세트, 크기 10 → 200, 10 단위 증가**
- 시나리오 세트 크기 200 = **`N × T` 크기 행렬 200개** (각 행렬이 시나리오 `ω`, 성분이 `D^t_i(ω)`)

### ⚠️ Out-of-sample 검정 시의 실행가능성 문제
`Σ_m is^t_im(ω)`가 `S_i`를 초과하면 모델이 **infeasible**이 될 수 있음. 이를 피하려고 **(4.11)을 (4.36)으로 대체**:
```
y^t_i(ω) = max{ S_i − Σ_m is^t_im(ω), 0 }
```

### ⭐ 결과 (Fig. 2)

| 검정 | 안정화 지점 |
|---|---|
| **In-sample** | 시나리오 **50개 초과**부터 평균값 변화·표준편차 감소가 유의하지 않음 |
| **Out-of-sample** | 시나리오 **100개 초과**부터 유사한 행태 |

→ ⭐ **`Ω = 100` 채택** — "허용 가능한 안정성을 달성하는 **최소** 시나리오 수"

## 4-2. ⭐ 롤링 호라이즌 시뮬레이션 (§5)

### 규모
| 항목 | 설정 |
|---|---|
| 네트워크 | ⭐ **병원 4곳 — 소형 2 + 대형 2** |
| **시뮬레이션 일수** | ⭐ **18,500일 연속** (실험당 모델을 **18,500번** 풂) |
| 반복 수 근거 | ⭐ **DKW(Dvoretzky–Kiefer–Wolfowitz) 부등식** — 비용 요소의 경험적 누적분포를 **오차 1% 미만, 신뢰수준 95%**로 얻는 데 필요한 시뮬레이션 수 |
| 비교 가능성 | **각 시뮬레이션 실행에서 생성된 동일 시나리오 세트를 모든 실험에 사용** |
| 스텝당 시간 | **1분 미만** (일반 PC) |
| 구현 | **Python 2.7.10** + **IBM ILOG CPLEX 12.6.2** |

### ⭐ Algorithm 1 — 롤링 호라이즌 알고리즘

```
for t = 1, …, 18,500 do
  Step 1: ⭐ 다음 T 기간에 대한 Ω개 수요 시나리오 생성
  Step 2: 현재 기간 t의 초기 재고로 2SSP 모델 실행
  Step 3: 주문 및 transshipment 결정 실행
  Step 4: transshipment 결정에 따라 재고 수준 갱신
  Step 5: 각 병원의 수요 관측 (수요 확률분포에 따라 생성)
  Step 6: ⭐ 관측 수요·만료·입고 주문에 따라 다음 기간 초기 재고 갱신
          — 수요는 각 병원에서 "가장 오래된 단위부터(FIFO)" 충족 (모든 모델 공통)
  Step 7: 현재 기간의 실제 관측 비용 계산
          (재고유지 + 주문 + 이송 + 품절(응급주문))
end
```

> ⭐ **`T`가 등장하는 지점은 Step 1** — 이것이 곧 **예측 호라이즌**입니다.

### ⭐ Step 6에서 왜 모든 모델에 FIFO를 강제하는가

> "at the stage of running the model, the **demand has not been realized yet**. Thus the realized demand (in the implementation/simulation phase) is **not necessarily the same as the demand in the set of demand scenarios** used in the models [...] In other words, the decision variables to determine the demand fulfilment were **scenario indexed and are not actionable in the implementation/simulation phase**."

→ 모델이 내놓은 출고 결정은 **시나리오 인덱스가 붙어 있어 실제 실행 불가**하므로, 시뮬레이션 단계에서는 **일률적으로 FIFO**를 적용합니다.

## 4-3. ⭐ 수요 분포 — Zero-Inflated Negative Binomial (ZINB)

**3개 파라미터**: `φ`(0의 팽창 확률), `r`(시행 횟수), `p`(각 시행의 성공확률)

### 채택 근거 2가지
1. ⭐ **음이항분포는 분산지수(index of dispersion) > 1을 가질 수 있음**
   - 혈액 성분 수요는 **분산지수 > 1**로 알려져 있음
   - **포아송(분산지수 = 1)을 쓰면 수요 변동성을 과소평가**
2. ⭐ **0 팽창** — 특히 **주말에 수요가 없는 날이 잦음** (응급수술 외에는 수술이 예정되지 않으므로)

### 병원별 파라미터
| 병원 | 분포 | 규모 |
|---|---|---|
| **1** | `ZINB(φ=0.6, r=4, p=0.60)` | 소형 |
| **2** | `ZINB(φ=0.6, r=3, p=0.57)` | 소형 |
| **3** | `ZINB(φ=0.25, r=15, p=0.57)` | 대형 |
| **4** | `ZINB(φ=0.25, r=15, p=0.48)` | 대형 |

> 소형 병원은 `φ = 0.6` (무수요일 60%), 대형 병원은 `φ = 0.25`로 대비시킨 설계

## 4-4. 물리적 · 경제적 파라미터

| 항목 | 값 |
|---|---|
| 적혈구 **유효기간** | **21일** |
| ⭐ 병원 **출고 시 평균 연령** | **10일** (채혈센터·처리센터·혈액은행에 평균 10일 체류) |
| ⭐ **병원 수령 시 잔여 유효기간** | **11일** ← 모델의 `M` |

**비용 파라미터** (전 병원 동일, 단위·기간당 화폐단위)

| 비용 | 값 |
|---|---|
| 재고유지 `H` | **1** |
| 주문 `R` | **1** |
| **이송 `C`** | **1.5** |
| **만료 `E`** | **13** |
| **응급주문(품절) `G`** | **16** |

> ⭐ **설계 제약 하나**: *"we assume that the **holding cost for 11 days of one blood unit is strictly less than its outdate cost**. Otherwise, the model would prefer to **discard rather than hold** inventory to meet the demand, which is not aligned with the explicit priorities of this context."*
> 검산: 11일 × 1 = **11 < 13** ✓

## 4-5. ⭐ 비교 대상 4개 정책

| 정책 | 정의 |
|---|---|
| **TS Model** | §4의 제안 모델 |
| **TS-FIFO Enforcement** | TS 모델 + **FIFO 출고정책 강제** |
| **Current Policy** | ⭐ 실제 병원의 현행 정책 시뮬레이션 |
| **No Transshipment** | 이송을 **전혀 사용하지 않음** |

### Current Policy의 구체적 내용
- **일별 검토** 재고정책 — 재고가 `S` 미만이면 `S`까지 끌어올리는 **base stock 정책**
- ⭐ **`S` = 평균 일 수요의 4배**
- **이송 규칙**: 소형 병원(1, 2)이 **잔여 유효기간 6일 미만** 단위를 **지정된 대형 병원 1곳에만** 이송
  - **병원 1 → 3**, **병원 2 → 4** (고정)

### ⭐ 제안 모델이 허용하는 것 (Fig. 3)
> "contrary to the current policy observed, the proposed model allows **both hospitals 1 and 2 to transship units to either of hospitals 3 and 4, as well between them**"

→ **이송 네트워크의 자유도 자체가 다릅니다.**

### ⚠️ FIFO 실험의 근사 처리
> MILP에 **완전 FIFO**를 넣으면 시뮬레이션 실행당 **CPU 20분 초과** → 신뢰할 만한 결과를 얻을 만큼 반복 불가
> → **(4.19)(4.20)만 강제하고 (4.21)(4.22)는 제거**
> 근거: (4.21)(4.22)는 **미래 기간의 FIFO를 강제하며 1단계 결정과 무관**하고, **2단계 결정은 어차피 미래의 근사**이므로 롤링 과정에서 대체됨

## 4-6. 실험의 3중 구성 (저자 명시)

1. **제안 모델의 재고통제 정책 vs 현행 정책 비교**
2. **FIFO 출고정책 강제의 효과**
3. **발주 빈도**(소형 병원 격일 발주) 및 **대안 발주정책**의 영향

---

# 5. 실험 결과

## 5-1. ⭐ W(예측 호라이즌) 실험 — Table 1, p.10

**표기는 `T`** — "the length of the planning horizon to be considered in the two-stage model"

| T | Shortage rate | Outdate rate | Average cost |
|---|---|---|---|
| **7** | 0.013 | 0.006 | ⭐ **76.652** (최저) |
| **10** | ⭐ **0.011** (최저) | 0.007 | 77.472 |
| **20** | 0.016 (최악) | 0.006 | 77.616 (최악) |

> "To decide the length of the planning horizon to be considered in the two-stage model, we performed a **sensitivity analysis considering distinct lengths (i.e., values of T)**. **Trading off computational burden and quality of the solution, we opted for a seven-day planning horizon (T = 7)**. [...] As can be observed in Table 1, **increasing the value of T does not conclusively improve the performance of the optimal policy**, which lead us to believe that a seven-day planning horizon is adequate."

### ⭐ 분석
- **비용은 `T`↑에 따라 단조 악화**: 76.652 → 77.472 → 77.616
- **품절률은 비단조**: T=10에서 최소(0.011)였다가 T=20에서 **재악화**(0.016)
- **폐기율은 거의 무변화** (0.006~0.007)
- → **"길수록 좋다"가 성립하지 않음**

> 💡 `T = 7`이 선택된 데는 **주간 주기성**이라는 물리적 이유도 암묵적으로 작용합니다 — 수요가 요일에 따라 변하고 주말에 거의 0이므로, **7일이 정확히 한 주기**입니다.

## 5-2. Table 2 — 병원별 품절률·폐기율 (품절비 16, 만료비 13)

| 병원 | TS Model<br>품절 / 폐기 | TS-FIFO<br>품절 / 폐기 | Current<br>품절 / 폐기 | No Transship<br>품절 / 폐기 |
|---|---|---|---|---|
| **1** (소형) | 0.024 / 0.014 | 0.023 / 0.025 | 0.006 / 0.000 | 0.000 / ⚠️ **0.387** |
| **2** (소형) | 0.034 / 0.128 | 0.034 / 0.175 | 0.027 / 0.000 | 0.005 / ⚠️ **0.285** |
| **3** (대형) | 0.014 / 0.000 | 0.015 / 0.000 | 0.000 / 0.031 | 0.000 / 0.008 |
| **4** (대형) | 0.010 / 0.001 | 0.010 / 0.001 | 0.016 / 0.001 | 0.022 / 0.001 |
| **Total** | **0.013 / 0.006** | 0.014 / 0.008 | 0.010 / 0.010 | 0.012 / **0.047** |
| **서비스 수준** | 0.986 | 0.986 | 0.990 | 0.988 |

**관찰**
1. ⭐ **4개 정책의 품절·폐기 성능은 서로 비슷** — 저자도 *"the four policies have comparable performance"* 라고 인정
2. ⭐⭐ **No Transshipment의 소형 병원 폐기율이 파국적**: **0.387, 0.285** (TS의 0.014, 0.128 대비)
   → **transshipment가 소형 병원의 폐기를 막는 결정적 장치**임을 입증
3. FIFO 강제 시 **폐기가 오히려 증가** (H1: 0.014→0.025, H2: 0.128→0.175)

## 5-3. Table 3 — 일평균 주문량·이송량

| 병원 | TS<br>주문 / 이송 | TS-FIFO<br>주문 / 이송 | Current<br>주문 / 이송 | No Trans<br>주문 |
|---|---|---|---|---|
| 1 | **1.468** / 0.534 | 1.419 / 0.469 | ⚠️ **2.558** / 1.500 | 1.733 |
| 2 | **0.819** / 0.124 | 0.820 / 0.094 | ⚠️ **1.813** / 0.912 | 1.289 |
| 3 | 9.029 / 0.768 | 9.134 / 0.875 | 7.200 / **0.000** | 8.544 |
| 4 | 11.120 / 0.125 | 11.117 / 0.138 | 11.043 / **0.000** | 11.875 |

⭐ **현행 정책은 소형 병원에서 TS 대비 주문량이 약 1.7~2.2배**입니다. 그런데도 성능은 비슷 → **과잉 주문 중**.
⭐ Current Policy는 대형 병원의 이송이 **0** — 단방향(소형→대형) 규칙 때문.

## 5-4. ⭐⭐ Table 4 — 일평균 비용 구성 (핵심 결과표)

| 비용 요소 | **TS Model** | TS-FIFO | **Current Policy** | **No Transshipment** |
|---|---|---|---|---|
| **재고유지비** | ⭐ **45.294** | 45.133 | ⚠️ **87.615** | 85.117 |
| 주문비 | 22.436 | 22.490 | 22.615 | 23.442 |
| 품절비 | 4.869 | 4.885 | **3.576** | 4.395 |
| **만료비** | ⭐ **1.727** | 2.441 | 2.972 | ⚠️ **14.390** |
| 이송비 | 2.326 | 1.576 | 3.618 | — |
| **평균 총비용** | ⭐ **76.652** | 77.312 | **120.396** | **127.344** |
| 표준편차 | **22.516** | 22.860 | 28.691 | 34.153 |
| 중앙값 | 73.000 | 74.000 | 115.000 | 118.000 |
| 왜도 | 4.093 | 3.977 | 4.460 | 3.032 |
| **P5** | 54.000 | 54.500 | 96.000 | 95.000 |
| **P95** | **105.500** | 107.500 | 159.000 | **189.000** |

### ⭐ 핵심 결론 3가지

**① 현행 정책의 평균 총비용이 TS 모델보다 약 58% 높다**
> "The average total cost for Current Policy is **nearly 58% higher** than those obtained with the TS Model."

**② 절감의 주된 원천은 재고유지비 — 87.6 → 45.3 (거의 절반)**
- 즉 TS 모델은 **훨씬 적은 안전재고로 같은 서비스 수준**을 달성

**③ TS 모델이 고비용 시나리오에 덜 취약하다**
> "the TS Model is **less affected by scenarios of high costs** (as shown by the P5 and P95 values)"
- P95: TS **105.5** vs Current 159.0 vs No Trans **189.0**
- 표준편차: TS **22.5** vs No Trans **34.2**

**④ No Transshipment의 만료비가 14.390 — TS의 8.3배**
→ transshipment의 가치가 **폐기 억제**에 집중되어 있음

**⑤ FIFO 완화가 오히려 이득**
> "relaxing the FIFO constraints in the TS Model might **trigger more transshipment**, which leads to a **lower observed outdate rate** than that observed using the TS-FIFO Enforcement model"
- 이송비 TS 2.326 vs TS-FIFO 1.576 (더 많이 이송), 만료비 1.727 vs 2.441 (덜 폐기)

## 5-5. Tables 5·6 — 소형 병원 격일 발주 (품절비 15, 만료비 12)

**모델 반영 방식**: 발주 불가일에 소형 병원의 주문을 **0으로 고정**하는 제약 추가

**Table 5 — 품절·폐기율**
| 병원 | TS<br>품절/폐기 | Current<br>품절/폐기 | No Trans<br>품절/폐기 |
|---|---|---|---|
| 1 | 0.020 / 0.004 | 0.010 / 0.000 | 0.001 / ⚠️ **0.376** |
| 2 | 0.021 / 0.004 | 0.036 / 0.000 | 0.012 / ⚠️ **0.275** |
| 3 | 0.012 / 0.000 | 0.000 / 0.031 | 0.000 / 0.008 |
| 4 | 0.010 / 0.000 | 0.016 / 0.001 | 0.022 / 0.000 |
| **Total** | 0.012 / ⭐ **0.001** | 0.011 / 0.010 | 0.012 / 0.045 |
| 서비스 수준 | 0.988 | 0.989 | 0.987 |

⭐ **TS 모델이 총 폐기율 최저 (0.001)**

**Table 6 — 일평균 비용**
| 비용 | TS | TS-FIFO | Current | No Trans |
|---|---|---|---|---|
| 재고유지 | **47.771** | 47.575 | 86.702 | 84.248 |
| 주문 | 22.399 | 22.492 | 22.594 | 23.385 |
| 품절 | ⚠️ 4.056 | 4.082 | **3.613** | 4.219 |
| 만료 | ⭐ **0.743** | 1.877 | 2.701 | **12.682** |
| 이송 | 2.743 | 2.737 | 3.494 | — |
| **평균 총비용** | ⭐ **77.712** | 78.399 | 119.103 | 124.534 |

**관찰**
- ⭐ **품절비만 TS가 더 높음** — 저자 설명:
  > "This particular effect is a consequence of the **overall cost minimization perspective** that model adopts [...] The trade-off opportunities exploited by this model could be **straightforwardly controlled by enforcing service-level constraints** (as in Dillon et al.)"
- **격일 발주 제약은 모든 정책의 총비용을 소폭 증가**시킴

## 5-6. ⭐ Fig. 4 — (R,S) 근사의 타당성 사후 검증

**질문**: 미래를 (R,S)로 근사한 전제가 타당한가?

**방법**: 롤링 호라이즌 각 기간에서 **결정된 `S_i` 값**과, **기간 시작 재고 + 해당 기간 실제 주문량**(모델이 실제 구현한 동적 재고정책)을 비교

**결과**
> "the average of the decided `S_i` values for all periods in the rolling horizon approach ('Average S') is **reasonably close** to the average inventory level obtained from the policy implemented by the model ('Average Inventory'). These results indicate that the **simplification of using the (R, S) policy to approximate the inventory system in future periods represents the future behavior of the system reasonably well**."

⭐ 또한 **`S_i`의 표준편차가 상당히 작음** — QMC로 충분한 시나리오를 생성한 결과이며, 앞서 수행한 안정성 검정 결과와 **일관**

> 💡 **차용 가치 높음**: "내가 둔 근사 가정이 타당했는가"를 **실험으로 사후 검증**하는 절차입니다.

## 5-7. Fig. 5 — 수혈 시점 혈액 연령 (Age at Transfusion)

**⭐ 전체 집계 평균 연령**

| 정책 | 평균 연령 (일) |
|---|---|
| **TS-FIFO Enforcement** | ⭐ **11.941** |
| **TS Model** | ⭐ **11.972** |
| Current Policy | 13.812 |
| No Transshipment | **14.230** |

**병원별 관찰**
- **No Transshipment**: 병원 1·2·3에서 **최악**, 병원 4는 타 정책과 유사
- **TS / TS-FIFO**: Current 대비 **대형 병원(3, 4)의 연령 개선**
- ⭐ **Current Policy가 소형 병원(1, 2)에서는 최저 연령** — 현행 정책이 **오래된 단위를 소형→대형으로 이송**하기 때문
- **TS와 TS-FIFO가 유사**한 이유: 시뮬레이션 Step 6에서 **두 모델 모두 FIFO로 출고**되므로

> 💡 **임상적 함의**: 더 신선한 혈액이 환자에게 간다는 것은 비용과 무관한 **독립적 편익**입니다. 비용·폐기 외에 **제3의 지표**를 제시한 좋은 설계.

---

# 6. ⭐ W(예측 호라이즌) 관점 정리

## 6-1. 이 논문에서 W = `T` (2SSP가 바라보는 계획 호라이즌 일수)

| | Cavagnini / Spinelli | **Dehghani** |
|---|---|---|
| 상위 호라이즌 | **유한** `T`가 따로 존재 | ⭐ **없음 — 18,500일 무한 반복** |
| W의 정체 | 전체를 쪼갠 **부분문제 크기** (`1 ≤ W < T`) | ⭐ **매 스텝의 look-ahead 길이** |
| 모델 | **MSSP**를 RHA로 분해 | ⭐ **순수 2SSP**를 RHA로 반복 |
| RHA 역할 | **계산 휴리스틱** | ⭐ **구현 프레임 + MSSP 근사의 정당화 장치** |

> ⭐ **infinite-horizon rolling에서의 예측 호라이즌**이라, 실무 배치 상황에 가장 가까운 형태입니다.

## 6-2. W 실험 요약

| 항목 | 내용 |
|---|---|
| **위치** | §5, **Table 1, p.10** |
| **테스트 값** | **T = 7, 10, 20** (3개) |
| **지표** | shortage rate / outdate rate / **average cost** |
| **선택** | ⭐ **T = 7** |
| **선택 근거** | *"Trading off **computational burden and quality of the solution**"* |
| **핵심 발견** | *"increasing the value of T **does not conclusively improve** the performance"* |
| **분량** | ⚠️ **표 1개 + 3문장** |

## 6-3. ⚠️ 이 W 실험의 한계

| 한계 | 내용 |
|---|---|
| **CPU 시간 미보고** | "computational burden"이라 언급만 하고 **수치 없음** |
| **값이 3개뿐** | 7, 10, 20 — 그 사이(8, 9, 12, 15)가 비어 있음 |
| **T=7의 특수성** | 주간 주기와 일치해 **구조적으로 유리**할 수 있는데 이 점이 논의되지 않음 |
| **교차 실험 없음** | 런타임 제한·시나리오 수·비용 파라미터와 `T`를 교차하지 않음 |
| **별도 절이 아님** | §5 본문에 묻혀 있음 |

---

# 7. 결론 및 후속 연구

## 논문의 자체 요약 (§6)

1. 혈액공급망의 **총비용·폐기·품절을 줄이는 proactive transshipment 의사결정 지원 도구** 제안
2. 불확실 수요 하 병원 네트워크에 대한 **2SSP 모델**로 최적 주문·이송량 산출
3. **QMC 샘플링 + 안정성 분석**으로 신뢰할 만한 시나리오 수 확보
4. **현행 이송 정책 대비 성능 비교** — ⭐ **안전재고 수준과 폐기의 감소를 통한 상당한 비용 편익**
5. ⭐ **수혈 시점 혈액 연령 개선** — 혈액공급망에서 바람직한 결과
6. ⭐ **보충과 proactive transshipment를 병원 네트워크에서 함께 분석한 최초 연구**

## 후속 연구 방향 (저자 명시)

| # | 방향 |
|---|---|
| 1 | **더 일반적인 병원 네트워크**에 모델 배치 |
| 2 | ⭐ **혈액형 대체(substitution) 포함** — 호환 가능한 대체 혈액형으로 수요 충족 |
| 3 | ⭐ **효율적 해법 + 병렬계산 전략** — 더 큰 네트워크, 더 많은 시나리오, **다계층(multiple tiers)** 고려를 위해 |

---

# 8. 우리 연구에 쓸 수 있는 것

## 8-1. 인용 포인트

| 주장 | 근거 |
|---|---|
| ⭐ **"2SSP + RHA는 정통적이고 신규한 접근"** | *"The employment of the aforementioned strategy in the context of this research is **novel, to the best of our knowledge**"* |
| ⭐ **"호라이즌을 늘려도 반드시 개선되지 않는다"** | *"increasing the value of T does not conclusively improve the performance"* + Table 1 |
| **"MSSP는 자연스럽지만 실용성을 해친다"** | *"rendering it an even more computationally challenging problem, and ultimately **compromising its practical appeal**"* |
| **"2SSP의 유연성 + RHA의 계산 가능성"** | §1 기여 진술 |
| **"비예측성 보존을 위해 2단계에서 특정 결정을 배제"** | transshipment를 2단계에서 제외한 논리 |
| **"transshipment는 폐기 억제에 결정적"** | No Transship 소형병원 폐기율 0.387·0.285 vs TS 0.014·0.128 |
| **"현행 실무 대비 58% 비용 절감"** | Table 4 — 120.396 vs 76.652 |
| **"확률적 접근이 꼬리위험에 강하다"** | P95: TS 105.5 vs No Trans 189.0 |

## 8-2. ⭐ 차용할 만한 실험 설계 기법

1. ⭐ **시뮬레이션 반복 수를 통계적으로 결정**
   - **DKW 부등식**으로 "경험적 CDF 오차 1% 미만, 신뢰수준 95%"를 만족하는 **18,500회** 도출
   - "왜 N번 돌렸나"를 **임의 선택이 아니라 정리(theorem)로** 정당화 — 매우 강력

2. ⭐ **안정성 검정 2종 (in-sample + out-of-sample)**
   - in-sample: 목적값이 더 이상 안 변하는 최소 시나리오 수
   - out-of-sample: **해를 구할 때와 다른 표본(분포에서 직접 1,000개 추출)**에서 1단계 해를 평가
   - 결과: in-sample 50, out-of-sample 100 → **더 엄격한 쪽(100) 채택**

3. ⭐ **근사 가정의 사후 검증** (Fig. 4)
   - "(R,S)로 미래를 근사했다"는 전제를, **실제 롤링에서 나온 `S` vs 실제 재고+주문**을 비교해 검증
   - 우리 연구에서도 **"내가 둔 단순화가 타당했는가"를 실험으로 확인**하는 절을 두면 좋음

4. **QMC(Sobol) 채택 + 그 이유 명시**
   - "금지적으로 많은 시나리오 없이 안정성 도달"
   - **비무작위화**라 표본 크기별 트리가 유일하게 결정됨

5. ⭐ **분포 선택의 실증적 정당화**
   - ZINB 채택 근거: 혈액 수요의 **분산지수 > 1** → 포아송은 변동성 과소평가
   - 0 팽창: **주말 무수요**라는 도메인 사실
   - → 분포를 "그냥 정규분포" 쓰지 않고 **도메인 근거로 선택**

6. **비용 파라미터 간 관계를 설계 제약으로 명시**
   - "11일 재고유지비(11) < 만료비(13)" — 아니면 모델이 **보유보다 폐기를 선호**
   - 우리 파라미터에도 이런 **정합성 조건**을 명시하면 설득력 상승

7. **비용 외 제3의 지표 도입**
   - **수혈 시점 혈액 연령** — 비용·서비스수준과 독립적인 임상 지표
   - 분포 통계(평균·표준편차·중앙값·왜도·P5·P95)까지 보고

8. **근사의 대가를 솔직히 보고**
   - 완전 FIFO는 CPU 20분 초과 → (4.21)(4.22) 제거, **그 근거를 명시**

## 8-3. 이 논문의 한계 (우리가 개선할 여지)

| 한계 | 개선 방향 |
|---|---|
| ⚠️ **W 실험이 표 1개 + 3문장** | Spinelli·Cavagnini 수준의 **독립 절 + 그림 + CPU 수치**로 확장 |
| **CPU 시간 미보고** | "computational burden" 언급만 있고 수치 없음 |
| **T=7과 주간 주기의 교락** | 7일이 **정확히 한 주 주기**라 구조적으로 유리. **비주기적 값(6, 8)과 비교**하면 분리 가능 |
| **W와 시나리오 수를 교차하지 않음** | 안정성 검정은 `T=7` 고정 상태에서 수행 → `T`가 커지면 필요한 시나리오 수도 달라질 수 있음 |
| **병원 4곳 고정** | 규모 확장 실험 없음 (저자도 후속 과제로 인정) |
| **혈액형 단일** | 대체 미고려 (저자 인정) |
| **고정비 없음** | 이진변수 필요해서 제외 |
| **서비스 수준 제약 없음** | 총비용 최소화라 품절비가 Current보다 높아지는 부작용. 저자도 *"could be straightforwardly controlled by enforcing service-level constraints"* 라고 언급 |

---

# 9. 다른 논문과의 관계

| 논문 | 관계 |
|---|---|
| **Cavagnini (2022, EJOR)** | ⭐ **Cavagnini가 본 논문을 인용**합니다 (문헌검토의 "Recent papers on transshipments"에 Dehghani, Abbasi & Oliveira 2021 등재). 두 논문 모두 **transshipment + backordering/응급주문**을 recourse로 다루나, Cavagnini는 **reactive**(수요 실현 후), Dehghani는 ⭐ **proactive**(수요 관측 전) |
| **Spinelli (2025, EJOR)** | W 실험의 정교함에서 **정반대 극단**. Spinelli는 이론 보증 + 교차실험, Dehghani는 표 1개. 단 Dehghani가 **순수 2SSP** |
| **Gioia (2023, IJPR)** | 동일 결론 — "길수록 좋지 않다". Gioia는 **터미널 가치**로, Dehghani는 **(R,S) 근사**로 2SSP의 근시안을 보완 → ⭐ **같은 문제의 두 처방** |
| **Curcio (2023, IJPR)** | 둘 다 2SSP를 RH에 임베드. 단 Curcio는 **잔여 전체 호라이즌**(W 없음), Dehghani는 **고정 길이 `T`** |
| **Fattahi (2022, Dec.Sci.)** | Fattahi는 RHA가 **평가 장치**, Dehghani는 **구현 프레임**. Fattahi는 W 고정, Dehghani는 W 스윕 |
| **Dillon, Oliveira & Abbasi (2017, IJPE)** | ⭐ **저자 중복(Oliveira, Abbasi)**. 혈액공급망 2SSP의 선행 연구로 본문에서 2회 인용 (고정비 모델링 / 서비스수준 제약 방식) |

> 💡 **우리 논문의 위치 잡기**: "2SSP를 RHA로 굴린다"는 골격 자체는 **Dehghani가 원형**입니다. 여기서 ⭐ **W를 체계적으로 실험하는 부분**(Spinelli·Cavagnini 수준)과 ⭐ **W와 재계획 주기의 분리**를 보강하면, 두 계보를 잇는 자리가 생깁니다.

---

*문서 생성: Claude Code · Omega 98 (2021) 102112 전문 직접 확인 (본문 기준, Appendix A·B는 목록만)*
