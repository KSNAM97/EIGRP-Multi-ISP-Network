# DUAL 알고리즘 (Diffusing Update Algorithm)

## 📌 일반 라우팅 vs EIGRP DUAL

### 일반 라우팅
1. 복수 경로 중 최적 경로 선출
2. 최적 경로 장애 발생 시 → **나머지 경로 재계산** → 새 최적 경로 선출

### EIGRP DUAL
1. EIGRP 연산으로 **Topology Table에서 Successor 선출** → Routing Table 등록
2. **DUAL 알고리즘**으로 **Feasible Successor(차선 경로) 미리 선출**
3. 장애 발생 시 즉시 차선 경로로 전환 → **빠른 수렴**

## 🔑 핵심 용어

| 용어 | 정의 |
|------|------|
| **FD** (Feasible Distance) | **출발지부터 목적지까지의 총 거리** |
| **AD** (Advertise Distance) | **출발지부터 Next-hop까지의 거리를 제외한 나머지 거리** |

> ⚠️ **FD값이 Routing Table에 등록되는 실제 Metric값**이며, AD값은 **DUAL 계산 시에만 사용**된다.

## 🧮 DUAL 조건 (3단계)

### ✅ 조건 1: Successor 선출
**가장 작은 FD값**을 가진 경로가 Successor로 선출되어 **Routing Table에 등록**

### ✅ 조건 2: Feasible Successor 후보 선별
**Successor의 FD값보다 작은 AD값**을 가진 경로가 **Feasible Successor**로 선출

```
조건: Successor의 FD > 후보 경로의 AD
```

### ✅ 조건 3: 다수일 경우 우선순위
조건 2를 만족하는 경로가 **다수일 경우**:
→ 그 중 **가장 작은 FD값**을 가진 경로가 Feasible Successor

## 📊 DUAL 예시 (개념도)

```
        [Router A]──────[Router B]──────[목적지 N/W]
            │                              ↑
            │                              │
            └────[Router C]────────────────┘
```

| 경로 | FD (총 거리) | AD (Next-hop 제외) |
|------|-------------|-------------------|
| A → B → 목적지 | 100 (Successor) | 60 |
| A → C → 목적지 | 150 | **80** ← Successor FD(100) 보다 작음 ✅ |
| A → D → 목적지 | 200 | 120 ← Successor FD(100) 보다 큼 ❌ |

→ **A → C 경로가 Feasible Successor**로 선출

## 🎯 DUAL의 장점

- 장애 발생 시 **재계산 없이** 즉시 차선 경로 전환
- **빠른 수렴(Convergence) 시간**
- Loop-free 보장 (AD < FD 조건이 Loop 방지 역할)
