# 페이퍼워크 — Gioia, Fadda & Brandimarte (2023/2024)

**"Rolling horizon policies for multi-stage stochastic assemble-to-order problems"**

| 항목 | 내용 |
|---|---|
| 저자 | Daniele Giovanni Gioia, Edoardo Fadda, Paolo Brandimarte — **Politecnico di Torino, DISMA** (Dept. of Mathematical Sciences "G.L. Lagrange") |
| 저널 | *International Journal of Production Research* **62**(14), 5108–5126 |
| 온라인 게재 | **2023-11-21** (Taylor & Francis) — 호수는 2024년 |
| DOI | `10.1080/00207543.2023.2283570` |
| 프리프린트 | **arXiv:2210.00491v3** (2023-11-22, accepted manuscript) |
| ⭐ 코드 | **오픈소스 공개** — https://github.com/DanieleGioia/ATO |
| 키워드 | stochastic programming · **end-of-horizon effect** · scenario tree generation · assemble-to-order production · data-driven optimization |

> ℹ️ **본 문서는 arXiv v3 (accepted manuscript) 기준**입니다. 게재본과 내용은 동일하나 **조판·페이지 번호가 다릅니다.** 인용 시 published version을 참조하세요 (저자들도 명시: *"Any citation must refer to its published version."*).

---

# 1. 문제 상황 (Problem Setting)

## 1-1. ATO(Assemble-To-Order)란 무엇이며 왜 필요한가

수요 불확실성에 대한 완충 수단 중 하나가 **조립 지연(delaying assembly)** — 제품 차별화를 뒤로 미뤄 **리스크 풀링** 효과를 얻는 전략. (성공 사례로 **HP DeskJet** 인용)

**ATO의 핵심 논리**
```
부품 제조·조달 리드타임이 길다  →  순수 Make-To-Order 불가
                                    ↓
             부품은 수요 불확실성 하에서 미리 생산해야 함
                                    ↓
             그러나 최종 조립이 빠르다면 완제품 재고는 불필요
                                    ↓
      ⭐ 완제품 수요를 관측한 "후에" 최종 조립 수행
```

## 1-2. ⭐ MRP/ERP의 2단계 구조와의 대응

이 논문의 실무적 앵커는 **MRP/ERP 시스템의 2단계 계획**입니다.

| 레벨 | 대상 | 구동 방식 | 본 논문의 대응 |
|---|---|---|---|
| **MPS** (Master Production Schedule) | **기본 모듈 / 부품** | **종속 수요 예측**에 따라 make-to-stock | **1단계 결정 = 부품 생산 `x`** |
| **FAS** (Final Assembly Schedule) | **완제품** | **실제 독립 수요**에 따라 구동 | **2단계 결정 = 조립 `y`** |

> "traditional MRP/ERP systems may **struggle with the complexity of finite capacity planning**, which compounds with demand uncertainty. The typical approach is to introduce **safety stocks**, and to plan according to a **rolling horizon strategy**."

→ **논문의 목표**: 이 실무 관행(안전재고 + 롤링 호라이즌)을 **최적화 모델 기반 처방적 분석(prescriptive analytics)**으로 개선

## 1-3. 문제 설정의 구체적 범위

| 항목 | 설정 |
|---|---|
| 시간 | **이산시간 (discrete-time)**, 기간 = **1개월** |
| 품목 | **다품목 (multi-item)** |
| 용량 | ⭐ **유한 용량 (finite-capacity)** — 부품 생산에 기계 용량 제약 |
| BoM | **평면(flat) 2레벨** — 부품(모듈) / 완제품 |
| 조립 | **병목 아님** — 조립 용량·비용 **고려하지 않음** |
| 수요 | **불확실** |
| **미충족 수요** | ⭐ **backordering 불가 → 전량 lost sale** |
| 목적 | **기대 이익 최대화** |
| 로트사이징 | **미고려** (셋업 비용·시간 무시) |
| 부품 조달 | **미고려** — 공급자로부터의 replenishment가 아니라 **자체 유한용량 생산** |

## 1-4. ⭐ 노드별 사건 순서 (Figure 1) — 모델 이해의 핵심

각 이산 시점(노드)에서 벌어지는 일의 **순서**:

```
① 완제품 신규 수요 관측
        ↓
② 재고의 부품으로 수요 충족 (조립/판매)
        ↓
③ 부품 재고유지비 + (필요 시) lost sale 비용 지불
        ↓
④ 신규 부품 생산
```

> "Understanding these dynamics will play a **major role in the rolling horizon simulations**, and this is why we also consider **end-item demand at the root node**."

⚠️ 이 순서 때문에 제약 (3)이 **`I_i[p(n)] + x_i[p(n)]`** — 즉 **부모 노드에서 이미 재고였거나 부모 노드에서 생산된 부품만** 현재 노드의 조립에 쓸 수 있습니다.

## 1-5. 저자들이 일부러 만든 "어려운" 수요 환경

> "We create demand settings that are **quite difficult to handle**, in order to test the efficiency in using the available information by different models."

3가지 난관을 의도적으로 결합:

| 특성 | 왜 어려운가 |
|---|---|
| **계절성 (seasonality)** | 생산용량으로 감당 못 하는 수요 피크 → **재고 버퍼의 사전 계획 필요** |
| **이봉성 (bimodality)** | 경험적 추정이 매우 어려움. 신제품(패션), 대형 고객의 불규칙 대량주문, 신규 경쟁자 진입 등에서 발생 |
| **패밀리 내 상관 (correlation)** | 같은 패밀리 내 변형 제품은 **음의 상관** — 단순 쌍별 상관으로 처리 불가 |
| **데이터 희소성** | ⭐ 계절성 때문에 **계절 기간당 관측치가 3·5·10개뿐** |

---

# 2. 기여 (Contributions)

## 저자가 명시한 2대 기여 (§1)

> "The contribution of this work is **twofold**."

**① ⭐ 잔여 부품 재고의 가치를 근사하는 계산 방법론 제안**
- 목적함수에 **end-of-horizon 항**을 추가
- 짧은 호라이즌 모델의 **근시안(myopia)을 상쇄**

**② 어려운 수요 특성 하에서 여러 모델 정식화의 성능 비교**
- 이봉성 · 계절성 · 상관 + **가용 용량 수준의 영향**
- **제한된 과거 데이터**만 있다고 가정

## 세분화한 기여

