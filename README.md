# EIGRP 기반 Multi-ISP 네트워크 구축 프로젝트

> EIGRP 기반 Multi-ISP 백본 네트워크 구축 및 Route Summarization 실습 (Cisco IOS / GNS3)

GNS3 / Cisco IOS 기반으로 7개 ISP 라우터와 3개 고객사 라우터(GIT, R1, R2)를 EIGRP로 연동하고, 주소 요약(Route Summarization)을 통해 라우팅 테이블을 최적화한 프로젝트입니다.

## 📌 프로젝트 개요

- **사용 프로토콜**: EIGRP (AS 100)
- **라우터 수**: 10대 (ISP 7대 + GIT, R1, R2)
- **주요 기술**: VLSM, Route Summarization, Passive Interface, Loopback 광고, DUAL 알고리즘
- **시뮬레이터**: GNS3 (Cisco IOS c7200)

## 🗺️ 네트워크 토폴로지

![EIGRP Multi-ISP Network Topology](topology/eigrp_isp_topology.png)

### 구성 요소
- **ISP 백본**: 7개 라우터 (ISP-1 ~ ISP-7) - 다중 경로 redundancy 구성
- **고객사 라우터**: 3개 (GIT, R1, R2)
- **WAN 링크**: HDLC 캡슐화 (64Kbps / 128Kbps)
- **LAN 구간**: GIT-SW, ISP-3-SW, ISP-6-SW, R1-SW, R2-SW

## 🎯 요구사항

1. 토폴로지에 따라 각 네트워크 구간에 IP 주소 할당
2. 각 라우터에 Loopback 0 생성 (식별용)
   - GIT: `123.123.123.123/24`
   - R1: `11.11.11.11/24`
   - R2: `22.22.22.22/24`
   - ISP-X: `101.101.10X.X/24`
3. ISP-2/3/4/6에 Loopback 1, 2 생성 (`131.116.X.0/24`, X = ISP 번호)
4. EIGRP AS 100 설정, `no auto-summary`, Passive Interface 적용
5. GIT 라우터와 R1 라우터에서 `131.116.0.0/21`로 요약된 단일 네트워크 확인
6. GIT 내부 단말에서 모든 `131.116.X.0/24` 네트워크와 통신 가능

## 📂 디렉토리 구조

```
EIGRP-Multi-ISP-Network/
├── topology/                          # 네트워크 토폴로지 다이어그램
│   └── eigrp_isp_topology.png
├── docs/                              # EIGRP 이론 정리 문서
│   ├── 01-eigrp-theory.md             # EIGRP 개요, AD/Metric, K-상수
│   ├── 02-eigrp-pdu.md                # 5가지 PDU (Hello/Update/Query/Reply/Ack)
│   └── 03-dual-algorithm.md           # DUAL 알고리즘 (Successor, FD/AD)
├── pre-config/                        # 라우터 설정 단계별 파일
│   ├── loopback0-preconfig            # 라우터 식별용 Loopback 설정
│   ├── router-ip-preconfig            # 인터페이스 IP 할당
│   ├── loopback1,2-preconfig          # 131.116.x.0/24 Loopback 설정
│   ├── eigrp-routing-preconfig        # EIGRP AS 100 라우팅 설정
│   └── eigrp-ip-summary               # 주소 요약 (Route Summarization)
├── connection-check/                  # 통신 확인 및 검증
│   ├── connection-preconfig           # 통신 확인 결과
│   ├── verification-commands.md       # 검증 명령어 모음
│   └── debug-results.md               # Debug 패킷 분석
├── LICENSE                            # MIT License
└── README.md
```

## 🔄 설정 순서

1. **Loopback 0 설정** → 각 라우터 식별
2. **인터페이스 IP 할당** → WAN/LAN 구간 IP 설정
3. **Loopback 1, 2 설정** → 131.116.x.0/24 대역 광고용
4. **EIGRP 라우팅 설정** → AS 100, no auto-summary, passive-interface
5. **주소 요약 설정** → ISP-1, ISP-5에서 /21로 요약
6. **통신 확인** → ping, show 명령으로 검증

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

