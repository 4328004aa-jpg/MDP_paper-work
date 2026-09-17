# 2SSP + RHA 논문 정리 — 기여 · 실험 · W(예측 호라이즌) 중심

> **대상**: `claude MCP` 폴더 내 논문 9편
> **초점**: 각 논문의 **기여**, 수행한 **실험**, **W(prediction horizon) 취급 방식**
> **작성 기준**: 각 논문 PDF 본문 직접 확인 (초록·검색 요약 아님)

---

## 0. 한눈에 보기

| # | 저자 (연도) | 저널 | 모델 | RHA 역할 | W 표기 | **W 실험** | 테스트 값 |
|---|---|---|---|---|---|---|---|
| 1 | **Spinelli** et al. (2025) | EJOR 323 | MSSP | **휴리스틱** | `W` | ✅ **최상급** | 1, 2, 3, 4 (T=6) |
| 2 | **Cavagnini**, Bertazzi, Maggioni (2022) | EJOR 301 | MSSP | **휴리스틱** | `W` | ✅ **우수** | 1, 2, 3 (T=4) |
| 3 | **Dehghani**, Abbasi, Oliveira (2021) | Omega 98 | **2SSP** | **구현 프레임** | `T` | ✅ 간략 | 7, 10, 20 |
| 4 | **Glomb**, Liers, Rösel (2022) | EJOR 300 | 결정론 | **휴리스틱** | decision/forward | ✅ (결정론) | 4개 조합 |
| 5 | **Kayacik** et al. (2025) | EJOR 321 | PAMSP | — | revision points | △ 다른 축 | 1~5회 |
| 6 | **Fattahi** & Govindan (2022) | Decision Sciences 53 | MSSP | **평가 장치** | `\|T\|` | ❌ 고정 4 | — |
| 7 | **Curcio** et al. (2023) | IJPR 61 | 2SSP + RO | **구현 프레임** | ❌ 없음 | ❌ | — |
| 8 | **Quddus** et al. (2017) | C&IE 110 | **2SSP** | **가속 부품** | `R_r` | ❌ 고정 | — |
| 9 | **Long**, Lyu, Liu (2026) | C&IE 220 | **2SSP** | **구현 프레임** | 시간창 | ❌ 단일값 | 120분 고정 |