| # | 기여 | 위치 |
|---|---|---|
| **C1** | ⭐ **FOSVA** — 구간선형 오목 터미널 가치함수 근사 (First Order Stock Value Approximation) | §3 |
| **C2** | 5개 모델군의 **체계적 비교**: TS / FOSVA / MP_\<n\> / MS3, MS3_\<n\> / SSt_β | §3, §5 |
| **C3** | ⭐ **out-of-sample 롤링 호라이즌 시뮬레이션**으로 평가 (in-sample 계산효율이 아님) | §5 |
| **C4** | ATO 맞춤 **현실적 gozinto 행렬 생성 절차** (패밀리 + outcast 구조) | §4.1 |
| **C5** | 계절성 · 이봉성 · **패밀리 내 상관**을 갖는 수요 생성 메커니즘 | §4.2 |
| **C6** | ⭐ **오픈소스 시뮬레이션·최적화 프레임워크 공개** | GitHub |
| **C7** | ADP 관점에서의 위치 규정 — **lookahead 트리 + 가치함수 근사의 결합** | §2.3 |

## 문헌 갭 (저자 명시)

1. ATO 문헌 상당수가 **연속/주기적 재고관리** 지향 — 유한용량 **생산계획**이 아님
2. 해석적 모델은 보통 **완제품 1개 또는 극소수**만 다룸
3. ⭐ **end-of-horizon 효과 완화 도구와 ATO 확률계획의 통합이 없음**
   - 금융 분야(자산-부채관리)에는 터미널 가치 연구가 풍부하나, **제조 분야에는 거의 없음**
   - 금융은 본질적으로 **단일 재화(부(富))**를 다루지만, ATO는 **BoM을 통한 부품 간 관계** 때문에 훨씬 복잡
4. 시나리오 트리 구조 비교 연구는 **금융 포트폴리오**에서만 수행됨
   - 금융은 **차익거래 회피** 제약이 트리 형태를 구속하지만 제조는 그런 제약 없음
   - 금융은 **데이터가 풍부**하나 제조는 희소 → **data-driven 접근 필요**

## ⭐ ADP 관점의 자기 위치 규정 (§2.3)

Powell (2022)의 분류 체계에 본 연구를 위치시킵니다:

| ADP 전략 | 이 문제에 대한 적용 가능성 (저자 판단) |
|---|---|
| **순수 가치함수 근사(VFA)** | ❌ BoM을 통한 부품 간 상호작용 → **가치함수가 매우 복잡** |
| **순수 정책함수 근사(PFA)** | ❌ 용량 제약이 부품들을 **결합**시킴 → 상태→결정 매핑이 어려움 |
| **순수 lookahead (다단계 SP)** | ❌ **시나리오 트리의 지수적 증가** |
| **확률→결정론 근사 + 버퍼** | △ 안전재고 방식 → **경쟁 벤치마크로 채택** |
| ⭐ **lookahead + VFA 결합** | ✅ **본 논문의 핵심** |

> "the **essential contribution** of our paper is to **integrate a lookahead approach, based on scenario trees, with a value function approximation**. As discussed by Bertsekas (2022), this is the key idea in many successful approaches to dynamic decisions."

---

# 3. 방법론

## 3-1. 표기

### 집합
| 기호 | 의미 | 크기 |
|---|---|---|
| `I = {1,…,I}` | **부품(모듈)** 집합 | **60** |
| `J = {1,…,J}` | **완제품** 집합 | **35** |
| `M = {1,…,M}` | **생산 자원(기계)** 집합 | **5** |

> ⚠️ 원문 Table 1의 괄호 기호가 본문과 뒤바뀌어 있습니다(표는 "No. end items (I) 35 / No. components (J) 60"). 본문 정의(`I`=부품, `J`=완제품)와 gozinto 구성(패밀리별 품목수 [12,7,5,3,3]=30 + outcast 5 = **완제품 35**)으로 검증하면 **부품 60 / 완제품 35**가 맞습니다.

### 파라미터
| 기호 | 의미 |
|---|---|
| `C_i` | 부품 `i`의 **원가** |
| `P_j` | 완제품 `j`의 **판매가** |
| `K_j` | 완제품 `j`의 **lost sale 페널티** |
| `H_i` | 부품 `i`의 **재고유지비** |
| `L_m` | 기계 `m`의 **생산 가용시간** |
| `T_im` | 기계 `m`에서 부품 `i` 1개 생산 소요시간 |
| **`G_ij`** | ⭐ 완제품 `j` 조립에 필요한 부품 `i`의 수 — **gozinto factor** |
| `Ī⁰_i` | 부품 `i`의 초기 재고 |

### 시나리오 트리 (Brandimarte 2006 표기)
| 기호 | 의미 |
|---|---|
| `N` / `N⁺` | 노드 집합 / `N \ {0}` |
| 노드 `0` | **루트** — here-and-now 결정 지점 |
| `p(n)` | 노드 `n`의 **부모** |
| `π[n]` | 노드 `n`의 **무조건부 확률** (`π[0] = 1`) |
| `d_j[n]` | 노드 `n`에서 완제품 `j`의 수요 |
| **branching factor** | 주어진 레벨에서 각 노드의 **자식 수** |

> 예: `[2, 2, 2]` = 4기간(루트 포함) 이진 트리 = **8 시나리오**

### 결정변수 (전부 비음 정수)
| 기호 | 의미 |
|---|---|
| `x_i[n]` | 노드 `n`에서 생산하는 **부품 `i`의 양** |
| `I_i[n]` | 노드 `n`에서 **가용한 부품 `i` 재고** |
| `y_j[n]` | 노드 `n`에서 **조립하는 완제품 `j`의 양** |
| `l_j[n]` | 노드 `n`에서 완제품 `j`의 **lost sale** |

## 3-2. 기본 다단계 모델 (식 1–7)

```
max   Σ_{n∈N} π[n] [ Σ_{j∈J} ( P_j y_j[n] − K_j l_j[n] )
                    − Σ_{i∈I} ( C_i x_i[n] + H_i I_i[n] ) ]              (1)

s.t.  Σ_{i∈I} T_im x_i[n]  ≤  L_m                    ∀m∈M, n∈N        (2)

      I_i[n] = I_i[p(n)] + x_i[p(n)] − Σ_{j∈J} G_ij y_j[n]
                                                     ∀i∈I, n∈N⁺       (3)

      I_i[0] = Ī⁰_i − Σ_{j∈J} G_ij y_j[0]            ∀i∈I             (4)

      y_j[n] + l_j[n] = d_j[n]                       ∀j∈J, n∈N        (5)

      x_i[n], I_i[n] ∈ ℤ⁺ ;  y_j[n], l_j[n] ∈ ℤ⁺                      (6)(7)
```

