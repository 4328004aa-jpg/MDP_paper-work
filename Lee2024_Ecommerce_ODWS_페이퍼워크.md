# 페이퍼워크 — Lee, Ko & Moon (2024)

**"E-commerce supply chain network design using on-demand warehousing system under uncertainty"**

| 항목 | 내용 |
|---|---|
| 저자 | **이준혁**(서울대 산업공학과 박사과정), **고창성**(경성대 산업경영공학과), **문일경**(서울대 산업공학과 / 산업시스템혁신연구소, 교신저자) |
| 저널 | ***International Journal of Production Research*** **62**(5), 1901–1927 (2024) |
| 온라인 게재 | **2022-10-06** |
| DOI | `10.1080/00207543.2022.2128462` |
| 접수 / 게재승인 | 2022-05-02 / 2022-09-14 |
| 연구비 | 한국연구재단(NRF) **NRF-2019R1A2C2084616** |
| 선행 | 학회 논문 **Lee, Park & Moon (2021)** 을 확장 (공급자 선택 결정 + 수요·공급 불확실성 추가) |

---

# ⚠️ 먼저 — 이 논문에는 **Rolling Horizon이 없습니다**

전문을 검색했으나 rolling horizon / receding horizon / 주기적 재최적화가 **등장하지 않습니다.** 정적(static) **2SSP + SAA + Benders 분해**입니다.

**그런데 결론의 future research가 정확히 그 지점을 가리킵니다** → §7에서 상세히 다룹니다. 이 논문은 선생님 연구의 **"직전 단계 + 갭 선언"** 으로 쓰기에 가장 좋은 레퍼런스입니다.

---

# 1. 문제 상황

## 1-1. 배경 — 왜 On-Demand Warehousing인가

**출발점**: COVID-19로 e-commerce 수요가 급증했으나, **소매업체의 창고 용량이 부족**.

> "most e-commerce retailers run their businesses with **low capital investment**. Therefore, e-commerce retailers usually operate warehouses with **small spaces**"

### 기존 3가지 해법과 그 한계

| 해법 | 한계 |
|---|---|
| ① 신규 창고·인프라 건설 | ⚠️ **대규모 자본투자 필요** |
| ② 전통적 창고운영사·3PL로부터 임차 | ⚠️ **계약기간이 길고 장기계약 요구** → 유연성 필요한 e-commerce에 부적합 |
| ③ ⭐ **ODWS (On-Demand Warehousing System)** | 본 논문의 대상 |

### ODWS란
**플랫폼이 잉여 공간을 가진 창고 운영자(provider)와 단기 공간이 필요한 e-commerce 소매업체를 연결**하는 서비스.
실제 사례: **FLEXE** (글로벌 시장)

### ⭐ ODWS의 3가지 이점 (저자 명시)

| # | 이점 | 설명 |
|---|---|---|
| 1 | **셋업비용 절감** | 추가 인프라 불필요 → **계절상품**의 변동 수요 대응·재고 넘침 방지에 저위험 전략 |
| 2 | **높은 유연성** | 창고·풀필먼트를 **즉시** 확보. 사업 동태에 맞춰 유연한 유통망 구축 |
| 3 | **배송 속도** | 창고가 고객과 멀면 당일배송 불가 → ODWS로 **고객 기반 근처의 공유 창고 공간**을 탐색 가능 |

> 팬데믹발 소비 지출이 얼마나 지속될지 확신할 수 없어, **중소 e-commerce 기업들이 ODWS를 선호**한다는 실무 동향(Leonard 2021) 인용

## 1-2. ⭐ 핵심 개념 — Commitment (단기 임차 계약)

> "Throughout this paper, we will use the terminology **commitment** to indicate the **short-term rent contract for warehouses** in the ODWS."

**이것이 ODWS의 본질적 장점이자 이 논문의 핵심 의사결정 대상**입니다.

### Commitment 작동 방식 (Figure 2a) — 6기간 예시

2-기간·3-기간 두 종류의 commitment가 가능한 경우:

