# EIGRP Network Pre-Configuration Check Report

> **Routing Protocol**: EIGRP AS 100  
> **Total Routers**: 10 (GIT, ISP-1 ~ ISP-7, R1, R2)  
> **Check Date**: Pre-config verification  
> **Status**: ✅ All neighbor relations stable (Passive)

---

## 📑 Table of Contents

- [Topology Overview](#topology-overview)
- [Summary Table](#summary-table)
- [Router Details](#router-details)
  - [GIT](#git)
  - [ISP-1](#isp-1)
  - [ISP-2](#isp-2)
  - [ISP-3](#isp-3)
  - [ISP-4](#isp-4)
  - [ISP-5](#isp-5)
  - [ISP-6](#isp-6)
  - [ISP-7](#isp-7)
  - [R1](#r1)
  - [R2](#r2)
- [Key Findings](#key-findings)

---

## Topology Overview

```
              GIT (123.123.123.123/Lo0, 168.110.21.62)
               │ Se1/0  121.160.11.64/30
               │
           ┌──ISP-1──── Fa0/0 ────ISP-2────Fa0/0────ISP-3────Fa0/0────ISP-4
           │ (.93)     121.160.11.68/30    │       .72/30   │        .76/30
           │ Se1/0                          │                │
           │ .92/30                         │                │ Fa0/0  .80/30
           │                                │                │ Se1/0  .100/30 ── R2 (22.22.22.22)
         ISP-7 ──Se1/0── ISP-6              │              ISP-5
        (.107.7)  .88/30 (.106.6)           │              (.105.5)
                                            │              │ Se1/0 .96/30 ── R1 (11.11.11.11)
                                            │              │ Se1/1 .84/30 ── ISP-6
                                            └─ Loopbacks: 131.116.x.x
```

**Link Subnets** (all `/30`):

| Subnet | Endpoints |
|---|---|
| 121.160.11.64/30 | GIT ↔ ISP-1 |
| 121.160.11.68/30 | ISP-1 ↔ ISP-2 |
| 121.160.11.72/30 | ISP-2 ↔ ISP-3 |
| 121.160.11.76/30 | ISP-3 ↔ ISP-4 |
| 121.160.11.80/30 | ISP-4 ↔ ISP-5 |
| 121.160.11.84/30 | ISP-5 ↔ ISP-6 |
| 121.160.11.88/30 | ISP-6 ↔ ISP-7 |
| 121.160.11.92/30 | ISP-7 ↔ ISP-1 |
| 121.160.11.96/30 | ISP-5 ↔ R1 |
| 121.160.11.100/30 | ISP-4 ↔ R2 |

---

## Summary Table

| Router | Router ID | Neighbors | EIGRP Routes | Summary | Status |
|---|---|---|---|---|---|
| GIT   | 123.123.123.123 | 1 | 14 | - | ✅ |
| ISP-1 | 101.101.101.1   | 3 | 13+ | 131.116.0.0/21 → Se1/1 | ✅ |
| ISP-2 | 131.116.1.2     | 2 | 13 | - | ✅ |
| ISP-3 | 131.116.4.3     | 2 | 13 | - | ✅ |
| ISP-4 | 131.116.6.4     | 3 | 12 | - | ✅ |
| ISP-5 | 101.101.105.5   | 3 | 13 | 131.116.0.0/21 → Se1/0 | ✅ |
| ISP-6 | 131.116.7.6     | 2 | 16 | - | ✅ |
| ISP-7 | 101.101.107.7   | 2 | 15 | - | ✅ |
| R1    | 11.11.11.11     | 1 | 14 | - | ✅ |
| R2    | 22.22.22.22     | 1 | 17 | - | ✅ |

**Common Settings (All Routers)**
- EIGRP AS: `100`
- K-values: `K1=1, K2=0, K3=1, K4=0, K5=0` (default)
- Maximum hopcount: `100`
- Variance: `1` (no unequal-cost load balancing)
- Auto-summary: `disabled`
- Maximum paths: `4`

---

## Router Details

### GIT

#### Interfaces
| Interface | IP Address | Status |
|---|---|---|
| Fa0/0 | 168.110.21.62 | up/up |
| Se1/0 | 121.160.11.65 | up/up |
| Lo0   | 123.123.123.123 | up/up |

#### EIGRP Neighbors
| Address | Interface | Uptime | SRTT(ms) | Q |
|---|---|---|---|---|
| 121.160.11.66 | Se1/0 | 00:34:35 | 15 | 0 |

#### EIGRP Routes (D)
| Network | Metric | Next-Hop | Interface |
|---|---|---|---|
| 100.10.1.0/24       | 41126400 | 121.160.11.66 | Se1/0 |
| 168.126.64.0/24     | 41152000 | 121.160.11.66 | Se1/0 |
| 168.126.63.0/24     | 40565760 | 121.160.11.66 | Se1/0 |
| 101.101.101.0/24    | 40640000 | 121.160.11.66 | Se1/0 |
| 101.101.102.0/24    | 40665600 | 121.160.11.66 | Se1/0 |
| 101.101.103.0/24    | 40691200 | 121.160.11.66 | Se1/0 |
| 101.101.104.0/24    | 40716800 | 121.160.11.66 | Se1/0 |
| 101.101.105.0/24    | 40742400 | 121.160.11.66 | Se1/0 |
| 101.101.106.0/24    | 41254400 | 121.160.11.66 | Se1/0 |
| 101.101.107.0/24    | 41152000 | 121.160.11.66 | Se1/0 |
| 200.10.1.0/24       | 41152000 | 121.160.11.66 | Se1/0 |
| 22.22.22.0/24       | 41228800 | 121.160.11.66 | Se1/0 |
| 131.116.0.0/21      | 40665600 | 121.160.11.66 | Se1/0 |
| 11.11.11.0/24       | 41254400 | 121.160.11.66 | Se1/0 |
| 121.160.11.80/30    | 40614400 | 121.160.11.66 | Se1/0 |
| 121.160.11.84/30    | 41126400 | 121.160.11.66 | Se1/0 |

#### Topology (Successor / FS)
- All prefixes have **1 successor**, **no Feasible Successor** (stub-like single uplink)
- Routing for Networks: `121.160.11.64/30`, `123.123.123.0/24`, `168.110.21.0/26`

#### Traffic
- Hellos sent/recv: 453 / 452 · Updates: 4/11 · Queries: 0/8 · Replies: 8/0

---

### ISP-1

#### Interfaces
| Interface | IP Address | Status |
|---|---|---|
| Fa0/0 | 121.160.11.69 | up/up |
| Se1/0 | 121.160.11.93 | up/up |
| Se1/1 | 121.160.11.66 | up/up |
| Lo0   | 101.101.101.1 | up/up |

#### EIGRP Neighbors
| Address | Interface | Uptime |
|---|---|---|
| 121.160.11.94 | Se1/0 | 00:34:38 |
| 121.160.11.65 | Se1/1 | 00:34:40 |
| 121.160.11.70 | Fa0/0 | 00:34:45 |

#### Topology — Multi-path Detected ⭐
| Network | Successor (FD/AD) | Additional Path (FD/AD) | FS? |
|---|---|---|---|
| 101.101.106.0/24 | via .70 (20742400/20716800) Fa0/0 | via .94 (21152000/20640000) Se1/0 | **✅ AD 20640000 < FD 20719360 → FS** |

→ ISP-1은 101.101.106.0/24 에 대해 **Feasible Successor 보유**.

#### Address Summarization
- `131.116.0.0/21` advertised on Se1/1 (toward GIT) with metric 409600

#### Traffic
- Hellos: 1364/1356 · Updates: 39/27 · Queries: 21/7 · Replies: 6/21

---

### ISP-2

#### Interfaces
| Interface | IP Address | Status |
|---|---|---|
| Fa0/0 | 121.160.11.73 | up/up |
| Fa0/1 | 121.160.11.70 | up/up |
| Lo0   | 101.101.102.2 | up/up |
| Lo1   | 131.116.0.2   | up/up |
| Lo2   | 131.116.1.2   | up/up |

#### EIGRP Neighbors
| Address | Interface | Uptime |
|---|---|---|
| 121.160.11.74 | Fa0/0 | 00:34:50 |
| 121.160.11.69 | Fa0/1 | 00:34:50 |

#### Topology
- All prefixes: 1 successor, FS not visible (no alternate paths in topology output)

#### Traffic
- Hellos: 914/916 · Updates: 36/35 · Queries: 10/12 · Replies: 11/9

---

### ISP-3

#### Interfaces
| Interface | IP Address | Status |
|---|---|---|
| Fa0/0 | 121.160.11.77 | up/up |
| Fa0/1 | 121.160.11.74 | up/up |
| Fa2/0 | 168.126.63.254 | up/up |
| Lo0/1/2 | 101.101.103.3 / 131.116.3.3 / 131.116.4.3 | up/up |

#### EIGRP Neighbors
| Address | Interface | Uptime |
|---|---|---|
| 121.160.11.73 | Fa0/1 | 00:35:00 |
| 121.160.11.78 | Fa0/0 | 00:35:00 |

#### Traffic
- Hellos: 920/1503 · Updates: 43/41 · Queries: 14/9 · Replies: 8/11

---

### ISP-4

#### Interfaces
| Interface | IP Address | Status |
|---|---|---|
| Fa0/0 | 121.160.11.81 | up/up |
| Fa0/1 | 121.160.11.78 | up/up |
| Se1/0 | 121.160.11.101 | up/up |
| Lo0/1/2 | 101.101.104.4 / 131.116.5.4 / 131.116.6.4 | up/up |

#### EIGRP Neighbors
| Address | Interface | Uptime |
|---|---|---|
| 121.160.11.102 | Se1/0 | 01:20:07 |
| 121.160.11.77  | Fa0/1 | 01:20:11 |
| 121.160.11.82  | Fa0/0 | 01:20:11 |

#### Traffic
- Hellos: 3137/1370 · Updates: 55/41 · Queries: 18/13 · Replies: 11/16

---

### ISP-5

#### Interfaces
| Interface | IP Address | Status |
|---|---|---|
| Fa0/1 | 121.160.11.82 | up/up |
| Se1/0 | 121.160.11.97 | up/up |
| Se1/1 | 121.160.11.86 | up/up |
| Lo0   | 101.101.105.5 | up/up |

#### EIGRP Neighbors
| Address | Interface | Uptime |
|---|---|---|
| 121.160.11.85 | Se1/1 | 00:34:54 |
| 121.160.11.98 | Se1/0 | 00:34:56 |
| 121.160.11.81 | Fa0/1 | 00:35:01 |

#### Topology — Multi-path Detected ⭐
| Network | Successor (FD/AD) | Additional Path (FD/AD) | FS? |
|---|---|---|---|
| 101.101.107.0/24 | via .81 (20742400/20716800) Fa0/1 | via .85 (21152000/20640000) Se1/1 | **✅ AD 20640000 < FD 20719360 → FS** |

#### Address Summarization
- `131.116.0.0/21` advertised on Se1/0 (toward R1) with metric 409600

#### Traffic
- Hellos: 1373/1957 · Updates: 38/32 · Queries: 20/6 · Replies: 5/20

---

### ISP-6

#### Interfaces
| Interface | IP Address | Status |
|---|---|---|
| Fa0/0 | 168.126.64.254 | up/up |
| Se1/0 | 121.160.11.85 | up/up |
| Se1/1 | 121.160.11.89 | up/up |
| Lo0/1 | 101.101.106.6 / 131.116.7.6 | up/up |

#### EIGRP Neighbors
| Address | Interface | Uptime |
|---|---|---|
| 121.160.11.86 | Se1/0 | 00:34:54 |
| 121.160.11.90 | Se1/1 | 00:34:54 |

#### Topology — Multiple FS Candidates ⭐⭐
ISP-6는 토폴로지 링 위치상 거의 모든 prefix에 대체 경로를 보유:

| Network | Successor (FD/AD) | Alt Path (FD/AD) | FS Status |
|---|---|---|---|
| 11.11.11.0/24    | via .86 (41152000/40640000) Se1/0 | via .90 (41766400/41254400) Se1/1 | AD 41254400 ≥ FD 41152000 → ❌ |
| 22.22.22.0/24    | via .86 (21177600/20665600) Se1/0 | via .90 (21740800/21228800) Se1/1 | AD 21228800 ≥ FD 21154560 → ❌ |
| 101.101.101.0/24 | via .86 (20742400/486400) Se1/0   | via .90 (21152000/20640000) Se1/1 | ❌ |
| 101.101.102.0/24 | via .86 (20716800/460800) Se1/0   | via .90 (21177600/20665600) Se1/1 | ❌ |
| 101.101.103.0/24 | via .86 (20691200/435200) Se1/0   | via .90 (21203200/20691200) Se1/1 | ❌ |
| 101.101.104.0/24 | via .86 (20665600/409600) Se1/0   | via .90 (21228800/20716800) Se1/1 | ❌ |

→ Alt paths exist but **none satisfy Feasibility Condition** (AD ≥ current FD).  
→ 대체 경로는 있지만 EIGRP가 즉시 사용할 FS는 없음 (장애 시 Query 발생).

#### Traffic
- Hellos: 915/911 · Updates: 23/29 · Queries: 0/7 · Replies: 7/0

---

### ISP-7

#### Interfaces
| Interface | IP Address | Status |
|---|---|---|
| Se1/0 | 121.160.11.90 | up/up |
| Se1/1 | 121.160.11.94 | up/up |
| Lo0   | 101.101.107.7 | up/up |

#### EIGRP Neighbors
| Address | Interface | Uptime |
|---|---|---|
| 121.160.11.93 | Se1/1 | 00:34:52 |
| 121.160.11.89 | Se1/0 | 00:34:54 |

#### Topology — Multi-path Detected ⭐
| Network | Successor (FD/AD) | Alt Path (FD/AD) | FS Status |
|---|---|---|---|
| 11.11.11.0/24    | via .93 (41254400/40742400) Se1/1 | via .89 (41664000/41152000) Se1/0 | ❌ |
| 22.22.22.0/24    | via .93 (21228800/20716800) Se1/1 | via .89 (21689600/21177600) Se1/0 | ❌ |
| 101.101.101.0/24 | via .93 (20640000/128256) Se1/1   | via .89 (21254400/20742400) Se1/0 | ❌ |
| 101.101.102.0/24 | via .93 (20665600/409600) Se1/1   | via .89 (21228800/20716800) Se1/0 | ❌ |
| 101.101.103.0/24 | via .93 (20691200/435200) Se1/1   | via .89 (21203200/20691200) Se1/0 | ❌ |
| 101.101.104.0/24 | via .93 (20716800/460800) Se1/1   | via .89 (21177600/20665600) Se1/0 | ❌ |
| 101.101.105.0/24 | via .93 (20742400/486400) Se1/1   | via .89 (21152000/20640000) Se1/0 | ❌ |

#### Notable
- Passive Interfaces 목록에 `VoIP-Null0` 포함 (IOS 자동 생성, 정상)

#### Traffic
- Hellos: 910/922 · Updates: 25/29 · Queries: 0/8 · Replies: 8/0

---

### R1

#### Interfaces
| Interface | IP Address | Status |
|---|---|---|
| Fa0/0 | 200.10.1.254 | up/up |
| Se1/1 | 121.160.11.98 | up/up |
| Lo0   | 11.11.11.11  | up/up |

#### EIGRP Neighbors
| Address | Interface | Uptime |
|---|---|---|
| 121.160.11.97 | Se1/1 | 00:34:50 |

#### Topology
- All prefixes: 1 successor via ISP-5 (single uplink → no FS possible)
- 131.116.0.0/21 (summary) 학습 확인 ✅

#### Traffic
- Hellos: 455/460 · Updates: 4/10 · Queries: 0/7 · Replies: 7/0

---

### R2

#### Interfaces
| Interface | IP Address | Status |
|---|---|---|
| Fa0/0 | 100.10.1.254 | up/up |
| Se1/1 | 121.160.11.102 | up/up |
| Lo0   | 22.22.22.22  | up/up |

#### EIGRP Neighbors
| Address | Interface | Uptime |
|---|---|---|
| 121.160.11.101 | Se1/1 | 00:34:56 |

#### Topology
- All prefixes: 1 successor via ISP-4 (single uplink → no FS possible)
- 131.116.0.0/24 sub-prefixes 모두 학습 ✅

#### Traffic
- Hellos: 459/1047 · Updates: 4/10 · Queries: 0/6 · Replies: 6/0

---

## Key Findings

### ✅ Healthy Items

1. **All EIGRP neighbor adjacencies are stable** — Q Cnt = 0 across all routers
2. **AS 100 / K-values / no auto-summary** consistent across all 10 routers
3. **Manual summarization** working correctly:
   - ISP-1: `131.116.0.0/21` → GIT 방향
   - ISP-5: `131.116.0.0/21` → R1 방향
4. **End-to-end reachability** confirmed (ISP-4 ping tests 100% success)
5. **All loopbacks set as passive** appropriately (no unnecessary EIGRP on stub interfaces)

### ⚠️ Observations

1. **Feasible Successor 부족**: 토폴로지가 링(ring) 구조임에도 불구하고 대부분의 prefix에 FS가 존재하지 않음
   - 원인: 시계방향/반시계방향 경로의 metric 차이가 작아 FC(AD < FD) 미충족
   - 영향: Successor 장애 시 즉시 fail-over 대신 **Query/Reply 과정**이 발생하여 수렴 시간 증가
   - 해결안: `variance` 조정 또는 인터페이스 delay 튜닝으로 FS 확보 검토

2. **FS 보유 prefix 요약**:
   - ISP-1: `101.101.106.0/24` ✅
   - ISP-5: `101.101.107.0/24` ✅
   - 그 외 라우터: FS 없음 또는 단일 uplink

3. **GIT, R1, R2는 stub 형태**: 단일 이웃만 보유 → 구조상 FS 불가능. `eigrp stub` 설정 고려 권장

4. **ISP-3, ISP-4에서 일부 출력 truncation** 확인 (`show ip route eigrp` 마지막 라인 잘림). 실제 학습 prefix는 더 많을 가능성.

### 📋 Recommendations

| 항목 | 권고 사항 |
|---|---|
| Stub 라우터 | GIT, R1, R2에 `eigrp stub connected summary` 적용 검토 |
| Variance | 백업 경로 활용을 위해 `variance 2` 이상 검토 |
| Summary | ISP-3/ISP-4의 131.116.x 대역도 추가 summarization 검토 |
| Output | 운영 점검 시 `terminal length 0` 적용으로 출력 truncation 방지 |
| 문서화 | 본 보고서를 변경 작업 baseline으로 보관 |

---

## Verification Commands Used

```cisco
show ip interface brief
show ip eigrp neighbors
show ip route eigrp
show ip protocols
show ip eigrp topology
show ip eigrp topology all-links
show ip eigrp interfaces
show ip eigrp traffic
```

---

*Report generated from pre-configuration verification output.*