| 식 | 역할 |
|---|---|
| **(1)** | **기대 순이익** = 기대 매출 − lost sale 페널티 − 재고유지비 − 부품 생산원가 |
| **(2)** | **기계 용량 제약** |
| **(3)** | ⭐ **재고 균형 + ATO 동역학** — 부모 노드 재고 + 부모 노드 생산량 − 현재 노드 조립 사용량 |
| **(4)** | 루트 노드 초기 조건 |
| **(5)** | **조립 + lost sale = 수요** |
| **(6)(7)** | 정수 비음 |

> ⭐ 제약 (3) + 재고 비음성이 **부품이 부족한 완제품의 조립을 원천 차단**합니다.

**문제**: 모델 (1)–(7)의 크기가 **기간 수와 분기계수에 따라 지수적으로 증가**.

## 3-3. ⭐ 5개 모델군 — 이 논문의 핵심 설계

### ① TS — 단순 2단계 (식 8–15)
- 트리: Figure 2(a). 1개의 미래 단계만
- 상용 솔버로 **상당히 많은 시나리오까지 해결 가능**
- ⚠️ **근시안(myopic)의 원인**:
  > "for each component produced but not assembled, the inventory cost `H_i` is paid and **no gain is received**. Thus, there are **no explicit incentives to leave components in inventory** after assembly"
  - 남는 재고는 오직 **2단계(직후 미래) 수요 헤지의 부산물**일 뿐

### ② ⭐ FOSVA — First Order Stock Value Approximation

TS의 근시안을 **터미널 재고에 가치를 부여**해 해소.

2단계 기여항을 다음으로 대체 (식 16):
```
Σ_s π^s [ Σ_j ( P_j y_j^s − K_j l_j^s ) − Σ_i H_i I_i^s + V(I_1^s, …, I_I^s) ]
```

**근사의 3가지 아이디어**

| # | 아이디어 | 대가 |
|---|---|---|
| 1 | ⭐ **부품별 분해**: `V(I) ≈ Σ_i V_i(I_i)` | **부품 간 상호작용 무시** (Powell 2011 — 흔하고 실무적으로 잘 작동) |
| 2 | 각 항을 **구간선형(piecewise linear)** 근사 | **모델의 선형성 보존** |
| 3 | ⭐ **`V(I) ≈ TS(I)`** — 초기 재고 상태를 바꿔가며 TS를 풀어 정보 수집 | 오프라인 학습 단계 필요 |

**오목성(concavity)의 근거**
- `TS(I)`는 **최대화 LP의 최적값을 제약 (13)의 우변의 함수로 본 것** → **오목**
- 각 좌표 방향으로도 오목성 보존 (Boyd & Vandenberghe 2014)
- ⭐ 재고가 과도하면 **기울기가 음수**가 될 수 있음

**해석: markdown value**
```
Φ_i(I_i^s) = V_i(I_i^s) − H_i I_i^s
```
- `Φ_i > 0`이면 완제품에 안 팔려도 **잔여 부품 `i`가 가치에 기여**
- 언제 그런가? ⭐ **미래 단계가 생산용량을 초과하는 부품량을 요구할 때**

**기울기 추정 (식 18)**
- `e_i` = ℝ^I 정규직교기저의 `i`번째 원소, `δ` = 근사 스텝 섭동
- 부품 `i`의 재고를 섭동시켜 TS 최적값의 **전진·후진 유한차분**을 확인

**Algorithm 1 — 평활화·동적 적응**
- 브레이크포인트 벡터 `u_i`에 `Î_i`를 **오름차순 삽입**, 위치 = `pos_i`
- 기울기 벡터 `v_i`의 `pos_i`에 `pos_i − 1` 위치 값을 삽입 → 길이 일치 + **비증가 수열 유지**
- 평활화: `κ⁺ = (1−α)v_i^{pos_i} + Δ_i⁺`, `κ⁻ = (1−α)v_i^{pos_i} + Δ_i⁻`
- `pos_i` 왼쪽에서 `κ⁺`보다 작은 값은 전부 `κ⁺`로, 오른쪽에서 `κ⁻`보다 큰 값은 전부 `κ⁻`로 → **비증가 수열 완성**
- **`K`회 정제**

> ⭐ **`K` 선택의 트레이드오프**: 점을 많이 넣을수록 근사가 좋아지지만, **브레이크포인트마다 최적화 문제에 새 변수가 생김**

**최종 모델 (식 19–22)**: 보조변수 `m_ik^s`가 구간선형 근사를 표현

**⚠️ 오프라인 비용**: `3 × K × I` 개의 TS 문제를 풀어야 함

### ③ MP_\<n\> — 2단계 + 다기간 (Figure 2b)
- 첫 분기 **이후에는 수요가 알려졌다고 가정**한 노드들을 덧붙임
- 독립성이 성립하면 그 값을 **기대수요(또는 추정치)**로 생성
- ⭐ **"certainty equivalent steady state"** 를 도입해 end-of-horizon 효과를 완화하되 **시나리오 수는 증가시키지 않음**
- 분기계수 예: `[10, 1, 1, …, 1]`
- `<n>` = **말단 선형 그래프의 길이**
- ⚠️ 제조업에서는 유효하나(Brandimarte 2006), **금융에서는 차익거래 기회를 만들어 사용 불가** (Birge et al. 2022)

### ④ MS3 / MS3_\<n\> (Figure 2c, 2d)
- **MS3**: **3단계** — 가까운 미래의 불확실성을 더 상세히 기술
  - 분기계수: 데이터 3·5·10년에 대해 각각 **`[3,3]`, `[5,5]`, `[10,10]`**
- **MS3_\<n\>**: MS3 뒤에 **분기계수 1인 노드 꼬리**를 덧붙임 (MP_\<n\> 방식의 확장)
  - 분기계수 예: `[10, 10, 1, …, 1]`

### ⑤ SSt_β — 결정론 모델 + 안전재고 (Figure 4)
- 분기계수 `[1, 1, …, 1]` → **완전 결정론**, 각 노드에 기대수요 추정치
- 계산 절감 덕에 **1년 전체 같은 긴 호라이즌** 고려 가능
- 안전재고 제약 (식 23):
  ```
  I_i[n] ≥ Σ_j G_ij · d_j^β        ∀i∈I, n∈N
  ```
  `d_j^β` = 완제품 `j` 수요분포의 **경험적 β-분위수**. β↑ → 안전재고↑