```
기간:      1    2    3    4    5    6
         ├────3-period────┤    ├──2-period──┤
사용가능:  ✓    ✓    ✓    ✗    ✓    ✓
```
- **기간 1**에 3-기간 commitment → 창고 `k`를 기간 1~3 사용 가능
- **기간 4**는 commitment를 안 했으므로 **사용 불가**
- **기간 5**에 2-기간 commitment → 기간 5~6 사용 가능

### ⭐ 비용 함수 — 장기 약정 할인

```
m-기간 commitment 비용 = α^m · θ
```
- `α` = **할인 계수(discount factor)**, `θ` = 1기간 사용 commitment 비용
- **더 긴 기간을 약정할수록 더 큰 할인**을 받는 현실을 반영

### Figure 2(b) — 기간 1~3을 커버하는 4가지 방법

| Case | 구성 | 비용 |
|---|---|---|
| **1** | 기간 1에 **3-기간** 약정 1회 | ⭐ **$257.2 (최저)** |
| 2 | 2-기간 + 1-기간 | — |
| 3 | 1-기간 + 2-기간 | — |
| 4 | 1-기간 × 3회 | 최고 |

⚠️ **하지만 트레이드오프가 있습니다**:
> "committing for a long period to use warehouses could **incur unnecessary costs** due to the long-term use of warehouses, **although there is small customer demand**"

→ **수요가 작게 실현되면 장기 약정이 낭비**가 됩니다. 이것이 불확실성 하에서 commitment 기간을 정해야 하는 이유입니다.

## 1-3. 공급망 구조

**대상 시장**: ⭐ **한국 e-commerce 시장**
**의사결정자**: 소매업체(retailer)
**성격**: **다품목(multi-item) · 다기간(multi-period)**

### 3가지 창고 유형

| 유형 | 개수 | 특징 |
|---|---|---|
| **Retailer warehouse** (자사 창고) | 1개 | 용량 `C^r` |
| ⭐ **Provider warehouse** (ODWS 플랫폼) | 여러 개, `k ∈ K` | 용량 `C^k`. **약정한 기간에만 사용 가능** |
| **Emergency warehouse** (비상 창고) | 1개 | 용량 `C^e`. ⚠️ **단위 재고유지비·운송비가 훨씬 높음** |

> 모델은 다수의 retailer·emergency 창고로 확장 가능하나, **모든 계산실험은 각 1개 설정**에서 수행

### 창고의 3가지 역할 (모두 물류센터 기능)
1. 공급자로부터의 선적물 집결, **차량 적재 분해(de-aggregation)**
2. 용량이 차지 않았으면 **단기/장기 보관**
3. 고객 수요에 따라 **분류·가공·포장** 후 고객 배송

⭐ **Provider warehouse는 위 역할이 "약정한 기간에만" 적용됩니다.**

## 1-4. 의사결정의 흐름 (Figure 1)

```
[불확실성 실현 전 — 전략적 결정]
  ① 공급자 선택  y_j       (투자비 F_j, 품질·변동성·위치 고려)
  ② Provider 창고 약정  g^k_mt, r^k_mt
        ↓
   ━━━ 수요 D, 공급 S 실현 ━━━
        ↓
[불확실성 실현 후 — 운영적 결정]
  ③ 공급자→창고 운송  x^r_ijt, x^k_ijt, x^e_ijt   (리드타임 L_s)
  ④ 창고 재고 보유    v^r_it, v^k_it, v^e_it
  ⑤ 고객 배송         u^r_it, u^k_it, u^e_it      (리드타임 L_d)
  ⑥ 미충족 수요 = lost sales  z_it                (페널티 π_i)
```

## 1-5. ⭐ 불확실성 — 수요 + **공급(수율)**

```
ξ = (D, S)
```

- `D^ω_it`: 시나리오 `ω`, 기간 `t`, 품목 `i`의 **집계 수요**
- `S^ω_ijt`: 공급자 `j`의 **공급량**

> ⭐ "we define the supply uncertainty form as **yield uncertainty**, which means **the amount actually supplied is random and different from the amount ordered**."

**유한·이산 지지집합**, 시나리오 집합 `Ω`, 확률 `p_1,…,p_|Ω|`

## 1-6. 가정

