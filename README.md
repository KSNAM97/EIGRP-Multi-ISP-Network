# EIGRP-Multi-ISP-Network
EIGRP 기반 Multi-ISP 백본 네트워크 구축 및 Route Summarization 
# EIGRP 기반 Multi-ISP 네트워크 구축 프로젝트

GNS3 / Cisco IOS 기반으로 7개 ISP 라우터와 3개 고객사 라우터(GIT, R1, R2)를 EIGRP로 연동하고, 주소 요약(Route Summarization)을 통해 라우팅 테이블을 최적화한 프로젝트입니다.

## 📌 프로젝트 개요

- **사용 프로토콜**: EIGRP (AS 100)
- **라우터 수**: 10대 (ISP 7대 + GIT, R1, R2)
- **주요 기술**: VLSM, Route Summarization, Passive Interface, Loopback 광고
- **시뮬레이터**: GNS3 (Cisco IOS)

## 🗺️ 네트워크 토폴로지

![Topology](topology/topology.png)

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
 ip summary-address<img width="2085" height="959" alt="EIGRP_ISP" src="https://github.com/user-attachments/assets/1047e6a2-1030-4b56-97cf-cefb6c2cae46" />
 eigrp 100 131.116.0.0 255.255.248.0
```

## ✅ 검증 결과

### GIT 라우터 - 요약된 단일 경로 확인
```
131.116.0.0/21 is subnetted, 1 subnets
D  131.116.0.0 [90/40665600] via 121.160.11.66, Serial1/0
```

### R1 라우터 - 요약된 단일 경로 확인
```
131.116.0.0/21 is subnetted, 1 subnets
D  131.116.0.0 [90/40665600] via 121.160.11.97, Serial1/1
```

### EIGRP Neighbor 정상 형성
모든 라우터에서 인접 라우터와 정상 neighbor 관계 형성 완료.

## 📂 디렉토리 설명

- `configs/`: 각 라우터별 전체 설정 파일
- `verification/`: show 명령 결과 및 ping 테스트 결과
- `docs/`: IP 주소 설계 문서 및 설계 노트
- `topology/`: 네트워크 토폴로지 이미지

## 💡 배운 점

- **Passive Interface Default 전략**의 효율성: 기본적으로 모든 인터페이스를 passive로 설정한 후 필요한 인터페이스만 활성화하여 불필요한 EIGRP Hello 패킷 송신 방지
- **Manual Summarization**을 통한 라우팅 테이블 크기 감소 및 수렴 시간 단축
- **VLSM**을 활용한 효율적인 IP 주소 설계 (/30 서브넷으로 WAN 링크 구성)
- ISP 백본망에서 다중 경로(redundancy) 구성 시 EIGRP의 빠른 수렴 특성

## 🛠️ 사용 도구

- GNS3
- Cisco IOS (c7200)
- VS Code

---
작성자: [본인 이름]  
작성일: 2026.06
