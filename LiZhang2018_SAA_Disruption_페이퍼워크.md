# 페이퍼워크 — Li & Zhang (2018)

**"A sample average approximation approach for supply chain network design with facility disruptions"**

| 항목 | 내용 |
|---|---|
| 저자 | **Xueping Li**, **Kaike Zhang** — Dept. of Industrial and Systems Engineering, **University of Tennessee, Knoxville** |
| 저널 | ***Computers & Industrial Engineering*** **126** (2018), **243–251** (9쪽) |
| DOI | `10.1016/j.cie.2018.09.039` |
| 키워드 | Supply chain network design · **Sample average approximation** · Stochastic programming · Facility location problem |

> ⚠️ **성격 주의**: Lee(2024)가 **모델링 + 관리적 시사점** 중심이라면, 이 논문은 **순수 알고리즘 논문**입니다. 9쪽 분량이고 민감도·관리적 시사점 절이 없습니다. 대신 **SAA의 통계적 바운드와 알고리즘 가속**을 정면으로 다룹니다.

---

# 1. 문제 상황

## 1-1. 배경 — 시설 중단(facility disruption)

**기본 문제**: **UFLP (Uncapacitated Facility Location Problem)**
> 잠재 입지 중 운영할 시설 집합을 고르고 고객 배정을 결정 → **초기 셋업비 + 일상 운송비** 최소화

**여기에 추가되는 것**: ⭐ **시설이 고장나면 전체 용량을 잃는다**
> 고장이 발생하면 고객을 **다른 작동 시설로 재배정**해야 하고, 이때 **더 높은 운송비**가 발생

## 1-2. 중단의 원인 (저자 분류)

| 유형 | 예시 |
|---|---|
| **공급망 내부** | 주요 설비 고장, 정전, 산업재해 |
| **자연재해** | 지진, 허리케인 |
| **기타** | 정치적 혼란, 테러, **사이버 공격** |

> "Though facility failures **rarely happen**, when it happens, it often **interrupts the normal operations and causes high costs**."

## 1-3. ⭐ 실무 사례 (동기 부여)

| 사건 | 영향 |
|---|---|
| ⭐ **2000년 Philips 앨버커키 마이크로칩 공장 화재** | 고객사 **Nokia·Ericsson**에 심각한 타격. **Ericsson 단기 매출 손실 최소 $4억**, *"the long-term impact was even greater"* |
| Hurricane Katrina (2005) | — |
| Tohoku-Kanto 지진·쓰나미 | — |
| Hurricane Maria (2017) | 지역 물리적 피해 + **공급망에 추가적 피해** |

## 1-4. SCND-FD 문제 정의

**2-에셜론 네트워크**

| 요소 | 기호 |
|---|---|
| 잠재 시설 입지 | `j ∈ J` |
| 고객 | `i ∈ I` |
| 고객 `i`의 **수요율** | `h_i` |
| 시설 `j` **셋업비** | `f_j` |
| `j → i` **단위 운송비** | `d_ij` |
| ⭐ 시설 `j`의 **입지 의존 고장률** | `q_j` |

### ⭐ Emergency 시설 `E` — 실행가능성 보장 장치

> "We use an **'emergency' facility E** to meet customer demand and model a **systemwide penalty** when **all other facilities fail**."

- **셋업비 `f_E = 0`**
- **단위 운송비 `d_iE` = 미충족 수요 1단위의 페널티 비용**
- 모델링 시 `E`를 집합 `J`에 포함

> 💡 **Lee(2024)의 emergency warehouse와 정확히 같은 장치**입니다. 두 논문 모두 *"항상 실행가능한 해가 존재함"* 을 보장하려고 무한(또는 매우 비싼) 백업 자원을 넣습니다.

**목적**: **기대 총비용 최소화**

---

# 2. 기여

저자가 명시한 **2가지**:

> "The major contribution of the paper is **twofold**.
> **First**, we model the supply chain network design problem with facility disruptions as a **two-stage stochastic programming model with a scenario-based formulation**.
> **Second**, we develop a general method, **sample average approximation**, which is able to solve this problem efficiently **with statistical bounds**, **regardless of the disruptions being correlated or not**."

## 세분화