| # | 가정 | 근거 |
|---|---|---|
| 1 | 미충족 수요는 **lost sale** | *"customers are more likely to **switch to another website** to search for substitute items rather than wait"* |
| 2 | ⭐ **부패성 품목 제외** | 냉장 시스템이 필요한데, **ODWS는 여러 사용자가 이질적 제품을 같은 공간에 보관**하므로 온·습도 조건 유지가 곤란 |
| 3 | ⭐ **창고 간 lateral transshipment 없음** | ⒜ 창고 수에 따라 결정변수가 **지수적으로 증가** ⒝ **provider 창고를 매 기간 열고 닫을 수 있으므로** transshipment 연결이 상쇄됨 |
| 4 | 집계 고객수요 + **3PL 라스트마일** | 한국 e-commerce 시장 관행. 배송비 `b_i`는 무게·목적지 무관하게 동일 |
| 5 | 운송비는 XY 평면 **유클리드 거리에 비례** | — |

---

# 2. 기여

## 2-1. 문헌 갭 (저자 명시 2개)

**Gap 1 — Commitment(단기 임차 계약)를 다룬 연구가 없음**
> 기존 ODWS 연구들은 ODWS의 핵심 특성인 단기 임차 계약을 다루지 않음. **Unnu & Pazour (2022)만 예외**인데, 그들도 **가용 약정 기간을 "주어진 파라미터"로 취급**했지 **결정 대상**으로 삼지 않음

**Gap 2 — 내재적 불확실성을 체계적으로 고려한 연구가 희소**
> 수요 불확실성을 고려한 연구는 일부 있으나(Van der Heide et al. 2018; Shi, Yu & Dong 2021), ⭐ **commitment 특성과 불확실성을 동시에 다룬 연구는 없음**

> "To the best of our knowledge, this study is the **first attempt to solve the problem considering the properties of commitment and uncertainties simultaneously** in the ODWS research area."

## 2-2. 4가지 연구 질문

| RQ | 내용 |
|---|---|
| **1** | ODWS 기반 SCND에서 **불확실성을 어떻게 고려**하고, 계산 부담을 줄이는 **해법을 어떻게 설계**할 것인가? |
| **2** | ODWS가 **공급망 네트워크와 총비용**에 어떤 영향을 주는가? |
| **3** | **commitment 비용과 stockout 비용이 변할 때** 총비용과 창고 활용도에 어떤 영향이 있는가? |
| **4** | **리드타임**은 ODWS 공급망에 어떤 영향을 주는가? |

## 2-3. 3가지 기여

| # | 기여 |
|---|---|
| **C1** | ODWS 기반 e-commerce SCND를 위한 **2단계 확률계획(TSSP) 모델** + 기댓값 함수 추정을 위한 **SAA** |
| **C2** | SAA의 계산 부담 완화를 위한 **multi-cut Benders 분해(BD)** + ⭐ **초기 최적성 컷 생성 가속기법(ABD)** |
| **C3** | 계산실험을 통한 **ODWS의 잠재적 비용절감 효과** 실증 |

## 2-4. Table 1 — 선행연구 비교축

저자들이 설정한 비교 차원:
**on-demand warehousing / multi-item / multi-period / capacity constraint / commitment granularity / duration period decision / uncertainty factors / solution methodology**

본 논문의 위치: **모든 축 충족 + 불확실성(demand, supply) + TSSP, SAA + BD**

---

# 3. 방법론

## 3-1. TSSP 모델 구조

> "the SCND model, which is developed based on the **multi-stage, capacitated, multiple-sourcing, multi-item, and dynamic FL(facility location) model**. In order to model the problem under uncertainties, we extend the deterministic model as the TSSP."

### 집합
| 기호 | 의미 |
|---|---|
| `T` | 기간 `t = 1,…,T` |
| `I` | 품목 `i = 1,…,I` |
| `J` | 공급자 `j = 1,…,J` |
| `K` | **provider 창고** `k = 1,…,K` |
| `R` / `E` | retailer / emergency 창고 |
| ⭐ `M` | **가용 약정 기간(available commitment periods)** `m = 1,…,M` |
| `Ω` | 시나리오 |