### ⑥ TS_noS — 보조 벤치마크
- 2단계 모델이되 **계절성을 무시하고 전체 관측치를 시나리오로 사용**
- 10년 데이터면 2단계에 **120 시나리오**
- ⭐ **역할**: *"to provide a direct comparison with FOSVA by proving that the **value function estimation is not equivalent to considering all the observations together**"*

## 3-4. ⭐ 모델군 요약표 — 이것이 곧 "W 실험" 설계

| 모델 | 트리 구조 | **Lookahead 기간 수(W)** | 특징 |
|---|---|---|---|
| **TS** | `[S]` | **1** | 기본 2단계, 근시안 |
| **TS_noS** | `[S]` (계절성 무시) | **1** | 보조 벤치마크 |
| ⭐ **FOSVA** | `[S]` + 터미널 가치 | **1** | **W를 늘리는 대신 가치함수 추가** |
| **MP_2** | `[S, 1]` | **2** | 확실성 등가 꼬리 |
| **MS3** | `[S, S]` | **2** | 완전 3단계 |
| **MP_3** | `[S, 1, 1]` | **3** | |
| **MS3_3** | `[S, S, 1]` | **3** | |
| **MP_4** | `[S, 1, 1, 1]` | **4** | |
| **MS3_4** | `[S, S, 1, 1]` | **4** | |
| **SSt_β** | `[1, 1, …]` | 최대 12 | 결정론 + 안전재고 |

> `S` = 가용 관측치 수 (3, 5, 10)

---

# 4. 실험 설정

## 4-1. 인스턴스 파라미터 (Table 1)

| 파라미터 | 값 |
|---|---|
| **부품 수** | **60** |
| **완제품 수** | **35** |
| **기계 수** | **5** |
| ⭐ **Tightness factor `τ`** | **{1.0, 1.1, 1.2, 1.3}** |
| 패밀리 수 `R` | **5** |
| 패밀리별 품목 수 | **[12, 7, 5, 3, 3]** (합 30) |
| 패밀리별 부품 수 | **[11, 17, 12, 6, 9]** (합 55) |
| **Outcast 품목 수** | **5** |
| 패밀리별 공통 부품 수 | **2** |
| 저마진 비율 / 범위 | **40%** / `[0.05, 0.2]` |
| 중마진 비율 / 범위 | **30%** / `[0.2, 0.4]` |
| 고마진 비율 / 범위 | **30%** / `[0.4, 0.6]` |
| **Lost sale 페널티** | `K_j = **0.2 · P_j**` |
| **재고유지비** | `H_i = **0.1 · C_i**` |
| 부품 원가 | `C_i ~ U[1, 50]` |

## 4-2. ⭐ Tightness factor `τ` — 용량 여유도

```
L_m = τ · Σ_{i∈I} T_im · Σ_{j∈J} G_ij · d̄_j              (24)
```
- `d̄_j` = 완제품 `j`의 기대수요 — **수요 생성 메커니즘이 복잡해 5,000 시나리오 샘플링으로 추정**
- **`τ = 1.0`**: 생산용량 = 전체 호라이즌 평균수요
- **`τ = 1.3`**: 평균수요 대비 **30% 여유**
- `τ`↑ → **계절 피크 대응이 쉬워짐** (평균수요 초과분을 미리 생산 가능)

> ⭐ **설계의 묘수**: 최대 계절 계수가 **1.3**이므로, `τ = 1.3`이면 **사전 재고 없이도 평균적으로 최대 계절 피크를 감당**할 수 있습니다. 즉 `τ`는 "재고 선행 생산이 얼마나 필수적인가"를 조절하는 손잡이입니다.

## 4-3. gozinto 행렬 생성 (Figure 5)

> "In the literature, several standard structures for the gozinto matrix have been proposed... Nevertheless, these structures are usually considered for their **theoretical properties rather than their realism**."

**생성 절차**
1. 품목 집합을 **패밀리로 분할** — 각 패밀리는 정해진 수의 완제품을 가짐
2. 패밀리 내 완제품은 **공통 부품 + 고유 부품**을 가짐
3. **퇴화 패밀리(단일 완제품) = outcast item** 도입
4. 표준 패밀리: 부품별 필요 개수 ~ **이산균등 [1, 9]**
5. **Outcast 품목**: 다른 품목과 패턴을 공유하지 않음 — 각 부품이 **확률 0.2의 이항분포**로 포함

**결과 (Figure 5)**: 대각 블록 5개 = 표준 패밀리, 하단 행 = outcast 품목, 각 블록 앞쪽 열 = 공통 부품

## 4-4. 수요 분포

### ⒜ 계절성 (Table 2) — 월별 승법 계수

| 월 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 계수 | 1.0 | 1.1 | 0.9 | 0.8 | 1.0 | 0.8 | **1.2** | **1.3** | **1.2** | 1.0 | 0.8 | 0.9 |

- ⭐ **기댓값과 표준편차에 동시 적용**
- 모든 완제품에 **동일하게** 적용
- 최대 피크 **1.3** ↔ 최대 tightness **1.3** 대응

### ⒝ 이봉 가우시안 (Table 3)

| 파라미터 | 값 |
|---|---|
| `μ₁` | **300** |
| `σ₁` | **50** |
| `μ₂` | **50** |
| `σ₂` | **15** |
| **혼합확률 `p`** | **0.8** |

```
D_j^Uncorr ~ BN(μ₁, μ₂, σ₁, σ₂, p)
```

**이봉성의 현실적 근거 3가지**
1. **신제품** — 신상품이 베스트셀러가 될지 재고가 될지 예측 곤란 (**패션 산업** 전형)
2. **대형 고객** — 다수 소형 고객은 집계 시 가우시안이나, 큰 고객의 **불규칙 대량주문**이 다봉성 유발
3. **신규 시장 진입자** — 주요 경쟁자의 진입/이탈

> ⚠️ 저자 자인: *"We assume that distributions of all end items are bimodal for **every time period**. This is rather **questionable in practice**."* — 의도적으로 **어려운 환경**을 만든 것

### ⒞ ⭐ 패밀리 내 상관 — 2단계 샘플링

**전제**: 패밀리 **간**에는 상관 없음 / 패밀리 **내**에는 **음의 상관**
> 소비자가 특정 구성의 완제품을 사면, 같은 패밀리의 다른 구성을 살 가능성이 낮음