| # | 기여 | 위치 |
|---|---|---|
| **C1** | SCND-FD의 **2SSP 정식화 + 시나리오 기반 정식화** (두 가지를 병렬 제시) | §3 |
| **C2** | ⭐ **Proposition 1** — 최근접 작동 시설 배정 성질. 해 평가를 효율화하는 열쇠 | §3.1 |
| **C3** | **SAA + 시나리오 분해** 결합, **통계적 상·하한** 제공 | §4.1–4.2 |
| **C4** | ⭐ **3개의 대안 서브루틴** (BWG, SNG, SWG)로 원본(BNG) 가속 | §4.2 |
| **C5** | 해 평가 알고리즘의 **시간복잡도 분석** + 독립 고장 시 표본 없이 평가하는 변형 | §4.2.1 |
| **C6** | ⭐ **상관 고장(correlated disruption)까지 동일 방법으로 처리** | §5.2 |

## 문헌 갭

저자들이 §2 말미에 지목:
> "Studies about reliable facility location problems under **correlated facility disruptions are still rare**."

기존 접근의 계보와 한계:
- **Snyder & Daskin (2005)**: p-median + UFLP, 동일 고장확률. ⚠️ **명시적 정식화는 시나리오 수가 극단적으로 많아 계산 불가** → 암묵적 비선형 정수계획 + Lagrangian 분해
- **Berman, Krass & Menezes (2007)**: 이질적 확률 허용
- **Cui, Ouyang & Shen (2010)**: 동일확률 가정 완화, 선형 등가모델 + Lagrangian 완화 + 연속근사
- **Shen, Zhan & Zhang (2011)**: 2단계 확률모델 + 비선형 정수계획 + 휴리스틱
- **O'Hanley et al. (2013)**: 더 compact한 대안적 선형화
- **Li & Ouyang (2010)**: **공간적 상관** 고장, 연속근사
- **Lu, Ran & Shen (2015)**: **분포강건최적화(DRO)**

→ 본 논문의 자리: **일반적이고(상관 무관) 통계적 보증이 있는 방법**

---

# 3. 방법론

## 3-1. 두 가지 정식화

### ⓐ 2단계 확률계획 모델 (§3.1)

| 단계 | 변수 |
|---|---|
| **1단계** | `X_j ∈ {0,1}` — 시설 `j`를 **열 것인가** |
| **2단계** | `Y_ij ∈ {0,1}` — 고객 `i`를 시설 `j`가 **서비스할 것인가** (불확실성 해소 후) |

`ξ` = 이진 확률벡터. `ξ_j = 1`이면 시설 `j` 고장, 아니면 0.

```
min  f(X) = Σ_{j∈J} f_j X_j  +  E_ξ[ Q(X, ξ) ]          (3.1)
s.t. X_j ∈ {0,1},  ∀j∈J                                  (3.2)

여기서 Q(X, ξ) =
     min  Σ_{i∈I} Σ_{j∈J} h_i d_ij Y_ij                  (3.3)
     s.t. Σ_{j∈J} Y_ij = 1,        ∀i∈I                  (3.4)
          Y_ij ≤ (1 − ξ_j) X_j,    ∀i∈I, j∈J             (3.5)
          Y_ij ∈ {0,1},            ∀i∈I, j∈J             (3.6)
```

| 식 | 의미 |
|---|---|
| (3.4) | 각 고객은 **정확히 하나의 시설**에 배정 |
| ⭐ (3.5) | **열려 있고(`X_j=1`) 고장나지 않은(`ξ_j=0`)** 시설에만 배정 가능 |

### ⭐ Proposition 1 — 알고리즘 전체의 열쇠

> "Given a location solution and a failure scenario, **each customer is always served by the nearest working facility**."

**증명은 자명**: 목적식 (3.3)이 거리 기반 가중 운송비이므로, 최근접 작동 시설이 아닌 곳에 배정하면 비용이 증가.

> 💡 **왜 중요한가**: 1단계 변수가 고정되면 **2단계를 최적화 문제로 풀 필요가 없습니다.** 정렬 한 번이면 끝. 이것이 §4.2의 시나리오 분해와 §4.2.1의 다항시간 평가를 가능하게 합니다.