### 단계 분할

| 단계 | 결정 | 성격 |
|---|---|---|
| **1단계 (here-and-now)** | **공급자 선택 `y_j`** + **provider 창고 약정 `g`, `r`** | 전략적 |
| **2단계 (wait-and-see)** | 운송 `x`, 재고 `v`, 배송 `u`, lost sales `z` | 운영적 |

### 주요 비용 파라미터
| 기호 | 의미 |
|---|---|
| `F_j` | 공급자 `j`와 협력하기 위한 **투자비** |
| `c^r_j, c^k_j, c^e_j` | 공급자→각 창고 **단위 운송비** |
| `h^r_i, h^k_i, h^e_i` | 창고별 **단위 재고유지비** |
| `b_i` | **파슬당 배송비** |
| `π_i` | **lost sale 페널티** |
| `θ`, `α` | **약정 단위비용**, **할인 계수** |
| `L_s`, `L_d` | 공급자→창고 / 창고→고객 **리드타임** |

§3.3에 **compact formulation**도 제시됩니다.

## 3-2. ⭐ 해법 — SAA + Benders + 가속

### §4.1 Sample Average Approximation (SAA)

**근본 난점**: 기댓값 함수 `E_ξ[Q(y, r, ξ)]` 계산

`ξ¹,…,ξ^N`을 i.i.d. 표본으로 두고 SAA 문제를 풂:
```
min  f·y + e·g + (1/N) Σ_{n=1}^{N} Q(y, r, ξ^n)
```
> `N`이 커지면 SAA 목적함수가 **확률 1로 참 목적함수에 수렴** (Kleywegt, Shapiro & Homem-de-Mello 2002)

**SAA + BD 조합을 택한 이유** (저자 명시)
- SAA는 **매우 범용적** — 결정론적 최적화에 특화된 다양한 알고리즘과 결합 가능
- SAA는 **valuable convergence properties**를 가짐
- BD는 변수를 일시적으로 고정해 문제를 다루기 쉽게 만듦
- ⭐ BD는 **완화문제가 아니라 MILP의 최적해로 수렴**

### §4.2 Multi-cut Benders Decomposition

### §4.3 ⭐⭐ 가속기법 (ABD) — 이 논문의 알고리즘적 핵심

**문제 진단**
> 전형적 BD(TBD)는 **최적성 컷이 빈 상태**로 Master Problem(MP)을 처음 풂
> → **형편없는 실행가능해** (`ȳ, ḡ, r̄ = 0`, `η̄`가 음수)
> → *"poor solutions tend to generate **ineffective cuts**"*
> → 종료조건까지 **반복 횟수 폭증** → 총 계산시간 증가

**해법**: **Expected Value Problem (EVP)**의 해(EVS)로 **더 좋은 초기 컷**을 만든다

```
[가속기법 절차]
Step 1: EVP를 상용 솔버로 풂
        (계산시간 30초 이내 OR 갭 5% 이내 중 먼저 도달할 때까지)
        → 기댓값 해 ȳ, ḡ, r̄ 획득
Step 2: 획득한 EVS 기반으로 각 시나리오 ω의 SUB(ω)를 풂
        → 최적 목적값 Q(ȳ, ḡ, ω)와 최적 쌍대해 획득
Step 3: 위 결과로 초기 최적성 컷 생성
        → 이후는 일반 BD와 동일
```

> 💡 **아이디어의 핵심**: *"The EVS can sometimes be a **high-quality solution** to the true problem. By utilising this property, we utilise the EVS to generate **better initial cuts** than the typical one at the initialisation step."*

---

# 4. 실험 설정

## 4-1. 테스트 인스턴스 (§5.1)

> ⚠️ **"there are no existing benchmark instances corresponding to our problem"** → 실제 데이터 기반으로 직접 생성

| 파라미터 | 출처 |
|---|---|
| 재고유지비 `h^r_i, h^k_i` | **Hass (2019)** 논문 기반 |
| 배송비 `b_i` | ⭐ **한국 택배 서비스 요금** 기반 |
| 기타 결정론적 파라미터 | 균등분포 무작위 생성 (Table A1) |