> ⭐ **핵심 발견**: W 스윕 실험이 있는 논문은 **3편(#1·#2·#3)뿐**이며, 모두 RHA를 **"계산 휴리스틱"으로 쓰는** 논문이다. RHA를 구현 프레임이나 평가 장치로 쓰는 논문(#6·#7·#9)은 W를 고정한다.

---

# 1. 논문별 상세

## 1-1. Spinelli, Maggioni, Ramos, Barbosa-Póvoa, Vigo (2025)

**"A rolling horizon heuristic approach for a multi-stage stochastic waste collection problem"**
*European Journal of Operational Research* 323(1), 276–296 · `10.1016/j.ejor.2024.11.041`
📄 오픈 프리프린트: **arXiv:2405.14499**

### 문제 · 모델
- 재활용 폐기물 수거 **inventory routing problem (IRP)**
- 다단계 확률계획(MSSP), **two-commodity flow** 정식화 (§3.1)
- 목적: 기대 이익 최대화 — 방문할 bin 선택 + 경로 계획
- 시나리오 트리: **conditional density estimation + dynamic stochastic approximation** (실데이터 기반)
- 사용 트리: 32 시나리오 / 63 노드 (안정성 분석으로 결정)

### 기여
1. 폐기물 수거 IRP에 대한 **다단계 확률계획 모델** 제시 (two-commodity flow)
2. 실데이터 기반 **시나리오 트리 생성 절차** (조건부 밀도 추정)
3. ⭐ **RHA의 worst-case 분석 — Theorem 4.1 / 4.2**
4. 다항시간 가해 특수 케이스 규명 (§3.2)
5. 포르투갈 실제 수거업체 데이터 기반 사례연구 + 관리적 시사점

### W 정의 (§4, p.282)

> "First of all, we fix the reduced number **W** of consecutive periods, with **1 ≤ W < T − 1**."

**Pseudocode 1**
- 1단계: `k ≤ T−W` 동안 **(W+1)-stage SP**를 `t = k,…,l`에서 반복, k를 1씩 전진
- 2단계: 꼬리 구간은 `(W+1−j)-stage SP`로 점차 축소
- **W = 1이면 부분문제가 정확히 2SSP**

### ⭐ 이론 결과 (p.282)
- **Theorem 4.1**: 특정 조건(γ=0)에서 RHA 성능 특성화
- **Theorem 4.2**: *"There exists a class of instances such that z^{RH,1} = −∞, even if model is feasible."*
  → **W=1이면 모델이 feasible해도 무한히 suboptimal할 수 있다**

### 실험 목록

| 절 | 실험 | 위치 |
|---|---|---|
| §5.1 | 데이터 분석 (실제 waste accumulation) | p.283 |
| §5.2 | 모델 간 해 비교 | p.284 |
| §5.3 | 불확실성의 영향 + 결정론적 해의 품질 (VSS 계열) | p.284 |
| **§5.4** | ⭐ **W 스윕 — 소규모 30 인스턴스** | **p.285, Fig. 5** |
| **§5.5** | ⭐ **W × 런타임 제한 교차 — 50 bins 실사례** | **p.286, Fig. 6·7** |
| §5.6 | 관리적 시사점 | p.288 |
| Appendix | 인스턴스별 상세 (Table 12–14), 시나리오 트리 안정성 | pp.293–295 |

### W 실험 ①: 소규모 30개 (Fig. 5, p.285)

| W | 평균 profit gap | infeasibility | CPU 절감 |
|---|---|---|---|
| 1 | 29.53% (feasible 25개만) | **30개 중 5개 infeasible** | 97.94% |
| 2 | 29.76% | 없음 | 90.13% |
| 3 | 29.76% (W=2와 동일) | 없음 | 57.00% |
| 4 | **14.92%** | 없음 | 27.56% |

→ **Theorem 4.2가 실제로 관측됨** (W=1에서 첫 2단계 문제부터 infeasible)

### W 실험 ②: 대규모 50 bins × 런타임 제한 TL (Fig. 6·7, p.286)

| W | profit reduction |
|---|---|
| 1 | 25.77% (TL 무관) |
| 2 | **8.68%** (TL 무관) |
| 3 | 9.24% (TL 무관) |
| 4 | TL=2·4h → **58.02%** / TL=12·24h → **8.07%** |

W=4가 짧은 TL에서 붕괴하는 이유가 본문에 명시:
> 첫 두 부분문제가 stage 1–5, 2–6이라 크기가 큰데 각각 **24분·48분**만 배정됨

### 결론
> "the performance of such heuristic **strongly depends on the size W** of the reduced time horizon. If the decision maker requires a good accuracy in a short time, **W = 2 is the best candidate**. On the other hand, if she/he is willing to wait, W = 4 attains better results but in a longer computational time."

**최종 권고: W=2 + 런타임 제한 2시간**

> 💡 **가장 중요한 방법론적 교훈**: W는 **런타임 제한과 상호작용**한다. W만 단독 스윕하면 큰 W를 부당하게 불리하게 평가하게 된다.

---

## 1-2. Cavagnini, Bertazzi, Maggioni (2022)

**"A rolling horizon approach for a multi-stage stochastic fixed-charge transportation problem with transshipment"**
*European Journal of Operational Research* 301(3), 912–922 · `10.1016/j.ejor.2021.11.037`

### 문제 · 모델
- 확률적·동적 수요 하의 **fixed-charge transportation problem**
- 다단계 혼합정수 확률계획(MSSP)
- 1단계 결정: 공급자→소매점 배송 / 보정 수단: **transshipment + backordering**
- 목적: 기대 총비용 최소화

### 기여
1. transshipment를 recourse로 갖는 **다단계 MIP 확률계획 정식화**
2. **NP-hard 증명**
3. **transshipment의 가치** 정량화 (§4)
4. RHA의 실증적 성능 평가 + **W 선택 지침**
5. RHA 해를 **warm start**로 쓰는 하이브리드 전략 (§7.3.1)
6. 관리적 시사점 (§7.5)

### W 정의 (§6, Pseudocode 1)

```
Input: T, 1 ≤ W < T
k := 0, l := W
while k ≤ T − W:   Solve (W+1)-stage SP on t = k,…,l
                   Store x, I;  k += 1, l += 1
j := 1
while k ≤ T − 1:   Solve (W+1−j)-stage SP on t = k,…,T
```

→ Spinelli와 **동일한 구조·표기** (Spinelli가 이 논문을 계승)

### 실험 목록

| 절 | 실험 |
|---|---|
| §7.1 | 인스턴스 설계 — **2개 데이터셋, 총 312 인스턴스**, 6가지 비용 케이스(옵션 A/B × 3) |
| §7.2 | **GUROBI 한계 탐색** — stage 수 증가 시 (Table 4). 5단계에서 out-of-memory, 3단계까지만 최적해 |
| **§7.3** | ⭐ **W 스윕** (T=4, 5단계) |
| §7.3.1 | RHA 해를 warm start로 사용 (Table 5) |
| §7.4 | 민감도 분석 (데이터 변경, backordering 수준) |
| §7.5 | 관리적 시사점 |

### ⭐ 실험 설계의 정교한 점

**① 벤치마크가 최적해가 아니라 lower bound**
- 5단계 시나리오 트리를 **512 시나리오짜리 128개 disjoint 그룹**으로 분할 (Maggioni & Pflug 2016)
- LB 품질 검증: T=3에서 최적비용이 LB보다 **평균 1.33%**만 높음 → LB 양호 판정

**② 시간 배분 설계**
- 전체 `TL_max = 86,400초`
- 부분문제당 `tl_max = TL_max / #subproblems`
- **남은 시간을 다음 부분문제로 이월**

### W 실험 결과 (q=44, LB 대비 비용 증가율)

| W | 평균 | 최대 | CPU (초) |
|---|---|---|---|
| 1 | 10.80% | 28.48% | 354 |
| 2 | **4.96%** | 11.89% | 3,537 |
| 3 | 4.47% | 10.09% | 30,650 |

- 전체 평균 6.74%, 최대 28.48%
- 재고비 낮을 때 W=2는 4.53%
- **W=2 vs W=3**: 재고비 높으면 **성능 동일**, 낮으면 평균 1% 차이 — 반면 **CPU는 약 10배**
- CPU 증가율: **W 1단위당 약 10배**

### 결론
> "The **best compromise** between solution quality and computational time is obtained with **W = 2**. Moreover, the **end-of-horizon effect is already overcome with W = 2**. The takeaway message from this analysis is that **the choice of W is critical** to have good solutions in reasonable computational times."

---

## 1-3. Dehghani, Abbasi, Oliveira (2021)

**"Proactive transshipment in the blood supply chain: A stochastic programming approach"**
*Omega* 98, 102112 · `10.1016/j.omega.2019.102112`

> ⭐ **9편 중 "2SSP + RHA"를 가장 정통으로 구현한 논문**

### 문제 · 모델
- 혈액(적혈구) 공급망 재고관리 — 병원 네트워크 + 중앙 혈액은행
- 각 병원이 매 검토주기마다 **주문량 + 타 병원으로의 transshipment량** 결정
- **2SSP** 정식화 + **rolling horizon**
- 시나리오 생성: **Quasi-Monte Carlo (QMC)** 샘플링

### 기여 (저자 자평)
> "we develop a mathematical model using **two-stage stochastic programming (2SSP) in a rolling horizon framework**"
> "The employment of the aforementioned strategy in the context of this research is **novel**, to the best of our knowledge."

1. 수요 분포 가정 없는 유연한 2SSP 모델 (**비동질 수요분포** 처리 가능 — 문헌 내 신규)
2. **proactive transshipment** 정책 제안 (사후 대응이 아닌 사전 예방)
3. 2SSP + RHA 결합 프레임워크 자체
4. 호주 실제 병원 정책 대비 비용 절감 실증

### MSSP → 2SSP 근사의 두 축
1. **2단계 의사결정을 (R,S) 정책으로 단순 근사** (미래 비용 대리)
   - 2단계에서는 transshipment 결정 없음 (비예측성 보존 목적)
2. **rolling horizon으로 매 기간 재최적화** → 동적 성격 복원

### W 정의 — 표기는 `T`
"the length of the planning horizon to be considered in the two-stage model"
- **Algorithm 1 Step 1**: *"Generate demand scenarios for the next **T** periods"*

### 실험 목록

| 절/위치 | 실험 |
|---|---|
| §4.3, Fig. 2 | **시나리오 수 결정** — in/out-of-sample 안정성. 10~200을 10단위로 20개 세트. in-sample 50개 초과, out-of-sample 100개 초과에서 안정 (T=7 고정 상태에서 수행) |
| **§5, Table 1, p.10** | ⭐ **T(=W) 민감도** |
| §5, Table 2·3·4 | 4개 정책 비교 — TS Model / TS-FIFO / Current Policy / No Transshipment |
| §5, Table 5·6 | 소형 병원이 **격일 주문**하는 경우 |
| §5, Fig. 4 | **(R,S) 근사의 타당성 사후 검증** |
| §5, Fig. 5 | 수혈 시점 혈액 연령 비교 |

### W 실험 결과 (Table 1, p.10)

| T | Shortage rate | Outdate rate | Average cost |
|---|---|---|---|
| **7** | 0.013 | 0.006 | **76.652** ← 최저 |
| 10 | **0.011** ← 최저 | 0.007 | 77.472 |
| 20 | 0.016 | 0.006 | 77.616 ← 최악 |

> "To decide the length of the planning horizon to be considered in the two-stage model, we performed a **sensitivity analysis considering distinct lengths (i.e., values of T)**. **Trading off computational burden and quality of the solution, we opted for a seven-day planning horizon (T = 7).** [...] **increasing the value of T does not conclusively improve the performance** of the optimal policy."

**단조 개선이 아님**: 비용은 T↑에 따라 일관되게 악화, shortage는 T=10 최소 후 T=20에서 재악화

### 실험 규모
- **18,500일 시뮬레이션** (실험당 모델을 18,500번 풂)
- 시뮬레이션 횟수 근거: **DKW(Dvoretzky–Kiefer–Wolfowitz) 부등식** — 경험적 CDF 오차 1% 미만, 신뢰수준 95%
- 각 스텝 1분 미만 (Python 2.7.10 + CPLEX 12.6.2)
- 설정: 유효기간 21일, 병원 수령 시 잔여 11일, 병원 4곳(호주)

### ⚠️ 타 논문과의 구조적 차이
- Spinelli·Cavagnini: 유한 호라이즌 `T`가 따로 있고 **W는 그걸 쪼갠 부분문제 크기**
- Dehghani: **18,500일 무한 반복 시뮬레이션**, `T`는 매 스텝의 **look-ahead 길이**

→ **infinite-horizon rolling에서의 예측 호라이즌**. 실무형 세팅에 더 가까움

---

## 1-4. Glomb, Liers, Rösel (2022)

**"A rolling-horizon approach for multi-period optimization"**
*European Journal of Operational Research* 300(1), 189–206 · `10.1016/j.ejor.2021.07.043`
📄 무료: optimization-online.org/2020/05/7809

> ⚠️ **결정론적 모델** — 확률계획 아님. W 설계의 **이론 프레임워크**로만 활용

### 문제 · 모델
- 시간 구조를 갖는 **일반적 다기간 최적화** 문제
- 적용 대상 2개: **capacitated lot-sizing** (§3), **항공기 tail assignment + fleet assignment + maintenance** (§4)

### 기여
1. ⭐ **호라이즌을 두 개로 분리**: **decision horizon** + **forward(forecast) horizon**
2. ⭐ **safeguarding constraint** — RHA 해에 **증명 가능한 optimality gap 보증** 부여
   (기존 RHA는 휴리스틱이라 품질 보증이 없었음)
3. Algorithm 1(기본 RH) vs Algorithm 3(safeguarded RH) 비교
4. 시간 길이에 **선형 스케일**하는 접근 — 6개월 전체 운항 스케줄까지 확장 가능성

### 실험 목록 (§5)

| 실험 | 내용 |
|---|---|
| §5.1 | 인스턴스 생성 — 8개 공항, 약 800편 항공편, 50개 인스턴스 |
| §5.3-1 | 전체 통합문제 vs Algorithm 1 vs Algorithm 2 vs 기존 휴리스틱 **런타임 비교** |
| §5.3-2 | ⭐ **safeguarding parameter 변화 + 호라이즌 조합 변화** |
| §5.3-3 | 대규모 실무 인스턴스 적용 (12시간 내 해결) |

### 호라이즌 조합 실험

항공편 수 기준 (**등간격이 아니라 포함 항공편 수 기준**으로 호라이즌 설정):

| decision horizon | forward horizon | 위치 |
|---|---|---|
| 60편 | 60편 | Fig. 7 |
| 30편 | 90편 | Fig. 8 |
| 80편 | 160편 | 본문 |
| 120편 | 120편 | 본문 |

- Fig. 7·8: safeguarding parameter별 optimality gap 비교
- 결과: safeguarding constraint가 **품질 보증뿐 아니라 런타임·해 품질도 개선**
- 120/120 조합에서 런타임 개선이 특히 큼 → "후속 결정의 큰 변동이 불리한 정비 계획과 런타임 증가를 유발"하기 때문

---

## 1-5. Kayacik, Basciftci, Schrotenboer, Ursavas (2025)

**"Partially adaptive multistage stochastic programming"**
*European Journal of Operational Research* 321(1), 192–207 · `10.1016/j.ejor.2024.09.034`

> △ W가 아니라 **revision points**(언제·몇 번 재조정할 것인가). **2SSP와 MSSP를 잇는 스펙트럼**을 다룸

### 기여 (저자 명시)
1. ⭐ **PAMSP(Partially Adaptive MSP) 문제 도입** — 의사결정 유연성과 커밋먼트의 균형. **각 결정변수별 최적 재조정 stage를 결정**. **2SSP·MSSP와의 연결 관계 확립**
2. NAC(비예측 제약) 복잡도 해소를 위한 이론적 성질 + **문제 크기 대폭 축소**
3. 정수 L-shaped + Benders 분해를 혼합정수 master/subproblem에 맞게 **맞춤 설계한 cutting plane 알고리즘**
4. 후보해 제거용 **전처리 cut-generation 절차**
5. 두 고전 문제에 적용: **stochastic lot-sizing**, **generation expansion planning**(실데이터 보정)

### 실험 목록 (§5)

| 절 | 실험 |
|---|---|
| §5.1.1 | PAMSP의 잠재력 — MSSP 목적값 수렴 가능성 |
| **§5.1.2** | ⭐ **다중 revision의 중요성** — 계획 호라이즌 길이별 VPAMS |
| **§5.1.3** | ⭐ **최적 revision point의 구조** — 5단계 문제에서 **전 조합 전수조사** |
| §5.1.4 | 계산 성능 |
| §5.2.1~3 | generation expansion (실데이터), 최적 revision stage, 계산 효율 |

### 핵심 결과

**Insight 2 — adaptive 2SSP는 긴 호라이즌에서 붕괴**

| revision 횟수 | 4-stage VPAMS | 10-stage VPAMS |
|---|---|---|
| 1회 (= adaptive 2SSP) | 90–92% | **32–38%** (더 길면 0으로 수렴 = 사실상 static) |
| 3회 | ~100% (6단계까지) | **70–82%** |
| 5회 | — | 더 높음 |

**Insight 3 — 횟수보다 타이밍**
- 5단계 문제 전수조사: 1회면 **4th stage**가 최적, 3rd가 근소 차 2위
- 2회면 최적은 **(3,5) 또는 (4,5)** — 예상되던 (3,4)가 **아님**
- **(3,5) 2회 > (2,3,4) 3회**, **3 또는 4에서 1회 > (2,5) 2회**

**계산 효율**: NAC 제거 기법이 계산시간 **59% 단축**, 알고리즘이 추가로 **56% 단축**

> 💡 **활용**: "2SSP를 RH로 굴리는 것만으로 충분한가"라는 **리뷰어 반론에 대응**하는 근거

---

## 1-6. Fattahi & Govindan (2022)

**"Data-Driven Rolling Horizon Approach for Dynamic Design of Supply Chain Distribution Networks under Disruption and Demand Uncertainty"**
*Decision Sciences* 53(1), 150–180 · `10.1111/deci.12481` · **오픈액세스 (CC-BY)**

### 문제 · 모델
- **동적 공급망 네트워크 설계** — 입지·용량·재고·할당 결정
- 수요 분포의 **적률(moments)이 불확실** + 시설 가용성이 **교란(disruption)**으로 확률적
- **다단계 확률계획(MSSP)**, **conic quadratic MIP(CQMIP)**로 정식화
- **forward scenario tree construction** 기법

### 기여
1. 적률 불확실성 + 교란을 함께 다루는 **MSSP 동적 SCND 모델**
2. **conic quadratic** 정식화
3. ⭐ **data-driven rolling horizon 절차** — 관측된 확률변수 실현값을 활용, MSSP 해를 **실제 구현 가능하게 만들고 평가**
4. forward 시나리오 트리 생성 기법 + 성능 검증
5. 위험 척도(CVaR) 정책의 효율성 분석
6. 관리적 시사점

### W에 해당하는 것 — `|T|`, **4로 고정**

p.161:
> "the optimal implementable decisions related to period t are the given optimal here-and-now decisions at this period, which are achieved by solving the **MSSP comprising t, t+1, …, t+|T|−1 periods**"

→ **고정 길이 |T| 슬라이딩 윈도우** = 진짜 prediction horizon. 그러나 Figure 4 캡션에 `|T| = 4`로 못 박고 **한 번도 변경하지 않음**

### ⚠️ RHA의 역할이 다름

| | Cavagnini / Spinelli | **Fattahi** |
|---|---|---|
| RHA 목적 | **계산 휴리스틱** — 못 푸는 문제를 푸는 수단 | **평가/시뮬레이션 장치** — MSSP 해의 "참 목적함수값" 근사 |
| W는 | 품질↔시간 트레이드오프 대상 → **스윕 필수** | 시뮬레이션 설정값 → **고정해도 논문 성립** |

**검증**: instance 11에서 MSSP 최적값 **2.36E6** vs RH 시뮬레이션 기댓값 **2.45E6** → 상대 차이 **3.8%** (100회 realization)

### 실험 목록

| 위치 | 실험 |
|---|---|
| Table 2 | CQMIP 모델 성능 (인스턴스별) |
| **Table 3, p.166** | ⭐ **RVMS — 2SSP vs MSSP 격차** |
| Fig. 5·6 | instance 11 네트워크 + RH 응답 분포 |
| Table 4 | 운송비·재고비 가중치 민감도 |
| **Fig. 7 (p.168), Table 5 (p.169)** | ⭐ **기간 길이(duration) 실험** |
| Fig. 8 | DC 교란 조건별 분석 |
| Fig. 9 | static vs dynamic 설계 비교 |
| Fig. 10 | data-driven 의사결정 효과 (7%↑ / 2–7% / 2%↓ 구간) |
| Table 6·7 | in-sample / out-of-sample 안정성 |
| Fig. 11, Table 8 | CVaR vs 기댓값 목적함수 |

### ⭐ RVMS (Table 3, p.166) — 2SSP가 얼마나 손해인가

`RVMS = (OF_TS − OF_MS)/OF_TS × 100%` (Huang & Ahmed 2009)
- 12개 인스턴스: **1.81% ~ 5.29%**
- **규모가 커질수록 격차 확대**
- 여기서의 2SSP 정의: 입지·용량 결정을 **모든 기간에 대해** 불확실성 실현 전 확정 (NAC 완화)

### 기간 길이 실험 (Table 5, p.169) — 2년 롤포워드 기대비용

| Instance | 3개월 | 6개월 | 12개월 |
|---|---|---|---|
| 11 | **2.05E6** | 2.18E6 | 2.32E6 |
| 15 | **3.83E6** | 4.02E6 | 4.32E6 |
| 20 | **6.29E6** | 6.58E6 | 6.89E6 |

> "as the length of periods in our planning process decreases, **the performance of the decisions get improved gradually**."

#### ⚠️ 이걸 W 근거로 인용하면 안 되는 이유

`|T|=4` 고정 → 기간 길이 3/6/12개월이면 **캘린더 lookahead는 12/24/48개월**.
두 효과가 **교란(confounded)**되어 있다:
- ⒜ lookahead가 짧아서 좋은 것인지
- ⒝ 재계획을 자주 해서 좋은 것인지

저자는 ⒝로 해석. **순수 W 효과로는 사용 불가.**
반대로 **"W와 재계획 주기를 분리하지 않으면 생기는 문제"의 사례**로는 유용.

---

## 1-7. Curcio, de Lima, Miyazawa, Silva, Amorim (2023)

**"The integrated lot-sizing and cutting stock problem under demand uncertainty"**
*International Journal of Production Research* 61(20), 6691–6717 · `10.1080/00207543.2022.2136279`

### 문제 · 모델
- **lot-sizing + cutting stock 통합** 문제 (제지·섬유·가구 산업)
- 수요 불확실성을 **2SSP**와 **robust optimization** 두 방식으로 각각 모델링
- 두 모델 모두 **column generation**으로 풀도록 설계
- 두 모델 모두 **rolling-horizon 절차에 임베드** → 다단계 세팅으로 적응

### 기여
1. 통합 lot-sizing/cutting stock에 **불확실성 도입** (문헌상 희소)
2. **2SSP와 RO를 동일 문제에서 직접 비교** (위험 선호별)
3. 두 모델 전용 **column generation** 알고리즘
4. **rolling-horizon 절차**로 정적 모델을 동적으로 전환
5. 의사결정자용 **가이드라인 7개조** (§6)

### ❌ W 파라미터 자체가 없음

§3.4, Figure 1:
- RH가 항상 **남은 전체 호라이즌** 사용: `|T|` → `|T|−1` → … → 마지막 기간
- 축소 예측 호라이즌 개념 부재
- NAC 불필요 (다단계 확률계획이 아니므로)

### 실험 목록 (§5) — 3개 계산연구

| 연구 | 내용 |
|---|---|
| 1 | 위험 선호·인스턴스 특성별 두 불확실성 모델 적용 양상 |
| 2 | **column generation 효율** 평가 |
| **3** | ⭐ **rolling-horizon 임베드 모델 평가** (Table 7) |

### RH 실험 결과 (Table 7)

- **500회 몬테카를로 시뮬레이션**, 인스턴스 클래스별 1개
- 2SSP는 100 시나리오, RO는 최소 기대비용 달성 예산·편차값 사용
- 각 모델 solve당 **런타임 제한 100초**
- **VPI**(완전정보의 가치)도 함께 계산

| 모델 | static 대비 |
|---|---|
| 결정론 + RH | **18% 비용 감소** |
| **2SSP + RH** | **16% 감소** |
| Robust + RH | 일부 인스턴스에서 개선 없음 (RO는 기댓값 최적화에 부적합) |

전체: 불확실성 인지로 **최대 39.7% 비용 절감**

> 💡 "2SSP를 RH로 굴리면 static보다 낫다"의 근거로는 좋음. **W 결정 근거로는 사용 불가.**

---

## 1-8. Quddus, Hossain, Marufuzzaman, Jaradat, Roni (2017)

**"Sustainable network design for multi-purpose pellet processing depots under biomass supply uncertainty"**
*Computers & Industrial Engineering* 110, 462–483 · `10.1016/j.cie.2017.06.001`

### 문제 · 모델
- **다목적 펠릿 가공 depot** 기반 바이오매스 공급망 네트워크 설계
- depot 유형 3종: **CPP**(Conventional Pelleting), **HMPP**(High Moisture Pelleting), **AFEX**(Ammonia Fiber Expansion)
- 소비 시장: 바이오리파이너리, 석탄발전, 제지, 사료
- **2SSP MILP**, feedstock 계절성 + 공급 불확실성

### 기여 (저자 명시 gap 2개 → 기여)
1. 소비 시장과 depot 간 복잡한 상호작용을 고려한 **다목적 펠릿 depot 공급망 설계 프레임워크** (문헌 최초)
2. **feedstock 계절성·불확실성이 바이오연료 및 소비 시장에 미치는 영향** 연구
3. ⭐ **하이브리드 분해 알고리즘**: SAA + Progressive Hedging(PHA) + 휴리스틱(HR) + **Rolling Horizon(RH)**
4. PHA 강화 기법 3종: **penalty parameter 갱신**(§3.3.1), **local/global 휴리스틱**(§3.3.2), **RH**(§3.3.3)

### W에 해당하는 것 — `R_r`, 스윕 없음

§3.3.3:
> "Let `t0_r` define the starting time period of subproblem r and **`R_r` denote the number of time periods comprised in subproblem r**. For each subproblem, we can set a **fixed or variable size** of `R_r` which we initialized to `ρ_r` where `ρ_r` is a **pre-specified step size**"

- 변수 처리: `t0_r ≤ t ≤ t0_r + R_r` 구간은 **이진(0/1)**, `t > t0_r + R_r`는 **선형완화(0≤Y≤1)**
  → 전형적인 **relax-and-fix 스타일 RH**
- **`R_r`을 바꿔가며 비교한 실험은 없음**

### ⚠️ RHA의 역할 — **알고리즘 가속 부품**
RH가 독립 해법이 아니라, PHA로 나온 결정론적 다기간 하위문제를 더 빨리 풀기 위한 **추가 가속 장치**

### 실험 목록 (§4)

| 절 | 실험 |
|---|---|
| §4.1 | 데이터 (미국 실제 feedstock 공급지·수요도시 지리분포, Fig. 6–8) |
| §4.2 | ⭐ **해법 알고리즘 성능 비교** (Table 3·5·6) |
| §4.3 | 실험 결과 — feedstock 공급 변동성이 네트워크 구성에 미치는 영향 (Fig. 9·10) |

### 알고리즘 실험 결과
- **[PHA+HR+RH]가 [PHA+HR]를 능가** — **18개 중 16개** 인스턴스에서 우수
- [SAA+PHA+HR] 전체 평균 optimality gap **2.50%**
- ⚠️ **RH on/off 비교이지 W 스윕이 아님**

### 관리적 결과
- 공급 변동성 증가 → **depot 시설 수 증가** (4개 추가)
- 저변동: CPP 3개, HMPP 2개, AFEX 3개 → 고변동: CPP 4개, HMPP 4개…

---

## 1-9. Long, Lyu, Liu (2026)

**"Joint optimization of airport slot allocation and gate assignment: A stochastic integer programming approach"**
*Computers & Industrial Engineering* 220, 112284 · `10.1016/j.cie.2026.112284`

### 문제 · 모델
- 공항 **slot allocation + gate assignment 통합 최적화** (기존엔 분리 연구)
- 용량 제약 + 운항 지연 불확실성
- **2SSP 정수계획**
  - 1단계: slot allocation (사전)
  - 2단계: gate assignment를 **실시간 문제**로 — 불확실성이 **rolling 방식으로 갱신**

### 기여
1. slot allocation과 gate assignment의 **통합 2SSP 정식화** (상호의존성 반영)
2. **Benders 기반 분해** 알고리즘 + optimality cut (§4.2)
3. 하위문제(실시간 gate assignment)용 **RHA + tabu search 휴리스틱** (§4.3)
4. **neighborhood constraint**로 수렴 가속 (§4.4)
5. 상하이 홍차오 국제공항 실제 운항 데이터 기반 검증

### W에 해당하는 것 — 시간창, **단일값 고정**

§4.3.1:
> "δ denotes the **time step** for solving the optimization problem and [W] denotes the **length of the time horizon** considered in the current iteration. At time interval t, only the delay information of the aircraft whose actual arrival time is within the **time window [t, t+W]** is considered."

**두 파라미터 구조**(Glomb와 동형): **time step δ** + **time window(=W)**
- 실험 설정: **δ = 10분, 시간창 = 120분** — **이 조합 하나만 테스트**

### 실험 목록 (§5)

| 절 | 실험 |
|---|---|
| §5.1 | 사례 설정 (상하이 홍차오, 2019년 실제 지연 분포) |
| §5.2 | ⭐ **SAA 표본 크기 N 결정** (Table 2, Fig. 4) — 90% 신뢰구간 + 상대오차 |
| §5.3 | GUROBI vs Tabu search 성능 (Table 3) |
| §5.4 | 테스트 인스턴스 및 모델 크기 (Table 4) |
| §5.5 | optimality cut 수렴 (Fig. 5) |
| §5.6 | neighborhood constraint 계산 성능 (Table 6·7) |
| §5.7 | **확률해의 가치(VSS)** (Table 8) |
| **§5.8** | ⭐ **RHA vs SHA 구성 비교** (Table 9) |
| §5.9 | 스케줄링·운영 통합의 효익 (Fig. 6) |

### §5.8 RHA vs SHA — **on/off 비교이지 W 스윕 아님**

| 구성 | 설정 |
|---|---|
| **RHA** | δ=10분, 시간창=120분 — 불확실성이 온라인으로 순차 공개 |
| **SHA** (static horizon) | 전체 지연 정보를 **완전히 아는** 오프라인 벤치마크 |

결과:
> "The RHA shows **longer computation time and higher costs** than the SHA. [...] this pattern is observed **consistently across all tested α settings**. This is expected because RHA evaluates the slot allocation plan under **progressively revealed uncertainty**, whereas SHA assumes full delay information and therefore provides a **perfect-information benchmark**."

→ SHA는 **완전정보 하한**이므로 RHA가 나쁜 게 당연. 이건 W 선택 실험이 아니라 **정보 구조 비교**

---

# 2. 횡단 분석 — 공통점

## 2-1. ⭐ 가장 중요한 축: **RHA의 역할이 3가지로 갈린다**

이 분류가 **W 실험의 유무를 결정**한다.

### 유형 A — 계산 휴리스틱 (W 스윕 **필수**)
> "원 문제를 못 푸니까 W 길이로 쪼개 푼다. 그럼 얼마나 손해인가?"

| 논문 | W 스윕 | 이유 |
|---|---|---|
| Spinelli (2025) | ✅ 1,2,3,4 | 품질↔시간 트레이드오프를 **정당화해야 함** |
| Cavagnini (2022) | ✅ 1,2,3 | 동일 |
| Glomb (2022) | ✅ 4조합 | 동일 (결정론) |
| Quddus (2017) | ❌ | 예외 — RH가 **부품**이라 on/off만 비교 |

### 유형 B — 구현/동적화 프레임 (W 고정이 **정상**)
> "정적 모델을 실무처럼 매 기간 재최적화한다."

| 논문 | W | 비고 |
|---|---|---|
| Dehghani (2021) | ✅ 7,10,20 | **예외적으로 스윕함** — 그래서 가치 있음 |
| Curcio (2023) | 파라미터 없음 | 항상 잔여 전체 호라이즌 |
| Long (2026) | 120분 고정 | 단일 구성 |

### 유형 C — 평가/시뮬레이션 장치 (W 고정이 **당연**)
> "모델 해를 실제 적용하면 참 성능이 얼마인지 근사한다."

| 논문 | W |
|---|---|
| Fattahi (2022) | `\|T\|`=4 고정 |

> 💡 **논문 쓰실 때**: 본인 연구에서 RHA가 A인지 B/C인지 먼저 정하면, W 스윕이 필수인지 아닌지가 자동으로 결정된다.

---

## 2-2. W 표기·정의 비교

| 논문 | 표기 | 정의 | 상위 호라이즌 |
|---|---|---|---|
| Spinelli | `W` | `1 ≤ W < T−1`, (W+1)-stage 부분문제 | 유한 T=6 |
| Cavagnini | `W` | `1 ≤ W < T`, (W+1)-stage 부분문제 | 유한 T=4 |
| Dehghani | `T` | 2SSP가 바라보는 계획 호라이즌 일수 | **무한** (18,500일 롤링) |
| Fattahi | `\|T\|` | `t, t+1, …, t+\|T\|−1` 슬라이딩 윈도우 | 유한 |
| Quddus | `R_r` | 부분문제 r의 기간 수 (+step size `ρ_r`) | 유한 |
| Long | 시간창 | `[t, t+W]` 내 항공기만 고려 (+ step δ) | 하루 운항 |
| Glomb | decision + forward | **2개로 분리** | 유한 |
| Curcio | — | 없음 (잔여 전체) | 유한 |

### 세 가지 설계 패턴
1. **단일 파라미터형** (Spinelli, Cavagnini, Dehghani, Fattahi, Quddus) — W 하나
2. **이중 파라미터형** (Glomb, Long) — **예측 호라이즌 + 전진 스텝**을 분리
3. **파라미터 없음** (Curcio) — 잔여 전체

> 💡 **이중 파라미터형이 더 엄밀하다.** Fattahi의 교란 문제(§1-6)가 바로 이 분리를 안 해서 생겼다.

---

## 2-3. ⭐ W 실험을 한 3편의 **공통 설계 패턴**

Spinelli · Cavagnini · Dehghani가 공통으로 따르는 절차:

| 단계 | 내용 | Spinelli | Cavagnini | Dehghani |
|---|---|---|---|---|
| ① | **후보 W를 소수(3~4개)로 한정** | 1,2,3,4 | 1,2,3 | 7,10,20 |
| ② | **품질 지표 + 계산시간 지표를 쌍으로 보고** | profit gap + CPU절감 | 비용증가율 + CPU초 | shortage/outdate/cost (시간 생략) |
| ③ | **벤치마크 설정** | 6-stage 최적해 | **lower bound** (LB 품질도 검증) | — |
| ④ | **중간값 선택 + 근거 명시** | W=2 | W=2 | T=7 |
| ⑤ | **"길수록 좋은 게 아니다" 결론** | ✅ | ✅ (W=2에서 이미 충분) | ✅ |
| ⑥ | **선택한 W를 이후 모든 실험에 고정** | ✅ | ✅ (§7.3.1~7.5) | ✅ (전 실험 T=7) |

### 공통 결론 — 3편이 독립적으로 도달
> **"W를 키운다고 단조롭게 좋아지지 않는다. 중간값에서 품질↔시간 최적 균형이 나온다."**

- Cavagnini: **W=2** (W=3은 CPU 10배인데 품질 1% 개선)
- Spinelli: **W=2** (W=3은 품질 동일, W=4는 짧은 TL에서 붕괴)
- Dehghani: **T=7** (T=10, 20은 비용이 더 나쁨)

이 결론은 **IJPR Gioia(2024)**("longer planning horizon can be detrimental")와 **Decision Sciences Carlson et al.(1982)**("the longest possible forecast horizon is not necessarily the best")에서도 독립 재현됨.

---

## 2-4. 9편이 **공통으로 수행한 실험 유형**

| 실험 유형 | 수행 논문 | 비고 |
|---|---|---|
| **시나리오 수 결정 / 안정성** | Spinelli(Appendix), Dehghani(§4.3), Fattahi(Table 6·7), Long(§5.2), Quddus(SAA) | **5/9편** — in-sample + out-of-sample 쌍이 표준 |
| **확률해의 가치** (VSS/EVPI/RVMS/VPAMS/VPI) | Fattahi(RVMS), Kayacik(VPAMS), Curcio(VPI), Long(§5.7), Spinelli(§5.3) | **5/9편** — "왜 확률계획인가"의 필수 방어 |
| **규모별 계산 한계 탐색** | Cavagnini(Table 4), Spinelli(Table 4), Long(Table 4), Quddus(Table 3) | **4/9편** — "휴리스틱이 왜 필요한가"의 근거 |
| **실제 사례연구** | Spinelli(포르투갈 폐기물), Dehghani(호주 병원 4곳), Long(상하이 홍차오), Quddus(미국 feedstock), Fattahi, Glomb(항공사) | **6/9편** |
| **정책/구성 비교** (on/off, static vs dynamic) | Curcio(static vs RH), Fattahi(Fig.9), Long(§5.8), Quddus(§4.2), Dehghani(4정책) | **5/9편** |
| **파라미터 민감도 분석** | Cavagnini(§7.4), Fattahi(Table 4), Spinelli(Table 7), Dehghani(Table 5·6) | **4/9편** |
| **관리적 시사점 절** | Cavagnini(§7.5), Spinelli(§5.6), Fattahi, Quddus(§4.3), Long(§5.9) | **5/9편** — 응용 저널일수록 필수 |
| **W 스윕** | Spinelli, Cavagnini, Dehghani, (Glomb) | **3~4/9편** ← **가장 희소** |

> 💡 **표준 실험 패키지**: ① 시나리오 수 안정성 → ② 계산 한계 탐색 → ③ 확률해의 가치 → ④ 정책 비교 → ⑤ 실사례 → ⑥ 민감도 → ⑦ 관리적 시사점.
> **W 스윕은 이 패키지에 포함되지 않는 선택 항목**이며, RHA가 유형 A일 때만 등장한다.

---

## 2-5. 기여 유형 분류

| 기여 유형 | 해당 논문 |
|---|---|
| **새 문제/모델 정식화** | Spinelli(IRP two-commodity), Cavagnini(transshipment recourse), Quddus(다목적 depot), Long(slot+gate 통합), Dehghani(proactive transshipment), Fattahi(적률불확실+교란) |
| **복잡도 증명** | Cavagnini(NP-hard), Spinelli(NP-hard + 다항시간 특수케이스) |
| ⭐ **RHA 이론 보증** | **Spinelli(Thm 4.1·4.2)**, **Glomb(safeguarding constraint)** |
| **분해/해법 알고리즘** | Quddus(SAA+PHA+HR+RH), Long(Benders+RHA+tabu), Kayacik(cutting plane+NAC제거), Curcio(column generation) |
| **모델링 패러다임 제안** | **Kayacik(PAMSP)** — 2SSP와 MSSP 사이 스펙트럼 |
| **시나리오 생성 기법** | Spinelli(조건부밀도추정), Fattahi(forward tree), Dehghani(QMC) |
| **2SSP+RHA 결합 자체** | **Dehghani** ("novel, to the best of our knowledge") |
| **방법론 비교** | Curcio(2SSP vs RO), Fattahi(2SSP vs MSSP via RVMS) |

---

# 3. 연구 갭 — 우리 논문에서 주장할 수 있는 것

## 3-1. 확인된 갭

1. **"2SSP를 RHA로 굴리면서 W를 체계적으로 스윕한" 논문이 극히 드물다.**
   - W 스윕 3편 중 **Spinelli·Cavagnini는 MSSP**이고, RHA가 그걸 쪼개는 도구
   - **순수 2SSP + RHA + W 스윕**은 **Dehghani(Omega 2021) 단 1편**이며, 그마저 표 1개 + 3문장

2. **W와 전진 스텝(재계획 주기)을 분리해 실험한 논문이 없다.**
   - 이중 파라미터를 **정의**한 논문은 있음 (Glomb, Long)
   - 그러나 **둘을 교차 실험**한 논문은 없음
   - Fattahi는 분리하지 않아 **교란**이 발생

3. **W × 런타임 제한 교차 실험은 Spinelli 1편뿐이다.**
   - 이걸 안 하면 큰 W가 부당하게 불리하게 평가됨

4. **W 선택에 이론 보증을 붙인 논문은 2편뿐** (Spinelli의 worst-case, Glomb의 safeguarding)
   - 나머지는 전부 **경험적 스윕**

## 3-2. 인용 전략 제안

| 주장하려는 것 | 인용할 논문 |
|---|---|
| "W 선택은 중요하다" | **Cavagnini** — *"the choice of W is critical"* |
| "W=1은 위험하다" | **Spinelli Thm 4.2** (`z^{RH,1} = −∞`) + 30개 중 5개 infeasible |
| "길다고 좋은 게 아니다" | **Dehghani**(*"does not conclusively improve"*), **Spinelli**, Gioia(IJPR 2024) |
| "W↑의 대가는 비선형" | **Cavagnini** — CPU 354 → 3,537 → 30,650초 (**단위당 ~10배**) |
| "W는 런타임과 상호작용" | **Spinelli §5.5** — W=4가 58.02% → 8.07% |
| "2SSP+RHA는 정통 접근" | **Dehghani** — *"novel, to the best of our knowledge"* |
| "2SSP는 MSSP 대비 얼마나 손해인가" | **Fattahi RVMS** 1.81~5.29% |
| "2SSP 1회 재조정으로는 부족" | **Kayacik Insight 2** — 10-stage에서 VPAMS 32–38% |
| "RHA가 static보다 낫다" | **Curcio** — 2SSP+RH가 static 대비 16% 개선 |

## 3-3. 실험 설계 권고

본인 논문에서 W 실험을 한다면:

1. **후보 W 3~4개**로 한정 (전수조사 불필요 — 3편 모두 그렇게 함)
2. **품질 지표 + CPU 시간을 반드시 쌍으로** 보고 (Dehghani가 CPU를 뺀 게 약점)
3. **벤치마크 명시** — 최적해가 안 나오면 Cavagnini식 **LB + LB 품질 검증**
4. ⭐ **W × 런타임 제한 교차** (Spinelli 방식) — 없으면 리뷰어가 지적할 여지
5. ⭐ **W와 재계획 주기를 분리** — Glomb/Long의 이중 파라미터 채택
6. **선택한 W를 이후 전 실험에 고정**하고 그렇게 했음을 명시
7. 가능하면 **W=1의 실패 사례**를 보여 W 선택의 필요성을 입증 (Spinelli 방식)

---

# 4. 저널별 분포 (참고)

전수 조사 결과 (OpenAlex 기준):

| 저널 | W 실험 논문 | 조사 결과 |
|---|---|---|
| **EJOR** | **3편** (Spinelli, Cavagnini, Glomb) | 최상급 — 이론 + CPU + 교차실험 |
| **Omega** | 1편 (Dehghani) | 간략 (표 1개) |
| **IJPR** | 1편 (Gioia 2024, 폴더 외) | 2·3·4 기간 |
| **C&IE** | 0편 | rolling horizon 56편 중 2SSP 결합 3편, 모두 W 고정 |
| **Decision Sciences** | 0편 | rolling horizon 14편, 대부분 1977–92 MRP/MPS |
| **Mathematics (MDPI)** | 0편 | rolling horizon + stochastic programming **0건** |
| **TRE** | 0편 | 확인된 것 없음 |

## 왜 EJOR에 몰리는가
W 실험은 RHA를 **계산 휴리스틱**으로 쓸 때만 필요하다 — 품질↔시간 트레이드오프를 정당화해야 하므로. 그런 **알고리즘·분석 지향** 논문이 EJOR로 간다.
반면 Omega·C&IE·Decision Sciences·Mathematics는 **응용·관리 인사이트 지향**이라 RHA를 구현 프레임이나 평가 장치로 쓰고, 그러면 W를 고정해도 논문이 성립한다.

> 💡 **목표 저널이 응용 쪽이면 W 스윕이 필수 관문은 아니다.** 다만 있으면 방법론적 엄밀성에서 확실한 차별점이 된다.

---

# 5. 역사적 계보 (참고)

W 실험의 뿌리는 **Decision Sciences**에 있다 (확률계획은 아니지만):

| 논문 | 연도 | 기여 |
|---|---|---|
| **Baker** | 1977 | 원전 — 유한 다기간 모델을 rolling 방식으로 구현했을 때의 효율 실험 |
| **Carlson, Beckman, Kropp** | 1982 | ⭐ *"the **longest possible forecast horizon is not necessarily the best**"*, *"effectiveness **fluctuates widely depending upon the length of the forecast horizon**"* |
| Blackburn & Millen | 1980 | rolling-schedule 환경의 lot-sizing 휴리스틱 |
| **Chung & Krajewski** | 1986 | ⭐ **replanning frequency** — W와 재계획 주기의 분리 |
| Sridharan & Berry | 1990 | freeze interval + 수요 불확실성 |

**1982년 결론이 2020년대 논문에서 그대로 재현되고 있다.** 도입부에서 이 계보를 밝히면 설득력이 크게 올라간다.

---

*문서 생성: Claude Code · 논문 9편 PDF 본문 직접 확인*