### ⓑ 시나리오 기반 모델 (§3.2)

`S` = 모든 고장 패턴 조합의 집합, `a_js` = 시나리오 `s`에서 시설 `j`의 고장 상태, `q_s` = 시나리오 확률

```
min  Σ_{j} f_j X_j + Σ_{s∈S} q_s Σ_{i} Σ_{j} h_i d_ij Y_ijs     (3.7)
s.t. Σ_j Y_ijs = 1,             ∀i, s                            (3.8)
     Y_ijs ≤ (1 − a_js) X_j,    ∀i, j, s                         (3.9)
     Y_ijs, X_j ∈ {0,1}                                          (3.10)(3.11)
```

> ⚠️ SAA를 적용할 것이므로 **`q_s`를 명시적으로 계산할 필요가 없습니다.**

### ⭐⭐ 왜 이 모델을 직접 풀 수 없나

```
|S| = 2^|J|        ← 모든 고장 조합
```
- **시나리오 하나만 봐도 UFLP** = **NP-hard**
- → *"This **prohibits directly solving the model with a standard MIP solver, such as CPLEX, for even a small number of potential locations**."*

**이 한 줄이 논문 전체의 존재 이유입니다.**

## 3-2. SAA (§4.1)

### 원리
확률문제의 기대 목적값을 **표본 문제의 값으로 근사**. 표본 절차를 여러 번 반복해 충분한 정보를 얻음.

```
min  f̂(X) = Σ_j f_j X_j + (1/N) Σ_{n=1}^{N} Q(X, ξ^n)          (4.1)
```

> **Kleywegt et al. (2002)**: 온건한 정칙 조건 하에서 `v_N`, `X_N`이 **확률 1로** 원 문제의 대응값에 수렴하며,
> ⭐ **최적해 `X_N`의 수렴 속도는 지수적으로 빠름(exponentially fast)**

### ⭐ 4단계 절차

```
Step 1  표본 크기 N, N′ 와 SAA 복제 횟수 M 결정
        ⚠️ 트레이드오프: 근사 정확도 ↔ 계산 난이도

Step 2  for m = 1..M:
            N개 고장 시나리오 표본 생성 → SAA 문제 (4.1) 풀이
            → 최적해 X_m, 최적값 v_m
        (N은 SAA 문제가 비교적 효율적으로 풀리도록 선택)

Step 3  ⭐ 하한 추정량:  v̄_{N,M} = (1/M) Σ_m v_m
        + 분산 σ²_{v̄}= [1/((M−1)M)] Σ_m (v_m − v̄)²
        → v̄_{N,M}는 참 문제 최적값 v*의 통계적 하한

Step 4  ⭐ 실행가능해 X̂ 선택 (M개 복제 중 최저 목적값)
        독립 표본 크기 N′로 목적값 추정:
            f̂_{N′}(X̂) = Σ_j f_j X̂_j + (1/N′) Σ_n Q(X̂, ξ^n)
        → f(X̂) ≥ v* 이고 f̂_{N′}(X̂)는 f(X̂)의 불편추정량
        → 따라서 상한 추정량
```

> ⭐ **핵심 설계**: `N′`는 보통 `N`보다 **훨씬 큽니다**. 1단계 결정이 고정된 상태에서 **독립적인 2단계 하위문제만** 풀면 되므로 훨씬 쉽기 때문입니다.

## 3-3. ⭐ 시나리오 분해 알고리즘 (§4.2)

### 기반: **Ahmed (2013)**, "A scenario decomposition algorithm for 0-1 stochastic programs", *OR Letters*

> 시나리오 부분문제의 해를 **전체 문제의 후보해**로 탐색하고, 평가된 해를 **잘라낸다(cut off)**. 이렇게 하면 하한이 개선되며 결국 듀얼리티 갭이 닫힘.

**이 문제에 잘 맞는 이유**: ⭐ Proposition 1 덕에 1단계 변수가 고정되면 `Q(X, ξ^n)`을 **낮은 다항시간에 계산** 가능

### 알고리즘 (BNG — 원본)