### ⭐ 수요 분포 추정 — 실제 데이터
> "we used the **e-commerce public dataset (Kaggle 2018)**, which consists of demand data for **614 time periods, from September 4, 2016 to September 3, 2018**. Then, we **fitted the normal distribution** to this dataset"

- 공급 `S^ω_ijt`도 **수요와 동일한 분포**를 채택
- 음수로 실현된 수요·공급은 **0으로 처리**

### 기타 설정
- 공급자·창고 위치는 사전 지정된 **XY 평면**에 균등분포
- 운송비는 **유클리드 거리에 비례**
- ⚠️ Emergency 창고의 `c^e_j`, `h^e_i`는 **다른 창고보다 현저히 큼**
- 문제 크기는 `|I|, |J|, |T|, |K|, |M|`로 결정
- **총 15개 인스턴스**, **13~15번이 대형(large-sized)**

**Table 2 예시** (N=40 기준)
| No. | XY | 총 변수 | 이진 | 연속 | 제약 | \|I\| | \|J\| | \|T\| | \|K\| | \|M\| |
|---|---|---|---|---|---|---|---|---|---|---|
| 1 | 100×100 | 29,103 | 153 | 28,950 | 14,000 | 2 | 3 | 10 | 5 | 3 |
| 2 | — | 32,899 | 131 | 32,768 | 15,104 | 2 | 3 | 8 | 8 | 2 |
| 3 | — | 73,844 | 324 | 73,520 | 26,000 | 3 | 4 | 10 | 8 | 4 |

### ⭐ 절별 설정 차이 (읽을 때 주의)
| 절 | Emergency 창고 | 리드타임 |
|---|---|---|
| §5.2, §5.3 | **용량 무제한** | `L_s = L_d = 0` |
| §5.4 | ⭐ **용량 제한 `C^e = 70`** | **변화시킴** |

> Emergency 창고 용량을 §5.2–5.3에서 무제한으로 둔 이유: *"we observed that the emergency warehouse was **rarely used** because of the high operational cost compared to the stockout cost"*

## 4-2. 실험 프로토콜

| 항목 | 설정 |
|---|---|
| 솔버 | **Xpress-Optimizer** |
| 시간 제한 | **3,600초** |
| BD 수렴 임계 `ε_BD` | **10⁻⁴** (§5.2) / **10⁻³** (§5.3) |
| 반복 | 각 `(N, 해법)` 조합당 **10회 실험**, 평균 보고 |
| SAA 설정 (§5.3) | `N = 3000`, `M = 20`, `ε_SAA = 1%` |
| 하한 계산용 시나리오 수 | `N ∈ {20, 40, 60, 80, 100, 200}` 점증 |

---

# 5. 실험 결과

## 5-1. §5.2 — 알고리즘 성능 (Solver vs TBD vs ABD)

**Figure 3 / Table A3**

⭐ **ABD가 TBD와 Solver를 모든 평가지표(Gap, CPUs, Itr, 시간제한 도달 횟수)에서 능가**

**추가 관찰**
- `N`이 커질수록 계산시간 증가 — 그러나 ⭐ **BD 알고리즘의 시간 증가가 Solver보다 훨씬 완만**
- ⭐ **`N`이 커질수록 필요 반복 횟수는 오히려 감소** — 1회 반복에 더 많은 시간이 들기 때문

## 5-2. §5.3 — 확률해의 가치 (VSS)

```
VSS = EEV − RP
```
(EEV = 기댓값해의 기대 결과, RP = recourse 문제 최적값)

**결과**
- ⭐ **EVS가 확률해보다 훨씬 높은 총비용**을 초래
- → *"indicated the **importance of capturing the stochastic nature of demands and supply** for designing the supply chain"*
- 모든 인스턴스에서 **`N < 100`이면 SAA 갭 1% 이내** 달성
- ⭐ **인스턴스 8~15는 `N = 20`만으로도** 고품질 확률해 획득

## 5-3. §5.4 — ODWS의 효과 (인스턴스 10, N=40) — 4개 실험

### 실험 ① 가용 provider 창고 수 `K_max` 변화 (Table 6)