**⚠️ 왜 2단계인가**: *"arbitrary negative correlations may lead to a **correlation matrix that is not positive semidefinite**"*

**Step 1** — 패밀리 집계수요를 **독립적으로** 샘플링 (식 26)
```
F_r ~ BN( n_r μ₁, n_r μ₂, √n_r σ₁, √n_r σ₂, p )
```
(`n_r` = 패밀리 `r`의 완제품 수)

**Step 2** — **Dirichlet 가중치**로 패밀리 내 분배 (식 27–28)
```
(W_1r, …, W_{n_r,r}) ~ Dirch(α_r)
D_j^Fcorr = w_jr · F_r
```
`Σ_{j∈J_r} w_jr = 1`, `w_jr > 0` 보장. Outcast 품목은 패밀리 수요 = 품목 수요.

**Table 4 — 실제 샘플링된 가중치**
| 패밀리 | 가중치 |
|---|---|
| 1 | 8.45%, 1.40%, 5.63%, 16.90%, 5.63%, 11.26%, 14.08%, 5.63%, 8.45%, 4.22%, 7.04%, 11.26% |
| 2 | 26.92%, 3.84%, 3.84%, 19.23%, 11.53%, 7.69%, 26.92% |
| 3 | 12.5%, 25%, 25%, 12.5%, 25% |
| 4 | 25%, 25%, 50% |
| 5 | 42.85%, 14.28%, 42.85% |

## 4-5. ⭐ 시나리오 트리 — 데이터 희소성이 설계를 결정

| 항목 | 설정 |
|---|---|
| 기간 단위 | **1개월** |
| 가용 과거 데이터 | **3년 / 5년 / 10년** |
| 총 관측치 | 36 / 60 / 120개 |
| ⭐ **계절 기간당 관측치** | **3개 / 5개 / 10개** |

> "With such a small amount of data, **estimating a demand distribution is out of the question**, and one has to resort to **purely data-driven approaches**."

**왜 10년을 넘기지 않는가**: 수요에 **추세**가 있을 수 있고, 품목이 **진부화**될 수 있으므로.

### ⭐ 왜 호라이즌을 4기간으로 제한했는가 — 두 가지 이유

> "Considering the entire seasonal pattern would require **12 time periods**, which results in a scenario tree **too big for commercial solvers**. Moreover, considering horizons **greater than 4 periods does not prove to be particularly useful**, because **the machine capacity does not allow for the production of components for such distant periods**."

1. **계산적 이유**: 12기간 트리는 상용 솔버로 감당 불가
2. ⭐ **물리적 이유**: **기계 용량 자체가 그렇게 먼 미래의 부품까지 생산할 수 없다**

> 💡 두 번째가 중요합니다. **W의 상한이 계산 한계가 아니라 "시스템의 물리적 용량"에서 나온다**는 논리 — W 선택을 정당화하는 강력한 논거입니다.

### 진행 순서
```
TS / FOSVA (1기간)  →  MS3, MP_2 (2기간)  →  MS3_3, MP_3 (3기간)  →  MS3_4, MP_4 (4기간)
```

## 4-6. 롤링 호라이즌 시뮬레이션 설계

| 항목 | 설정 |
|---|---|
| 방식 | ⭐ **out-of-sample 롤링 호라이즌 시뮬레이션** |
| **시뮬레이션 호라이즌** | **24개월 (2년)** |
| 결정 주기 | **월 단위** 생산 결정 |
| **반복 횟수** | **10회 독립 복제** |
| 초기 재고 | **각 부품의 실제 평균수요** |
| in/out-of-sample 분포 | **동일 분포** |

**24개월을 택한 이유**
> "The length of the test horizon allows us to **appreciate the periodicity of the solutions** and **mitigate any dependence on the initial inventory**"

→ 실제로 §5.2에서 **2개월째부터 생산량이 주기적 패턴**에 진입함을 확인하고, 이로써 초기상태 의존성을 무시할 수 있다고 논증합니다.

## 4-7. 성능 지표 (§5.1)

| 지표 | 정의 |
|---|---|
| **① 기대 이익** (주 지표) | **완전정보 모델 대비 %** (Table 5) |
| **② 평균 재고 수준** | **완전정보 모델의 최적 재고 대비 %** (Table 6) |
| **③ 평균 lost sales** | ⭐ **전체 방법·tightness·연수를 통틀어 계산한 평균 대비 % 편차** (Table 7) |

> ⚠️ **③이 왜 다른 기준인가**: 완전정보 모델은 수요를 미리 알므로 **평균 lost sale이 0** → 비율 계산 불가. 그래서 **모든 방법의 전체 평균**을 기준으로 삼음.
> 읽는 법: `−10%` = 전체 평균보다 **10% 적은** lost sale / `+100%` = 전체 평균의 **2배**

### ⭐ 계산시간을 보고하지 않는 이유 (명시적 선언)

> "Since **solution times of the models generally do not exceed one minute**, they are not reported and are out of the scope of the paper. [...] Indeed, **the point of the paper is about using a limited amount of available data in the best way, not about solving large-scale stochastic programs**."

> 💡 **Cavagnini·Spinelli와 결정적으로 갈리는 지점**입니다. 저기서는 W가 **계산비용**과의 트레이드오프였지만, 여기서는 트리가 작아 **계산이 병목이 아니고**, W는 순수하게 **해 품질(정보 활용 방식)** 의 문제입니다.

---

# 5. 실험 결과

## 5-1. §5.2 — 2단계 모델군 (TS / TS_noS / FOSVA)

**Figure 7** (10년 데이터, `τ = 1.3`): 24개월 생산비용 + lost sales, 95% 신뢰구간

| 모델 | 행동 |
|---|---|
| **TS_noS** | ⭐ **비계절적 생산 전략을 일관되게 따름** → **막대한 lost sales** |
| **TS** | 계절성은 제대로 따라가나, **터미널 가치 추정이 없어 생산량이 FOSVA보다 낮음** → **계절 피크 대응 실패** → lost sales |
| **FOSVA** | 더 높은 생산량 유지 → 피크 대응 |

**결과**: ⭐ **FOSVA가 모든 구성에서 TS·TS_noS를 능가**
- 원인: TS·TS_noS는 **수요 충족에 필요한 수준보다 낮은 재고** (Table 6)
- Table 7에서 **lost sales가 종종 전체 평균의 2배 이상 (>100%)**