```
Step 1  UB ← +∞,  LB ← −∞,  S ← ∅,  X* ← ∅
Step 2  [하한 문제]  for i = 1 to N:
            min_{X ∉ S}  f_i(X) = Σ_j f_j X_j + Q(X, ξ^i)
            → v_i, X^i
        LB ← (1/N) Σ_i v_i ,   S ← S ∪ {X^i}
Step 3  [상한 문제]  for X ∈ S:
            u ← Σ_j f_j X_j + (1/N) Σ_n Q(X, ξ^n)
            if UB > u:  UB ← u,  X* ← X
Step 4  if UB > LB:  goto Step 2
```

> ℹ️ Ahmed(2013)는 Step 2를 **Lagrangian 듀얼**로 분해하고 듀얼 벡터를 갱신하는 절차를 두었으나, **저자 본인이 sub-gradient 방식이 하한을 유의하게 개선하지 못했다고 밝혔으므로** 본 논문은 **Lagrangian 절차를 넣지 않았습니다.**

### Proposition 2 — 유한 종료 + 최적성

> "The scenario decomposition algorithm **terminates within a finite number of iterations and returns an optimal solution**."

**근거 3단**:
1. SAA 문제의 실행가능 영역은 **유한 개**의 실행가능해로 구성
2. 매 반복마다 모든 해가 평가되고 영역이 **축소**
3. ⭐ **Emergency 시설 가정이 실행가능해의 존재를 항상 보장** — 극단적으로 모든 작동 시설이 중단돼도 고객은 `E`가 서비스

### ⚠️ 발견된 문제 — 수렴이 매우 느리다

> "we find that this method has one **major issue: the convergence rate is very slow** for some problem instances."

**원인 2가지**
1. 각 하위문제가 **시나리오 하나만 집중**해 최적화하므로 **낮은 목적값**이 나옴 → 전체 시나리오로 보면 상한이 **훨씬 높음**
2. ⭐ **많은 입지해가 최적값에 근접** → 갭이 크고 **하한이 느리게 증가** → 갭을 닫는 데 막대한 반복 필요

### ⭐⭐ 해결 — 2가지 아이디어 × 2 = 4개 알고리즘

**아이디어 A**: 시나리오 각각이 아니라 **시나리오 그룹**으로 하한 문제를 푼다
**아이디어 B**: 주 반복 후 일괄 제거가 아니라 **하위문제를 풀 때마다 순차적으로** 제거한다

|  | **Batch cutting** (일괄 제거) | **Sequential cutting** (순차 제거) |
|---|---|---|
| **Grouping scenarios** (그룹화) | **BWG** | ⭐ **SWG** |
| **For each scenario** (개별) | **BNG** ← 원본 | **SNG** |

> **SWG 구현**: `G`를 `N`개 시나리오의 분할(partition)로 두고, 각 그룹 `T_i ∈ G`에 대해
> `min_{X∉S} f_i(X) = Σ_j f_j X_j + (1/|T_i|) Σ_{t∈T_i} Q(X, ξ^t)`
> 초기에 `G`는 `N`개의 **싱글톤 시나리오**를 담고, 반복마다 병합

## 3-4. ⭐ 해 평가의 시간복잡도 (§4.2.1)

```
Step 1  각 고객 i에 대해 열린 시설을 거리 오름차순 정렬
        {r(1), r(2), …, r(p)},  d_{i,r(1)} ≤ … ≤ d_{i,r(p)}
        → O(|I| |J| log|J|)

Step 2  for n = 1..N:  각 고객 i:  t = 1..p:
            if r(t) 작동 중:  cost += (1/N) h_i d_{i,r(t)};  break
        → 고장 여부 확인은 O(1)  ⟹  O(N |I| |J|)
```

**전체**: `O(|I||J| log|J| + N|I||J|)`
보통 `N >> log|J|` 이므로 ⭐ **`O(N|I||J|)`가 지배**

### ⭐ 독립 고장일 때의 변형 — 표본 없이 평가

고장이 독립이면 **시나리오를 순회할 필요가 없습니다**:
```
각 고객 i:  pr ← 1
   for t = 1..p:
       cost += pr · (1 − q_{r(t)}) · h_i · d_{i,r(t)}
       pr   ← pr · q_{r(t)}
```
→ ⭐ **`O(|I||J| log|J|)`** 로 단축 (N 항이 사라짐)