```
Utilisation := Σ_k Σ_m Σ_t r^k_mt
```

| 관찰 | 내용 |
|---|---|
| `K_max` ↑ (8까지) | 활용도 증가, **총비용 감소** |
| ⚠️ `K_max > 8` | **활용도가 30 → 29로 오히려 감소** |
| `K_max = 1` | **최고 총비용** (창고 하나로 수요 충족이 곤란) |
| ⭐ `K_max ≥ 9` | **활용도·총비용이 더 이상 변하지 않음** → **10~20개를 쓴다고 나아지지 않음** |

### 실험 ② 약정 비용 `θ` 민감도 (Figures 5, 6)

| 관찰 | 내용 |
|---|---|
| `θ` ↑ | 활용도 ↓, 총비용 ↑ (약정이 비싸 provider 창고 기피) |
| ⭐ **`θ > 7500`** | **총비용 불변, 활용도 = 0** → provider 창고를 **전혀 사용하지 않음** |
| `θ > 5000` | **약정비용 비중이 증가하다 감소로 전환**, 동시에 **stockout 비용 비중은 감소하다 증가로 전환** |
| 재고유지비 비중 | ⚠️ **무시할 수준** (`h`가 다른 비용 파라미터보다 훨씬 작음) |

⭐ **도출된 인사이트**
> "allowing for the condition of **stockout for most of the demands is a better cost-saving strategy** compared to using provider warehouses **when the `θ` is significantly higher than the stockout cost parameter `π_i`**"

### 실험 ③ Lost sale 비용 `π_i` 민감도 (Figures 7, 8) — `C^e = 70`

```
UEW := (1/|Ω|) Σ_ω Σ_i Σ_e Σ_t u^e_it   (emergency 창고 활용도)
```

| `π_i` | 현상 |
|---|---|
| ~150 | **UEW = 0** — emergency 창고 미사용, 미충족 수요는 **전부 lost sale**. 총비용·stockout 비용 **급증** |
| ⭐ **160** | **UEW가 0 → 약 32로 급등** (임계점). stockout 비용 **급락** |
| 160~600 | UEW 소폭 증가, 총비용 소폭 증가, stockout 비용 소폭 증가 |
| ⭐ **> 650** | **stockout 비용 = 0** (모든 수요 충족). UEW·총비용 불변. **공급자 비용 비중 증가** → 추가 공급자 채택 |

→ **stockout 비용과 운송비 비중이 서로 반대 방향으로 움직임**

### 실험 ④ 리드타임 효과 (Figure 9, Table A4)

⚠️ **전제**: 리드타임이 있으면 계획 초기에 막대한 stockout 비용 발생 → **초기 재고를 기대수요와 같게** 설정

한 종류만 변화시키고 다른 하나는 0으로 고정해 비교:

| 관찰 | 내용 |
|---|---|
| `L_s`, `L_d` 증가 | **총비용·stockout 비용 증가** |
| 배송비 비중 | 감소 (총 stockout 증가 때문) |
| ⭐ **약정비용 감소** | → **provider 창고 활용도도 감소** |
| ⭐ **핵심** | **`L_d`(창고→고객)를 늘릴 때 총비용이 `L_s`(공급자→창고)보다 훨씬 급격히 증가** |

## 5-4. §5.5 — 관리적 시사점

**① 적정 개수의 provider 창고가 최선**
> "Utilizing the ODWS can save on the total cost [...] because it has a **similar effect as expanding capacity flexibly**. Even though most of the demands can be satisfied with enough provider warehouses, we could observe that **using a moderate number of provider warehouses is a good strategy** for minimising total cost."

→ **공급자와 provider 창고의 위치 + 적정 개수**를 함께 고려해야 함

**② 불확실성을 정확히 다루는 것이 결정적**
> "Our proposed model is **very sensitive to uncertainty** because frequent stockouts could occur when insufficient provider warehouses are committed. Even though simple solution approaches could solve the problem (e.g. EVP), **most obtained solutions are imprecise for acceptable decision-making**."

---

# 6. 결론