## 5-2. ⭐ Table 5 — 완전정보 대비 이익 % (핵심 결과표)

| 연수 | τ | **FOSVA** | TS | TS_noS | MP_2 | **MP_3** | MP_4 | MS3 | **MS3_3** | MS3_4 |
|---|---|---|---|---|---|---|---|---|---|---|
| **3** | 1.3 | **42.9** | 17.5 | 15.8 | 40.6 | 41.4 | 40.6 | 41.2 | 41.9 | 41.3 |
| | 1.2 | **43.1** | 17.2 | 16.1 | 40.5 | 41.2 | 40.5 | 40.8 | 41.5 | 40.5 |
| | 1.1 | **42.9** | 16.7 | 16.7 | 40.3 | 41.2 | 40.8 | 40.6 | 41.1 | 39.8 |
| | 1.0 | **43.3** | 16.5 | 17.6 | 41.3 | 42.0 | 41.8 | 41.3 | 41.2 | 40.1 |
| **5** | 1.3 | **48.4** | 18.0 | 16.5 | 44.9 | 46.8 | 47.0 | 45.1 | 46.8 | 46.5 |
| | 1.2 | **48.4** | 18.0 | 16.8 | 44.6 | 46.6 | 46.9 | 44.9 | 46.7 | 46.0 |
| | 1.1 | **48.1** | 17.8 | 17.3 | 44.9 | 47.2 | 47.1 | 45.0 | 46.8 | 46.0 |
| | 1.0 | **48.6** | 18.0 | 18.3 | 46.0 | 47.8 | 47.7 | 46.0 | 47.2 | 46.2 |
| **10** | 1.3 | **49.6** | 17.1 | 15.5 | 46.8 | 48.4 | 48.3 | 47.8 | 48.4 | 47.8 |
| | 1.2 | **49.5** | 17.1 | 15.8 | 46.8 | 48.5 | 48.6 | 47.4 | 48.5 | 48.0 |
| | 1.1 | 49.6 | 17.0 | 16.3 | 47.5 | **50.1** | 50.0 | 48.1 | 49.9 | 49.1 |
| | 1.0 | 51.0 | 17.0 | 17.2 | 50.1 | **52.5** | 52.4 | 50.2 | 51.9 | 51.5 |

**핵심 관찰**
1. ⭐ **FOSVA가 12개 구성 중 10개에서 1위**
2. ⭐ **예외는 `τ = 1.0, 1.1` + 10년 데이터** — 이때는 **MP_3**가 승리 (50.1 vs 49.6 / 52.5 vs 51.0)
   - 즉 **용량이 빡빡하고 데이터가 충분할 때만** 긴 lookahead가 유리
3. **MS3_3와 MP_3가 2위권** — FOSVA보다 **1~2% 낮음**
4. ⭐ **MP_4·MS3_4가 MP_3·MS3_3보다 나쁨** — W=4가 W=3보다 열등
5. **TS·TS_noS는 압도적 최하위** (16~18%) — 터미널 가치 없는 순수 2단계는 사실상 무력

## 5-3. Table 6 — 완전정보 대비 평균 재고 %

| 연수 | τ | FOSVA | TS | TS_noS | MP_2 | MP_3 | **MP_4** | MS3 | MS3_3 | **MS3_4** |
|---|---|---|---|---|---|---|---|---|---|---|
| **3** | 1.3 | 119.0 | 85.1 | 73.2 | 117.3 | 125.4 | 126.8 | 118.3 | 126.1 | **128.2** |
| | 1.0 | 113.7 | 83.3 | 73.4 | 111.7 | 122.1 | 123.6 | 111.7 | 122.5 | 125.8 |
| **5** | 1.3 | 120.5 | 83.3 | 73.5 | 120.9 | 131.8 | 134.0 | 123.1 | 131.9 | **135.4** |
| | 1.0 | 113.3 | 81.9 | 73.7 | 114.5 | 126.2 | 128.2 | 114.4 | 125.9 | 129.8 |
| **10** | 1.3 | 120.2 | 79.6 | 72.8 | 120.2 | 133.6 | 135.5 | 123.2 | 133.9 | **138.0** |
| | 1.0 | 111.2 | 76.9 | 71.5 | 111.9 | 124.9 | 127.3 | 112.6 | 124.4 | 129.4 |

(전체 12행 중 대표 행 발췌)

**⭐ 가장 중요한 패턴**
```
TS/TS_noS:  71~85%   ← 재고 부족 → lost sales 폭증
FOSVA:      111~120% ← 2기간 lookahead(MS3, MP_2)와 유사한 수준
MP_3/MS3_3: 122~134%
MP_4/MS3_4: 124~138% ← ⚠️ 과잉 재고
```

> "as the horizon of the tree increases, the **inventory levels increase accordingly**. Nevertheless, **a longer view and higher inventory are not always favorable**; in fact, **MS3_4 and MP_4 hold an excessive inventory, ranking behind MS3_3 and MP_3 in terms of profit, due to the associated holding costs**."

⭐ **FOSVA의 성공 메커니즘이 여기서 드러납니다**: FOSVA는 **W=3, 4 모델보다 낮은 재고를 유지하면서도 더 높은 이익**을 냅니다. 즉 긴 호라이즌은 "재고를 더 쌓게" 만들 뿐이고, FOSVA는 **적정 수준을 정확히 짚어냅니다.**

## 5-4. Table 7 — 전체 평균 대비 lost sales % (발췌)

| 연수 | τ | FOSVA | **TS** | **TS_noS** | MP_2 | MP_3 | MP_4 | MS3 | MS3_3 | MS3_4 |
|---|---|---|---|---|---|---|---|---|---|---|
| 3 | 1.3 | −13.6 | **+101.2** | **+124.0** | −9.1 | −22.9 | −24.3 | −11.2 | −24.4 | −26.7 |
| 5 | 1.3 | −11.2 | **+127.5** | **+155.8** | −12.0 | −35.3 | −39.4 | −16.8 | −35.3 | −41.1 |
| 10 | 1.3 | −12.5 | **+148.5** | **+170.7** | −12.4 | −42.6 | −45.7 | −20.7 | −43.0 | **−49.4** |
| 10 | 1.0 | −7.7 | +109.4 | +122.4 | −9.1 | −32.1 | −35.2 | −12.0 | −31.4 | −38.1 |