---

# 4. 실험 설정

## 4-1. 계산 환경

| 항목 | 사양 |
|---|---|
| 구현 언어 | **C++** |
| 하드웨어 | ⭐ **Dell PowerEdge R910 서버, 2.0GHz CPU (총 48코어), 128GB 메모리** |
| OS | Windows Server Enterprise (64-bit) |
| 솔버 | **CPLEX Academic Initiative Edition 12.6 (64bit)**, ⭐ **단일 스레드 모드**, 기타 기본값 |
| 병렬화 | ⭐ **OpenMP** (스레드 수 = M) |

> 💡 Cavagnini(i7 노트북, 8GB)와 대조적입니다. **서버급 환경**이고, 병렬화까지 명시적으로 다룹니다.

## 4-2. 인스턴스 생성 (§5.1, 독립 고장)

| 파라미터 | 생성 방식 |
|---|---|
| 시설·고객 **위치** | **[0,100] × [0,100] 균등분포** |
| 단위 운송비 `d_ij` | **유클리드 거리에 비례** |
| 시설 **셋업비** `f_j` | **U[2000, 5000]** |
| ⭐ **고장확률** `q_j` | **U[0, 0.1]** |
| 고객 **수요** `h_i` | **U[0, 20]** |
| **Emergency 시설** | `f_E = 0`, ⭐ **`d_iE = 200`** — *"so that **only when all open facilities fail** does the penalty occur"* |

**규모**: **20 시설 × 20 고객 ~ 60 시설 × 80 고객**
**반복**: 각 입력 크기마다 ⭐ **무작위 인스턴스 10개**
**표본 크기**: **N = 20, 50, 100**

---

# 5. 실험 결과

## 5-1. Table 1 — 4개 알고리즘 비교

**보고 지표**: **반복 수(Iter.)** · **평가된 해의 수(nSol)** · **CPU 시간** · 평균 목적값(Obj. Value)

### ⭐ 실험 설계의 좋은 점
> "It is clear that **all of the algorithms terminate with the same objective value** since the problems are solved optimally."

→ **목적값이 동일하므로 비교가 순수하게 "속도"** 에 대한 것입니다. 해 품질이 교란변수가 되지 않습니다.

### 결과

| 상황 | 최선 |
|---|---|
| **소규모** (특히 고객 수가 적을 때) | ⭐ **BWG** (일괄 제거 + 그룹화) |
| ⭐ **규모가 커지면** | ⭐ **SNG, SWG** (순차 제거) — 반복 수도 더 적음 |

**부수 관찰**
- **그룹화를 안 쓰면** 일반적으로 **더 많은 반복 + 더 많은 해 평가** 필요
- ⭐ **표본 크기 `N`이 커질수록 반복 수는 오히려 감소**

## 5-2. Table 2 — SAA 성능 (독립 이질적 고장)

| 설정 | 값 |
|---|---|
| **복제 횟수 `M`** | **20** |
| ⭐ **`N′`** | **100,000** — *"provides a reasonable balance between solution time and solution quality"* |
| 사용 알고리즘 | ⭐ **SWG** (Table 1에서 대규모 최고 성능이므로) |

**보고 항목**: 하한 추정량 + **분산** / 상한 추정량 + **분산** / **최적성 갭 + 분산** / 총 CPU 시간 / **병렬 시간**

### 결과 해석

| 발견 | 설명 |
|---|---|
| **최적성 갭이 작음** | 하한·상한 값 대비 → SAA가 **고품질 해**를 찾음 |
| **상한 추정량의 분산이 작음** | `N′`를 크게 잡았기 때문 |
| ⭐ **최적성 갭의 분산은 주로 하한 추정량의 분산이 결정** | 그리고 표본 크기 `N`이 커지면 감소 |

## 5-3. ⭐ Fig. 1 — 순차 vs 병렬 (흥미로운 발견)

**인스턴스**: 40 시설 × 60 고객, `N = 50`, `M = 20`

| 구현 | 시간 |
|---|---|
| **순차** | **1,917초** |
| **병렬** | ⭐ **310초** (약 **6.2배** 단축) |