1. ODWS + SCND를 **불확실성 하에서 다룬 최초 연구**
2. **TSSP** 모델 + **SAA + BD** 해법, 특히 **ABD**가 TBD·Xpress를 갭·계산시간 모두에서 능가
3. SAA 확률해가 EVP 해보다 우수
4. ODWS 활용이 **소수의 제한된 용량 창고를 쓰는 것보다 총비용 절감**
5. 약정비용·stockout 비용 간 관계, 두 리드타임의 영향, 관리적 시사점 도출

---

# 7. ⭐⭐ 가장 중요한 부분 — Future Research가 곧 선생님 연구의 자리입니다

## 논문이 스스로 선언한 한계

> "For further research, we intend to extend our study by using **multi-stage stochastic programming**, which has an advantage for dealing with uncertainty under a multiperiod setting.
> ⭐ **The nature of TSSP enables the stochastic parameters to become known in a single moment.**
> However, regarding the problem with a **planning horizon with multiple periods**, the uncertainty can be dealt with **more accurately when the stochastic parameters have been realised progressively in each period**.
> Therefore, through utilising the above scheme, **some decisions will be made before the realisation of uncertainty, and other decisions are made after the realisation in each period**." (Govindan, Fattahi & Keyvanshokooh 2017 인용)

## 이게 왜 결정적인가

저자들이 지적한 TSSP의 한계는 **정확히 Rolling Horizon이 해결하는 문제**입니다:

| 저자들이 말한 한계 | RHA가 주는 답 |
|---|---|
| "불확실성이 **한 순간에** 모두 드러난다" | **매 기간 재최적화**하여 순차 실현 반영 |
| "다기간 계획 호라이즌에서는 **점진적 실현**이 더 정확" | 롤링이 바로 그 구조 |
| "일부 결정은 실현 전, 나머지는 **각 기간 실현 후**" | **2SSP를 매 기간 반복** = 그 스킴 |
| 해법으로 **MSSP**를 제안 | ⚠️ 그런데 MSSP는 시나리오 트리 폭발 |

⭐ **그리고 Dehghani, Abbasi & Oliveira (2021, Omega)가 정확히 이 논리로 2SSP+RHA를 정당화합니다**:
> "Multistage stochastic programming is a generalization of the 2SSP that would be **more naturally suitable** [...] However, to avoid problems related to having multiple decision stages and, ultimately, **to make the problem computationally tractable**, very often multistage stochastic programming models are **reformulated and approximated by 2SSP models**."

→ **Lee et al.이 "MSSP로 가겠다"고 한 길을, Dehghani는 "MSSP 대신 2SSP+RHA"로 우회**한 것입니다.

## 💡 논문 도입부에 쓸 수 있는 서사

```
① Lee, Ko & Moon (2024, IJPR)  — e-commerce + ODWS를 2SSP로 정식화.
   그러나 저자들 스스로 "TSSP는 불확실성이 한 순간에 드러난다"는 한계를 지적하고
   다기간 점진적 실현을 후속 과제로 제시.

② MSSP는 그 답이 될 수 있으나 시나리오 트리가 지수적으로 폭발
   (Cavagnini et al. 2022: 6단계에서 GUROBI out-of-memory).

③ Dehghani et al. (2021, Omega)는 MSSP 대신 2SSP + RHA로 우회.
   그러나 예측 호라이즌 W의 크기는 표 하나 수준의 보정에 그침.

④ 본 연구는 ①의 e-commerce/ODWS 세팅에 ③의 2SSP+RHA를 적용하고,
   ③이 남긴 W 선택 문제를 체계적으로 다룬다.
```

이 흐름이면 **한국 e-commerce 맥락 + 방법론 갭**이 한 줄로 이어집니다.

---

# 8. 이 논문과 다른 논문들의 관계