## 📚 EIGRP 이론 (docs/)

프로젝트 진행 과정에서 정리한 EIGRP 핵심 이론 문서입니다.

| 문서 | 내용 |
|------|------|
| [01. EIGRP 이론](docs/01-eigrp-theory.md) | EIGRP 개요, Distance Vector + Link-State 특성, AS, AD, Metric, K-상수, Load Balancing |
| [02. EIGRP 5가지 PDU](docs/02-eigrp-pdu.md) | Hello, Update, Query, Reply, Ack 동작 원리 및 IP 사용 방식 |
| [03. DUAL 알고리즘](docs/03-dual-algorithm.md) | Successor, Feasible Successor, FD/AD 계산 및 DUAL 조건 |

### 핵심 이론 요약

**EIGRP 특성**
- Cisco 전용 Routing Protocol (1986년 IGRP 확장형)
- Distance Vector이지만 Link-State처럼 동작 (하이브리드)
- Classless: SubnetMask 포함, VLSM/CIDR 지원
- Protocol 번호 88번 사용

**AD (Administrative Distance)**

| Protocol | AD 값 |
|----------|-------|
| Static | 1 |
| EIGRP Summary | 5 |
| EIGRP Internal | 90 |
| OSPF | 110 |
| RIP | 120 |
| EIGRP External | 170 |

**EIGRP Metric**
```
EIGRP Metric = EIGRP Bandwidth + EIGRP Delay
EIGRP Bandwidth = (10^7 / 목적지까지 최소 Bandwidth) × 256
EIGRP Delay     = (목적지까지 Delay의 총 합 / 10) × 256
```
- K-상수: K1=1, K2=0, K3=1, K4=0, K5=0 (Bandwidth + Delay만 사용)

**EIGRP 3가지 Table**
- **Neighbor Table**: EIGRP 인접성 목록
- **Topology Table**: 모든 네트워크 정보 + 통신 가능 경로
- **Routing Table**: 최적 경로만 등록

**DUAL 알고리즘 핵심**
- **FD (Feasible Distance)**: 출발지부터 목적지까지 총 거리 → Routing Table 등록값
- **AD (Advertise Distance)**: Next-hop 제외 나머지 거리 → DUAL 계산 시에만 사용
- **Successor**: 가장 작은 FD값을 가진 최적 경로
- **Feasible Successor**: Successor의 FD > 후보 AD인 차선 경로

## ✅ 검증 결과

### GIT 라우터 - 요약된 단일 경로 확인

```
GIT# show ip route eigrp
     131.116.0.0/21 is subnetted, 1 subnets
D       131.116.0.0 [90/40665600] via 121.160.11.66, 00:00:14, Serial1/0
```

### R1 라우터 - 요약된 단일 경로 확인

```
R1# show ip route eigrp
     131.116.0.0/21 is subnetted, 1 subnets
D       131.116.0.0 [90/40665600] via 121.160.11.97, 00:01:33, Serial1/1
```

### EIGRP Neighbor 정상 형성

모든 라우터에서 인접 라우터와 정상 neighbor 관계 형성 완료. (총 10대 라우터)

📝 **상세 검증 명령어 및 Debug 결과**: 
- [검증 명령어 모음](connection-check/verification-commands.md)
- [Debug 결과 분석](connection-check/debug-results.md)

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

- **GNS3**: 네트워크 시뮬레이터
- **Cisco IOS**: c7200 라우터 이미지
- **VS Code**: 설정 파일 편집
- **Git/GitHub**: 형상 관리 및 포트폴리오

## 📜 라이선스

이 프로젝트는 [MIT License](LICENSE)를 따릅니다.

---

**작성자**: KSNAM97  
**작성일**: 2026.06  
**프로젝트 유형**: 네트워크 엔지니어링 포트폴리오