⚠️ **그런데**:
> "The figure shows when the algorithm is paralleled, **the solution time for each replication increases**. This indicates that when the algorithm is paralleled, **resources other than the CPU become the bottleneck**."

→ **48코어인데 20스레드로 6.2배밖에 안 나옵니다.** 메모리 대역폭 등이 병목이라는 진단.

## 5-4. §5.2 — 상관 고장 (Correlated Disruptions)

### ⭐ 상관 모델링 방식의 선택 논리

| 방식 | 평가 |
|---|---|
| **상관행렬** | 가장 단순하나 ⚠️ *"it is **difficult to estimate the parameters** for the supply chain network"* |
| ⭐ **조건부 확률** | 고장 메커니즘이 잘 이해될 때 — 예: **공통 위험원 노출(shared hazard exposure)** 로 상관이 유발될 때. **위험원의 상태에 조건부**로 고장을 기술 (Li & Ouyang 2010) |

### 채택한 모델 — 위험원 전파

```
· 위험원(hazard source)이 좌표 원점에 존재
· 위험 사건이 확률 α 로 발생
· 사건 발생 시 시설 j 가 고장날 확률:   e^( − D_j / γ )
      D_j = 시설 j 에서 위험원까지의 거리
      γ   = 중단 전파 강도(propagation effect)를 특징짓는 파라미터
```

> 거리가 멀수록 고장확률이 **지수적으로 감소**하는 구조. 두 파라미터 `α`, `γ`를 변화시켜 목적값과 해에 미치는 영향을 검토.

### Table 3 — 3가지 위험 수준 (`N = 50`)

| 시나리오 | `α` | `γ` |
|---|---|---|
| **저위험** | 0.1 | 100 |
| **중위험** | 0.2 | 200 |
| **고위험** | 0.2 | 200 |

> ⚠️ 원문 텍스트에서 **중위험과 고위험의 파라미터가 동일하게 표기**돼 있습니다. PDF 추출 오류이거나 원문 오탈자일 수 있으니 인용 시 원본 Table 3을 확인하세요.

### ⭐ 결과

1. **저위험 → 고위험으로 갈수록 열린 시설 수가 증가**
   → 위험이 클수록 **중복성(redundancy)을 더 확보**
2. ⭐ **하한·상한의 분산이 현저히 증가**
3. **도출된 실무 지침**:
   > "when handling a **high risk scenario, a larger sample size is required** to achieve a more accurate estimation."

---

# 6. 결론

1. SCND-FD를 **2SSP + 시나리오 기반 정식화**로 모델링 — 시설 셋업비 + 기대 운송비 최소화
2. 모든 고장 상태 표현에 **지수적 시나리오** 필요 → 표준 MIP 솔버로 불가
3. **SAA**가 ⭐ **통계적 바운드를 제공**하며 **상관 유무와 무관하게** 문제를 해결
4. 3개의 대안 서브루틴이 원본(BNG)을 **유의하게 능가**, ⭐ **비교적 대규모에서는 SWG가 최고**
5. **중간 규모 문제에서 SAA의 효과성** 입증 — 수용 가능한 시간 내 통계적 바운드가 있는 고품질 해

### 실무 함의
> "by considering disruptions at the supply chain **design phase**, a decision maker has the opportunity to **assess potential risks** caused by uncertainties."
> ⭐ "To apply the proposed sample average approximation method, it is advisable to obtain **reliable failure scenarios via historical data or predictable models**."

---

# 7. Lee (2024)와의 비교 — 같은 골격, 다른 선택