| 논문 | 관계 |
|---|---|
| ⭐ **Dehghani (2021) Omega** | **MSSP의 한계 진단이 동일**. Lee는 "MSSP로 가겠다", Dehghani는 "2SSP+RHA로 우회" → **직접 대비** |
| **Fattahi & Govindan (2022) Dec.Sci.** | Lee가 인용한 **Govindan, Fattahi & Keyvanshokooh (2017)** 과 같은 그룹. Fattahi는 **MSSP + data-driven RHA**로 실제로 그 길을 감 |
| **Curcio et al. (2023) IJPR** | 동일 저널. **2SSP를 RH에 임베드해 다단계 세팅으로 적응** — Lee가 말한 "후속 과제"의 한 구현 |
| **Gioia et al. (2023) IJPR** | 동일 저널. 2SSP의 근시안을 **터미널 가치**로 보완 — MSSP·RHA와는 **제3의 길** |
| **Cavagnini (2022) EJOR** | MSSP의 계산 폭발을 실증 (6단계 OOM) — Lee가 MSSP로 갈 때 마주칠 벽 |

---

# 9. 우리 연구에 쓸 수 있는 것

## 9-1. 인용 포인트

| 주장 | 근거 |
|---|---|
| ⭐ **"2SSP는 불확실성이 한 순간에 해소된다는 한계가 있다"** | 본 논문 §6 원문 — **저자 자신의 진술**이라 반박 불가 |
| ⭐ **"다기간 세팅에선 점진적 실현이 더 정확하다"** | 동일 |
| **"e-commerce는 창고 용량 제약이 핵심 문제"** | §1 — COVID-19 맥락 |
| **"ODWS/commitment 의사결정 연구가 희소"** | Gap 1·2 |
| **"확률적 접근이 EVP보다 확실히 낫다"** | §5.3 VSS |
| **"창고를 무한정 늘려도 소용없다"** | `K_max ≥ 9`에서 효과 소멸 |
| **"창고→고객 리드타임이 공급자→창고보다 치명적"** | 실험 ④ |

## 9-2. 차용할 만한 실험 설계

1. ⭐ **실제 공개 데이터로 분포 추정** — Kaggle e-commerce 데이터 614기간(2016.9~2018.9)에 정규분포 적합
2. **벤치마크가 없으면 실무 근거로 파라미터 생성** — 재고비는 선행논문, 배송비는 **한국 택배 요금**
3. ⭐ **가속기법의 아이디어** — EVP 해로 **초기 Benders 컷**을 만들어 수렴 가속. RHA의 warm start와 발상이 같음 (Cavagnini §7.3.1과 비교 가능)
4. **절별로 설정을 바꿔 목적을 분리** — §5.2-5.3은 emergency 무한용량·리드타임 0으로 **알고리즘에 집중**, §5.4는 제약을 켜서 **정책 분석에 집중**
5. **임계점(threshold) 탐색형 민감도** — `θ > 7500`, `π_i = 160`, `π_i > 650`, `K_max ≥ 9` 처럼 **"어느 값에서 행동이 바뀌는가"** 를 찾아 보고

## 9-3. 이 논문의 한계 (= 우리의 기회)

| 한계 | 우리가 할 수 있는 것 |
|---|---|
| ⭐ **Rolling Horizon 없음** — 정적 2SSP | **2SSP + RHA로 확장** (저자가 후속 과제로 지목) |
| ⭐ **예측 호라이즌 개념 자체가 없음** | **W 크기 실험** 추가 |
| 저자 제안은 **MSSP** | MSSP는 트리 폭발 → **RHA가 더 실용적**임을 논증 |
| **부패성 품목 제외** | 신선식품 e-commerce로 확장 여지 |
| **창고 간 transshipment 없음** | Dehghani의 **proactive transshipment**와 결합 여지 |
| 리드타임은 **고정 상수** | 확률적 리드타임 |
| **out-of-sample 시뮬레이션 평가 없음** | 롤링 시뮬레이션으로 정책 성능 평가 (Dehghani·Gioia 방식) |

> 💡 마지막 두 줄이 특히 좋습니다 — **"e-commerce/ODWS 세팅 + 2SSP를 RHA로 굴리고 + W를 체계적으로 실험하고 + out-of-sample로 평가"** 라는 조합은 지금까지 조사한 범위에서 **비어 있습니다.**

---

*문서 생성: Claude Code · IJPR 62(5), 1901–1927 전문 직접 확인 (Appendix Table A1–A4는 본문 참조 기준)*
