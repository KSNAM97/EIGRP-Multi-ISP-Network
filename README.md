# 🌐 EIGRP 기반 Multi-ISP 네트워크 구축 프로젝트

<div align="center">

![EIGRP](https://img.shields.io/badge/Protocol-EIGRP-blue?style=flat-square)
![Cisco](https://img.shields.io/badge/Cisco-IOS-1BA0D7?style=flat-square&logo=cisco&logoColor=white)
![GNS3](https://img.shields.io/badge/Simulator-GNS3-orange?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)

**EIGRP 기반 Multi-ISP 백본 네트워크 구축 및 Route Summarization 실습 (Cisco IOS / GNS3)**

</div>

---

GNS3 / Cisco IOS 기반으로 **7개 ISP 라우터**와 **3개 고객사 라우터(GIT, R1, R2)**를 EIGRP로 연동하고, 주소 요약(Route Summarization)을 통해 라우팅 테이블을 최적화한 프로젝트입니다.

## 📌 프로젝트 개요

| 항목 | 내용 |
|------|------|
| **사용 프로토콜** | EIGRP (AS 100) |
| **라우터 수** | 10대 (ISP 7대 + GIT, R1, R2) |
| **주요 기술** | VLSM, Route Summarization, Passive Interface, Loopback 광고, DUAL 알고리즘 |
| **시뮬레이터** | GNS3 (Cisco IOS c3745) |

## 🗺️ 네트워크 토폴로지

![EIGRP Multi-ISP Network Topology](topology/eigrp_isp_topology.png)

### 구성 요소

- **ISP 백본**: 7개 라우터 (ISP-1 ~ ISP-7) — 다중 경로 redundancy 구성
- **고객사 라우터**: 3개 (GIT, R1, R2)
- **WAN 링크**: HDLC 캡슐화 (64Kbps / 128Kbps)
- **LAN 구간**: GIT-SW, ISP-3-SW, ISP-6-SW, R1-SW, R2-SW

## 🎯 요구사항

1. 토폴로지에 따라 각 네트워크 구간에 IP 주소 할당
2. 각 라우터에 **Loopback 0 생성** (식별용)
   - GIT: `123.123.123.123/24`
   - R1: `11.11.11.11/24`
   - R2: `22.22.22.22/24`
   - ISP-X: `101.101.10X.X/24`
3. **ISP-2/3/4/6에 Loopback 1, 2 생성** (`131.116.X.0/24`, X = ISP 번호)
4. **EIGRP AS 100** 설정, `no auto-summary`, Passive Interface 적용
5. **GIT 라우터와 R1 라우터**에서 `131.116.0.0/21`로 요약된 단일 네트워크 확인
6. GIT 내부 단말에서 모든 `131.116.X.0/24` 네트워크와 통신 가능

## 📂 디렉토리 구조

```
EIGRP-Multi-ISP-Network/
├── 📁 topology/                              # 네트워크 토폴로지 다이어그램
│   └── eigrp_isp_topology.png
├── 📁 docs/                                  # EIGRP 이론 정리 문서
│   ├── 01-eigrp-theory.md                    # EIGRP 개요, AD/Metric, K-상수
│   ├── 02-eigrp-pdu.md                       # 5가지 PDU (Hello/Update/Query/Reply/Ack)
│   └── 03-dual-algorithm.md                  # DUAL 알고리즘 (Successor, FD/AD)
├── 📁 preconfig/                             # 라우터 설정 단계별 파일 (순서대로 적용)
│   ├── 01-router-ip-preconfig                # 인터페이스 IP 할당
│   ├── 02-loopback0-preconfig                # 라우터 식별용 Loopback 설정
│   ├── 03-loopback1-2-preconfig              # 131.116.x.0/24 Loopback 설정
│   ├── 04-eigrp-routing-preconfig            # EIGRP AS 100 라우팅 설정
│   └── 05-eigrp-ip-summary-preconfig         # 주소 요약 (Route Summarization)
├── 📁 connection-check/                      # 통신 확인 및 검증
│   ├── verification-commands.md              # 검증 명령어 가이드
│   └── verification-command-preconfig        # 실제 검증 결과 (show 명령 출력)
├── 📄 LICENSE                                # MIT License
└── 📄 README.md
```

## 🔄 설정 순서

`preconfig/` 폴더의 파일을 **번호 순서대로** 적용한 후, 마지막에 검증을 진행합니다.

| 단계 | 파일 | 내용 |
|:----:|------|------|
| **1** | [`01-router-ip-preconfig`](preconfig/01-router-ip-preconfig.txt) | 인터페이스 IP 할당 (WAN/LAN) |
| **2** | [`02-loopback0-preconfig`](preconfig/02-loopback0-preconfig.txt) | 라우터 식별용 Loopback 0 설정 |
| **3** | [`03-loopback1-2-preconfig`](preconfig/03-loopback1-2-preconfig.txt) | 131.116.x.0/24 대역 광고용 Loopback |
| **4** | [`04-eigrp-routing-preconfig`](preconfig/04-eigrp-routing-preconfig.txt) | EIGRP AS 100 라우팅 설정 |
| **5** | [`05-eigrp-ip-summary-preconfig`](preconfig/05-eigrp-ip-summary-preconfig.txt) | 주소 요약 (ISP-1, ISP-5에서 /21) |
| **6** | [`verification-commands.md`](connection-check/EIGRP-PreConfig-Check.md) | 통신 확인 및 검증 |

## 🔧 주요 설정 내용

### EIGRP 기본 설정 패턴

```
router eigrp 100
 no auto-summary
 passive-interface default
 no passive-interface <업데이트 필요한 인터페이스>
 network <연결망> <wildcard>
```

### 주소 요약 (Route Summarization)

ISP-1(s1/1), ISP-5(s1/0)에서 `131.116.0.0/21`로 요약하여 GIT, R1에 단일 경로로 광고:

```
interface s1/1
 ip summary-address eigrp 100 131.116.0.0 255.255.248.0
```

> 💡 **/21 마스크 적용 범위**: `131.116.0.0` ~ `131.116.7.255` (0, 1, 3, 4, 5, 6, 7 대역 모두 포함)

## 📚 EIGRP 이론 (docs/)

프로젝트 진행 과정에서 정리한 EIGRP 핵심 이론 문서입니다.

| 문서 | 내용 |
|------|------|
| [📘 01. EIGRP 이론](docs/01-eigrp-theory.md) | EIGRP 개요, Distance Vector + Link-State 특성, AS, AD, Metric, K-상수, Load Balancing |
| [📗 02. EIGRP 5가지 PDU](docs/02-eigrp-pdu.md) | Hello, Update, Query, Reply, Ack 동작 원리 및 IP 사용 방식 |
| [📕 03. DUAL 알고리즘](docs/03-dual-algorithm.md) | Successor, Feasible Successor, FD/AD 계산 및 DUAL 조건 |

### 🔑 EIGRP 핵심 요약

#### EIGRP 특성

- **Cisco 전용** Routing Protocol (1986년 IGRP 확장형)
- **Distance Vector이지만 Link-State처럼 동작** (하이브리드)
- **Classless**: SubnetMask 포함, VLSM/CIDR 지원
- **Protocol 번호 88번** 사용

#### AD (Administrative Distance)

| Protocol | AD 값 | 비고 |
|----------|-------|------|
| Static | 1 | |
| **EIGRP Summary** | **5** | 요약 경로 |
| **EIGRP Internal** | **90** | Bandwidth + Delay |
| OSPF | 110 | Bandwidth |
| RIP | 120 | Hop-count |
| **EIGRP External** | **170** | 재분배 경로 |

#### EIGRP Metric 공식

```
EIGRP Metric    = EIGRP Bandwidth + EIGRP Delay
EIGRP Bandwidth = (10^7 / 목적지까지 최소 Bandwidth) × 256
EIGRP Delay     = (목적지까지 Delay의 총 합 / 10) × 256
```

> **K-상수**: K1=1, K2=0, K3=1, K4=0, K5=0 → **Bandwidth + Delay만 사용**

#### EIGRP 3가지 Table

| Table | 역할 |
|-------|------|
| **Neighbor Table** | EIGRP 인접성 목록 |
| **Topology Table** | 모든 네트워크 정보 + 통신 가능 경로 |
| **Routing Table** | 최적 경로만 등록 |

#### DUAL 알고리즘 핵심

- **FD (Feasible Distance)**: 출발지부터 목적지까지 총 거리 → **Routing Table 등록값**
- **AD (Advertise Distance)**: Next-hop 제외 나머지 거리 → **DUAL 계산 시에만 사용**
- **Successor**: 가장 작은 FD값을 가진 **최적 경로**
- **Feasible Successor**: `Successor의 FD > 후보 AD`인 **차선 경로**

## ✅ 검증 결과

### 📍 GIT 라우터 — 요약된 단일 경로 확인

```
GIT# show ip route eigrp
     131.116.0.0/21 is subnetted, 1 subnets
D       131.116.0.0 [90/40665600] via 121.160.11.66, 00:00:14, Serial1/0
```

### 📍 R1 라우터 — 요약된 단일 경로 확인

```
R1# show ip route eigrp
     131.116.0.0/21 is subnetted, 1 subnets
D       131.116.0.0 [90/40665600] via 121.160.11.97, 00:01:33, Serial1/1
```

### 📍 EIGRP Neighbor 정상 형성

총 10대 라우터에서 인접 라우터와 정상 neighbor 관계 형성 완료.

📝 **상세 검증 자료**:
- [🔍 검증 명령어 가이드](connection-check/verification-commands.md)
- [📊 실제 검증 결과](connection-check/verification-command-preconfig)

## 🔍 주요 검증 명령어

```
show ip interface brief         # 인터페이스 상태
show ip eigrp neighbors         # EIGRP 인접 관계 확인
show ip route eigrp             # EIGRP 라우팅 테이블
show ip protocols               # EIGRP 프로토콜 상세 정보
show ip eigrp topology          # Topology Table (DUAL 확인)
show ip eigrp interfaces        # EIGRP 활성 인터페이스
debug eigrp packets             # EIGRP 패킷 디버그
```

## 💡 배운 점

- **Passive Interface Default 전략**의 효율성: 기본적으로 모든 인터페이스를 passive로 설정한 후 필요한 인터페이스만 활성화하여 불필요한 EIGRP Hello 패킷 송신 방지
- **Manual Summarization**을 통한 라우팅 테이블 크기 감소 및 수렴 시간 단축
- **VLSM**을 활용한 효율적인 IP 주소 설계 (/30 서브넷으로 WAN 링크 구성)
- **DUAL 알고리즘**의 빠른 수렴 특성: Feasible Successor를 미리 선출하여 장애 시 즉시 전환
- ISP 백본망에서 **다중 경로(redundancy) 구성**의 중요성
- **EIGRP PDU 동작 원리** 이해를 통한 트러블슈팅 능력 향상

## 🛠️ 사용 도구

| 도구 | 용도 |
|------|------|
| **GNS3** | 네트워크 시뮬레이터 |
| **Cisco IOS (c3745)** | 라우터 운영체제 |
| **Cisco CLI** | 라우터 설정 명령어 입력 |
| **MobaXterm** | 콘솔 접속 터미널 |
| **Git / GitHub** | 형상 관리 및 포트폴리오 |


## 📜 라이선스

이 프로젝트는 [MIT License](LICENSE)를 따릅니다.

---

<div align="center">

**작성자**: [KSNAM97](https://github.com/KSNAM97)  
**작성일**: 2026.06  
**프로젝트 유형**: 네트워크 엔지니어링 포트폴리오

⭐ 도움이 되셨다면 Star를 눌러주세요!

</div>