| 축 | **Lee, Ko & Moon (2024)** IJPR | **Li & Zhang (2018)** C&IE |
|---|---|---|
| **문제** | e-commerce SCND + **ODW commitment** | **UFLP + 시설 중단** |
| **모델** | **2SSP** | **2SSP** (+ 시나리오 기반 정식화 병기) |
| **불확실성** | **수요 + 공급(yield)** — 연속 → 정규분포 | ⭐ **시설 고장** — **이진(binary) 확률벡터** |
| **1단계** | 공급자 선택 + **약정** | **입지** |
| **2단계** | 운송·재고·배송·lost sales | **배분(assignment)** |
| **시나리오 수** | 유한 (표본) | ⭐ **`2^|J|` — 지수적** |
| **표본법** | **SAA** ✅ | **SAA** ✅ |
| **가속 방법** | ⭐ **EVP 해로 초기 Benders 컷 생성 (ABD)** | ⭐ **그룹화 × 순차제거 2×2 (BWG/SNG/SWG)** |
| **분해 기법** | **Benders** | ⭐ **시나리오 분해** (Ahmed 2013) |
| **실행가능성 보장** | ⭐ **Emergency warehouse** | ⭐ **Emergency facility `E`** |
| **통계적 바운드** | SAA 갭 보고 | ⭐ **상·하한 + 각각의 분산까지 정면 보고** |
| **VSS/EEV** | ✅ 보고 | ❌ 없음 |
| **민감도 분석** | ✅ 4종 (`K_max`, `θ`, `π_i`, 리드타임) | ⚠️ 위험 수준 3종만 |
| **관리적 시사점 절** | ✅ | ❌ |
| **병렬화** | ❌ | ⭐ **OpenMP, 순차 대비 6.2배** |
| **분량** | 27쪽 | **9쪽** |

## ⭐ 가장 주목할 공통점 — Emergency 백업 장치

**두 논문 모두 "매우 비싼 무한 백업 자원"을 넣어 실행가능성을 보장합니다.**
- Lee: **emergency warehouse** (단위 재고비·운송비가 훨씬 높음)
- Li & Zhang: **emergency facility `E`** (`f_E = 0`, `d_iE = 200` = 페널티)

⭐ Li & Zhang은 이걸 **Proposition 2의 증명 요소로까지 사용**합니다 — *"our assumption of an emergency facility **ensures that a feasible solution always exists**"*.

> 💡 본인 모델에서도 이 장치를 쓰신다면, **단순한 모델링 편의가 아니라 "실행가능성 보장 + 알고리즘 종료 보장"의 이중 역할**로 정당화할 수 있습니다. 두 논문을 함께 인용하세요.

## ⭐ 가속 기법의 대조

| | Lee (ABD) | Li & Zhang (SWG) |
|---|---|---|
| **진단** | 빈 컷 집합 → 형편없는 초기해 → **무용한 컷** | 시나리오 하나만 보는 하위문제 → **하한이 느리게 상승** |
| **처방** | **EVP 해**로 더 좋은 초기 컷 생성 | **시나리오 그룹화** + **순차 제거** |
| **공통 발상** | ⭐ **"좋은 정보를 먼저 주입해 초기 수렴을 앞당긴다"** | 동일 |

Cavagnini(2022)의 **RHA 해를 GUROBI warm start로 쓰기**(85.67% → 2.99%)도 **같은 발상**입니다. 세 논문을 묶으면 *"분해 알고리즘의 초기화 품질이 전체 성능을 좌우한다"* 는 논지를 만들 수 있습니다.

---

# 8. 우리 연구에 쓸 수 있는 것

## 8-1. 인용 포인트

| 주장 | 근거 |
|---|---|
| ⭐ **"명시적 시나리오 열거는 불가능하다"** | `|S| = 2^|J|` + *"prohibits directly solving [...] **even for a small number of potential locations**"* |
| **"SAA는 통계적 바운드를 준다"** | §4.1 Step 3·4 — 하한·상한 추정량과 분산 |
| **"SAA 최적해의 수렴은 지수적으로 빠르다"** | Kleywegt et al. (2002) 인용 |
| ⭐ **"위험이 클수록 더 큰 표본이 필요하다"** | §5.2 — 고위험에서 하한·상한 분산 급증 |
| ⭐ **"위험이 클수록 시설을 더 많이 연다"** | Table 3 — redundancy 확보 |
| **"분해 알고리즘은 초기화 품질이 관건"** | §4.2 수렴 지연 진단 + 4개 대안 |
| **"병렬화 이득은 코어 수에 비례하지 않는다"** | Fig. 1 — 48코어/20스레드에 6.2배, CPU 외 자원이 병목 |
| **"상관 중단은 공통 위험원 노출로 모델링하라"** | §5.2 — 상관행렬은 파라미터 추정이 어려움 |

## 8-2. ⭐ 차용할 만한 실험 설계

