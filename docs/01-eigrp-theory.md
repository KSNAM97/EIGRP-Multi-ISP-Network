# EIGRP (Enhanced Interior Gateway Routing Protocol) 이론

## 📌 개요

EIGRP는 **1986년에 개발된 IGRP 프로토콜의 확장형**으로서 Cisco 전용 Routing Protocol이다.

> **AS Number(라우팅 도메인을 식별하는 그룹번호)**는 이웃 라우터와 일치해야 한다.

## 🔄 EIGRP의 하이브리드 특성

**Distance Vector이지만 Link-State처럼 동작**한다.

### Distance Vector 특징
- **Split-horizon 알고리즘**에 의해 Loop 방지
- **Auto-summary** command가 Default로 enable

### Link-State 특징
- 인접성(Adjacency)을 맺은 Router간 라우팅 업데이트 실시
- Link 변화 시 변화된 부분에 대해서 **부분적인 업데이트** 실시
- **네트워크에 변화가 없으면 업데이트를 실시하지 않음**

## 🌐 Classless 프로토콜

- 라우팅 업데이트 정보 안에 **SubnetMask 포함**
- Subnetting, VLSM을 사용하여 구축한 네트워크 환경에서 사용 가능
- **CIDR 기능 지원** (수동 요약 기능 지원)
- Class가 단절된 네트워크 환경에서 라우팅 업데이트 가능

## 🏢 AS (Autonomous System)

> ⚠️ BGP의 AS와는 다른 개념

- AS는 **16bit**로 구성 (AS 범위: **1~65535**)
- 하나의 관리자에 의해 동작하는 지역 또는 사설 Domain

## 📊 AD (Administrative Distance, 신뢰도)

하나의 Router에 복수개의 Routing Protocol을 설정하게 되면, Router는 **AD값이 가장 작은 Routing Protocol**을 사용한 라우팅 업데이트를 실시한다.

| Routing Protocol | AD 값 | 비고 |
|------------------|-------|------|
| **Connected** | **0** | 직접 연결된 인터페이스 |
| Static | 1 | 정적 경로 |
| **EIGRP Summary** | **5** | 요약 경로 |
| **EIGRP Internal** | **90** | Bandwidth + Delay |
| OSPF | 110 | Bandwidth |
| RIP | 120 | Hop-count (거리) |
| **EIGRP External** | **170** | 재분배 경로 |


## 📐 Metric (메트릭)

- Router는 하나의 목적지 네트워크에 대해서 **한 개의 경로만 지원** (최적 경로만)
- 복수개의 경로가 확인되면 **Metric값을 사용하여 최적 경로 선출**

### EIGRP Metric 공식

```
EIGRP Metric = EIGRP Bandwidth + EIGRP Delay
```

### EIGRP 5가지 속성값 (K-상수)

| K-상수 | 속성 | 기본값 | 사용 여부 |
|--------|------|--------|-----------|
| K1 | Bandwidth | 1 | ✅ 사용 |
| K2 | Load | 0 | ❌ (실시간 변동) |
| K3 | Delay | 1 | ✅ 사용 |
| K4 | Reliability | 0 | ❌ (실시간 변동) |
| K5 | MTU | 0 | ❌ (실시간 변동) |

> **기본은 K1(Bandwidth), K3(Delay)만 사용**. K2, K4, K5는 실시간으로 변하는 값이라 기본 비활성화.

### Metric 계산 공식

```
EIGRP Bandwidth = (10^7 / 목적지까지 최소의 Bandwidth) × 256
EIGRP Delay     = (목적지까지 Delay의 총 합 / 10) × 256
```

## ⚖️ Load Balancing (로드 분산)

- 기본적으로 Router는 최적 경로 1개만 사용
- **로드 분산**: 복수개의 경로를 지원하는 기능
- **균등 로드 분산**: 동일한 Metric값을 가진 경로에 대해 분산
- **비균등 로드 분산**: Metric값이 달라도 복수 경로 지원 (**EIGRP만 지원**)

| IOS 버전 | 분산 가능 경로 수 | 기본값 |
|----------|------------------|--------|
| IOS 12.2 | 1 ~ 6개 | 4개 |
| IOS 12.4 | 1 ~ 16개 | 4개 |

## 📡 EIGRP 라우팅 업데이트

- **Protocol 번호 88번** 예약 사용
- 출발지 IP: 송신 Interface의 IP
- 목적지 IP: PDU 및 Network Type에 따라 **Unicast / Multicast(224.0.0.10)** 병행 사용