**관찰**
- TS·TS_noS: **전체 평균의 2~2.7배** lost sales
- ⭐ **MS3_4가 lost sales는 최소**(−49.4%)인데 **이익은 MS3_3보다 낮음** → **재고비가 lost sale 절감분을 잡아먹음**
- 모든 모델에서 **tightness↑ → lost sales↓**

> "a **good balance** between the number of components in inventory and lost sales **is not achieved for too long horizons**, resulting in lower profits."

## 5-5. §5.4 — 안전재고 휴리스틱 (Table 8)

**설정**: `β = 0, 10, 25, 50`

⚠️ **경험적 분포를 계절성 없이 집계**해 산출
> 이유: *"the number of observed scenarios is too limited, entailing **unreasonably large safety stock values**, driven by an insufficiently sized data pool"*
> 결과: **관측 연수가 유의한 영향을 주지 않음**

**결과**
| 항목 | 값 |
|---|---|
| `SSt_0` | 안전재고 없음 — 시나리오 **평균만**으로 불확실성 인식 |
| 최적 β | ⭐ **β = 10** (이때 재고 수준이 FOSVA와 유사) |
| SSt 평균 이익 (β=10) | **31.17%** |
| TS | 17.32% |
| TS_noS | 16.65% |

**3가지 결론**
1. ⭐ **SSt_50을 제외한 모든 안전재고 방법이 TS·TS_noS를 능가** (31.17% vs 17.32%)
   - 원인: **안전재고 + lookahead 전략의 결합**. 결정론적이어도 **최소 재고 수준을 넣으면 큰 이점**
2. ⭐ **그러나 최선의 SSt_10조차 모든 확률적 방법(FOSVA, MP_*, MS3_*)보다 열등**
   → *"safety stock models **under-perform problems embedding multiple future scenarios**, which provide us with a better hedge against demand uncertainty"*
3. ⭐ **안전재고 휴리스틱만 tightness가 낮아질수록 성능이 나빠짐** (다른 방법과 반대 경향)
   - 안전재고량은 tightness와 무관하게 일정한데, **용량이 작아지면 모델의 유연성이 훼손**되기 때문
   - 예외: `β=50` + 10년 데이터

---

# 6. ⭐ W(예측 호라이즌) 관점 정리

## 6-1. 이 논문에서 W는 무엇인가

**W = 시나리오 트리의 깊이(lookahead 기간 수)**

⚠️ **Cavagnini·Spinelli와 근본적으로 다릅니다**:

| | Cavagnini / Spinelli | **Gioia** |
|---|---|---|
| W의 정체 | **분해 휴리스틱의 파라미터** — 큰 문제를 쪼개는 창 크기 | **모델 그 자체의 깊이** |
| 비교 대상 | 동일 모델의 **W별 성능** | **서로 다른 모델 정식화** |
| 트레이드오프 축 | **해 품질 ↔ 계산시간** | **근시안 ↔ 과잉재고** (계산시간은 무관, 전부 1분 미만) |
| 벤치마크 | 최적해 / 하한 | **완전정보 모델** |
| 평가 방식 | in-sample 최적성 갭 | ⭐ **out-of-sample 롤링 시뮬레이션** |

## 6-2. W 실험 결과 요약

| W | 대표 모델 | 이익 (10년, τ=1.3) | 재고 | 평가 |
|---|---|---|---|---|
| **1** | TS | 17.1 | 79.6 | ❌ 근시안 — 재고 부족 |
| **1 + 터미널가치** | ⭐ **FOSVA** | **49.6** | 120.2 | ✅ **최선** |
| **2** | MP_2 / MS3 | 46.8 / 47.8 | 120.2 / 123.2 | △ |
| **3** | MP_3 / MS3_3 | 48.4 / 48.4 | 133.6 / 133.9 | ✅ 2위권 |
| **4** | MP_4 / MS3_4 | 48.3 / 47.8 | 135.5 / 138.0 | ❌ **과잉재고로 역행** |

## 6-3. ⭐ 이 논문의 핵심 주장

> "The **addition of a terminal value improved performance considerably**, leading to solutions of **better quality than those obtained by models based on a longer planning horizon**. This result is particularly interesting, considering that the approximation of the value of the terminal state is based on a **decomposition with respect to components and neglects the interaction among components** through the bills of materials.
> Moreover, we have shown that **considering a longer planning horizon does not necessarily improve performance**."

**세 겹의 메시지**
1. **W를 늘리는 것보다 W=1에 터미널 가치를 붙이는 게 낫다**
2. 그 터미널 가치가 **부품 간 상호작용을 무시하는 조잡한 근사**인데도 그렇다
3. **긴 계획 호라이즌이 성능을 반드시 개선하지 않는다** — 다른 세팅에서 Birge, Blomvall & Ekblom (2022)도 동일 결론

### 왜 긴 W가 역효과를 내는가 — 메커니즘

```
W↑  →  더 먼 미래의 수요 피크를 봄  →  재고를 더 쌓음
                                            ↓
                              lost sales는 확실히 줄어듦 (Table 7)
                                            ↓
                       ⚠️ 그러나 재고유지비가 그 이득을 상쇄
                                            ↓
                              W=4에서 순이익이 W=3보다 낮아짐
```

여기에 **데이터 희소성**이 겹칩니다 — 계절 기간당 관측치가 3~10개뿐이므로, **먼 미래에 대한 트리는 정보량이 아니라 노이즈를 늘릴** 뿐입니다.

---

# 7. 결론 및 후속 연구

## 논문의 자체 요약 (§6)

1. 유한용량 ATO 생산계획을 위한 **여러 확률계획 모델** 제시
2. ⭐ **오픈소스 시뮬레이션·최적화 프레임워크** 제공
3. **터미널 재고에 가치를 부여해 짧은 호라이즌 모델의 근시안을 줄이는 방법론** 제시
4. **상관·계절성의 영향** 조사, ATO에서 자연스러운 **제품 패밀리 계층 구조** 정의
5. 실무: **MRP의 2단계 MPS/FAS 절차 지원**
6. 알고리즘: ⭐ **제한된 lookahead 트리 + 상태 가치함수 근사를 통합한 ADP 전략**으로 해석 가능

## 후속 연구 방향

논문에 명시적 "future research" 목록이 상세하지는 않으나, 본문에서 도출되는 자연스러운 확장:
- 가치함수 근사에서 **부품 간 상호작용 반영** (현재는 분해로 무시)
- `K`(브레이크포인트 수)의 **체계적 튜닝** — 현재는 "tuning이 필요하다"고만 언급
- 이봉성 가정의 완화 (저자도 "practice에서는 questionable"이라 자인)
- 로트사이징(셋업 비용·시간) 통합