1. **목적값을 통제해 속도만 비교**
   모든 알고리즘이 **최적해로 종료**하므로 목적값이 같습니다 → 비교가 순수하게 계산 효율에 대한 것. **해 품질이 교란되지 않는 깔끔한 알고리즘 비교 설계**

2. ⭐ **2×2 요인 설계로 알고리즘 변형을 조직**
   "그룹화 여부 × 제거 방식"으로 4개를 만들고, **어느 축이 언제 효과적인지**를 규명(소규모=그룹화, 대규모=순차제거). 임시방편적 휴리스틱 나열보다 훨씬 설득력 있습니다.

3. **상·하한과 각각의 분산을 모두 보고**
   최적성 갭만이 아니라 **갭의 분산이 무엇에 의해 결정되는지**까지 분석(→ 하한 추정량의 분산)

4. ⭐ **해 평가 알고리즘의 시간복잡도를 명시**
   `O(N|I||J|)` 와 독립 고장 시 `O(|I||J| log|J|)` — **"왜 빠른가"를 점근적으로 증명**

5. **병렬화 이득을 실측하고 병목을 진단**
   Fig. 1처럼 순차 vs 병렬을 같은 인스턴스에서 비교하고, 기대만큼 안 나오는 이유를 설명

6. **`N`과 `N′`를 분리 설계**
   `N`(SAA 문제용, 작게) vs `N′`(상한 추정용, 100,000) — **하한과 상한에 서로 다른 표본 크기**를 쓰는 것이 SAA의 표준 실무

## 8-3. 이 논문의 한계 (= 우리의 여지)

| 한계 | 개선 방향 |
|---|---|
| **UFLP 기반 — 용량 제약 없음** | 용량 제약(capacitated)으로 확장. Lee는 이미 용량 있음 |
| **다기간 아님 — 단일 시점 설계** | ⭐ **다기간 + 롤링**으로 확장. 이 논문엔 시간 축이 없음 |
| **관리적 시사점 절 부재** | 알고리즘 논문이라 의도적. 우리는 둘 다 필요 |
| **VSS/EEV 미보고** | 확률해의 가치를 정량화하지 않음 (Lee는 함) |
| **민감도 분석이 위험 수준 3종뿐** | Lee 수준의 파라미터 민감도 필요 |
| **중·고위험 파라미터 표기 혼선** | Table 3 원본 확인 필요 |
| **중단만 불확실 — 수요는 결정론** | 수요·공급·중단을 **동시에** 다루면 신규성 |

> 💡 **가장 큰 여지**: 이 논문에는 **시간 축이 없습니다.** 단일 시점 설계 문제입니다. Lee의 다기간 commitment + 이 논문의 중단 리스크 + 롤링 호라이즌을 결합하면 세 논문 모두와 차별화됩니다.

---

# 9. 다른 논문과의 관계

| 논문 | 관계 |
|---|---|
| **Santoso, Ahmed, Goetschalckx & Shapiro (2005) EJOR** | ⭐ 본 논문이 §4.1에서 **직접 인용** — *"applied the SAA scheme with an **accelerated Benders decomposition**"*. **Lee와 Li&Zhang의 공통 조상** |
| **Ahmed (2013) OR Letters** | ⭐ **시나리오 분해 알고리즘의 원전**. 본 논문은 여기서 Lagrangian 절차만 빼고 채택 |
| **Kleywegt, Shapiro & Homem-de-Mello (2002)** | SAA의 수렴 이론. **Lee도 동일 인용** |
| **Lee, Ko & Moon (2024) IJPR** | 위 §7 전체 비교 참조 |
| **Cavagnini (2022) EJOR** | **초기해 품질로 분해 알고리즘을 가속**한다는 발상이 공통 (warm start ↔ 초기 컷 ↔ 그룹화) |
| **Schütz et al. (2009)** | 본 논문이 인용 — SAA + **듀얼 분해**, 단·장기 불확실성 통합 SCND |
| **Snyder & Daskin (2005, 2007)** | 신뢰성 시설입지의 고전. 시나리오 기반 정식화의 출처 |

---

*문서 생성: Claude Code · Computers & Industrial Engineering 126 (2018) 243–251 전문 직접 확인*