---

# 8. 우리 연구에 쓸 수 있는 것

## 8-1. 인용 포인트

| 주장 | 근거 |
|---|---|
| ⭐ **"긴 계획 호라이즌이 반드시 낫지 않다"** | *"considering a longer planning horizon does not necessarily improve performance"* + Table 5 (W=4 < W=3) |
| ⭐ **"W를 늘리는 대신 터미널 가치를 붙이는 게 낫다"** | *"leading to solutions of better quality than those obtained by models based on a longer planning horizon"* |
| **"순수 2단계는 근시안적이다"** | TS 17.1% vs FOSVA 49.6% (완전정보 대비) |
| **"긴 호라이즌은 과잉재고를 부른다"** | Table 6 — MS3_4가 138.0%, lost sales는 최소인데 이익은 열위 |
| **"W의 상한은 물리적 용량이 정한다"** | *"the machine capacity does not allow for the production of components for such distant periods"* |
| **"안전재고 휴리스틱 < 확률계획"** | SSt_10 31.17% vs 모든 SP 방법 |
| **"결정론 + 안전재고 > 순수 2단계 SP"** | SSt 31.17% vs TS 17.32% ← 반직관적이라 인용가치 높음 |
| **"용량이 빡빡할 때만 긴 lookahead가 유리"** | τ=1.0·1.1 + 10년에서만 MP_3 > FOSVA |

## 8-2. ⭐ 차용할 만한 실험 설계 기법

1. **out-of-sample 롤링 시뮬레이션으로 평가**
   - in-sample 최적성 갭이 아니라 **실제 기대이익**을 측정
   - in/out-of-sample을 동일 분포에서 생성, **10회 독립 복제 + 95% 신뢰구간**

2. **완전정보(perfect information) 모델을 분모로**
   - 모든 지표를 **완전정보 대비 %**로 정규화 → 서로 다른 구성 간 비교 가능
   - ⭐ lost sales처럼 완전정보에서 0이 되는 지표는 **전체 방법 평균을 기준**으로 전환

3. **초기상태 의존성을 실험으로 제거**
   - 24개월로 길게 잡고, **생산량이 2개월째부터 주기적**임을 보여 초기 재고 의존성을 배제

4. ⭐ **tightness factor `τ`로 문제 난이도를 연속 조절**
   - `L_m = τ · (평균 수요 기준 소요시간)`
   - `τ` 최대값을 **최대 계절 계수와 일치**시켜 "사전 재고가 필수인가"의 경계를 만듦
   - W 효과가 **용량 여유도에 따라 뒤집힌다**는 발견이 여기서 나옴

5. **3중 지표로 메커니즘까지 설명**
   - 이익(결과) + **재고 수준** + **lost sales** (원인)
   - → "왜 이 모델이 좋은가"를 재고-품절 균형으로 **설명**할 수 있게 됨

6. **보조 벤치마크를 목적에 맞게 설계**
   - `TS_noS`의 존재 이유가 명확: *"value function estimation is **not equivalent to** considering all the observations together"*
   - 즉 **예상되는 반론을 미리 차단하는 벤치마크**

7. **데이터 희소성을 실험 변수로**
   - 3·5·10년 → 계절 기간당 3·5·10 관측치
   - W 효과가 **데이터 양에 따라 달라진다**는 것을 보임

## 8-3. 이 논문의 한계 (우리가 개선할 여지)

| 한계 | 개선 방향 |
|---|---|
| **W와 트리 형태가 교란됨** | MP_\<n\>(꼬리 분기 1)과 MS3_\<n\>(2레벨 분기) 두 축이 W와 섞임. **분기 구조를 고정하고 W만 변화**시키는 설계 가능 |
| **계산시간을 아예 보고하지 않음** | 트리가 작아서 그렇지만, 규모 확장 시 비교 불가. **FOSVA의 오프라인 비용(`3·K·I` TS 문제)도 미보고** |
| **`K`(브레이크포인트 수) 튜닝 미보고** | "튜닝이 필요하다"고만 언급하고 실험 없음 → **`K` 민감도 분석 여지** |
| **가치함수가 부품 간 상호작용 무시** | BoM 구조를 반영한 근사로 확장 |
| **단일 인스턴스 구성** | 부품 60 / 완제품 35 / 기계 5 고정. **규모 확장 실험 없음** |
| **W 실험이 별도 절로 분리되지 않음** | §4.3과 §5에 흩어져 있음. 우리 논문에서는 **독립된 W 민감도 절**로 구성하면 가독성 우위 |
| **backordering 불가 가정** | 전량 lost sale은 강한 가정 |

---

# 9. 다른 논문과의 관계

| 논문 | 관계 |
|---|---|
| **Cavagnini (2022) / Spinelli (2025)** | W가 **분해 휴리스틱 파라미터** vs **모델 깊이**. 결론은 수렴 — **"중간이 최선, 길다고 좋지 않다"** |
| **Dehghani (2021, Omega)** | 동일 결론 (*"increasing T does not conclusively improve"*). 다만 Dehghani는 2SSP+RHA 정통, Gioia는 **트리 구조 비교** |
| **Birge, Blomvall & Ekblom (2022)** | 저자들이 직접 인용 — **금융 도메인에서 같은 결론** |
| **Fattahi (2022)** | RHA를 평가 장치로 쓰는 점은 유사하나, Fattahi는 W 고정 |
| **Kayacik (2025, EJOR)** | 2SSP의 근시안을 **revision points**로 푸는 접근. Gioia는 **터미널 가치**로 푸는 접근 → **같은 문제의 두 처방** |
| **Carlson, Beckman & Kropp (1982, Decision Sciences)** | 원전 — *"the longest possible forecast horizon is not necessarily the best"*. Gioia가 **41년 뒤 ATO 세팅에서 재현** |

> 💡 **Gioia + Kayacik을 대비시키면 강한 서사가 나옵니다**: 2SSP의 근시안을 고치는 두 가지 길 — ⒜ **미래 상태에 가치를 부여**(Gioia) vs ⒝ **재조정 시점을 추가**(Kayacik). 우리 연구가 어느 쪽인지, 혹은 둘을 결합하는지로 위치를 잡을 수 있습니다.

---

*문서 생성: Claude Code · arXiv:2210.00491v3 (accepted manuscript) 전문 직접 확인*
