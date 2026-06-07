# EIGRP 검증 명령어 모음

## 📋 기본 확인 명령어

### 1. 인터페이스 IP 확인
```
show ip interface brief
```

### 2. EIGRP Neighbor 확인
```
show ip eigrp neighbors
```
**확인 항목**: 인접성 형성된 라우터 목록, Hold time, Uptime

### 3. EIGRP 라우팅 테이블 확인
```
show ip route eigrp
```
**확인 항목**: EIGRP로 학습된 경로, Metric, Next-hop

### 4. 전체 라우팅 테이블 확인
```
show ip route
```

### 5. EIGRP 프로토콜 정보 확인
```
show ip protocols
```
**확인 항목**: AS번호, K값, Auto-summary 여부, Passive Interface, 광고 네트워크

### 6. EIGRP Topology Table 확인
```
show ip eigrp topology
```
**확인 항목**: Successor, Feasible Successor, FD, AD 값

### 7. EIGRP 인터페이스별 상세 정보
```
show ip eigrp interfaces
show ip eigrp interfaces detail
```

### 8. EIGRP 트래픽 통계
```
show ip eigrp traffic
```

## 🔍 DUAL 알고리즘 검증

### Topology Table에서 FD/AD 확인
```
show ip eigrp topology all-links
```

### 특정 네트워크의 DUAL 정보
```
show ip eigrp topology 131.116.0.0/21
```

## 🐛 디버그 명령어

### EIGRP 패킷 디버그
```
debug eigrp packets                    ← 모든 패킷
debug eigrp packets hello              ← Hello만
debug eigrp packets update             ← Update만
debug eigrp packets query reply ack    ← Query/Reply/Ack
```

### EIGRP Neighbor 변화 디버그
```
debug eigrp neighbor
```

### DUAL 알고리즘 디버그
```
debug eigrp fsm
```

> ⚠️ **주의**: 디버그는 CPU 부하를 일으키므로 운영 환경에서는 신중히 사용.
> 디버그 종료: `undebug all` 또는 `no debug all`

## 🧪 통신 확인 (Ping / Traceroute)

### 기본 Ping
```
ping 131.116.0.2
```

### Source 지정 Ping (Loopback에서 출발)
```
ping 131.116.7.6 source 168.110.21.62
ping 22.22.22.22 source 123.123.123.123
```

### Extended Ping
```
ping
Protocol [ip]: 
Target IP address: 131.116.0.2
Repeat count [5]: 100
Datagram size [100]: 1500
Source address or interface: 168.110.21.62
```

### Traceroute (경로 추적)
```
traceroute 131.116.7.6
traceroute 22.22.22.22 source 168.110.21.62
```

## ✅ 본 프로젝트 검증 결과

### GIT 라우터 - 요약 경로 확인
```
GIT# show ip route eigrp
     131.116.0.0/21 is subnetted, 1 subnets
D       131.116.0.0 [90/40665600] via 121.160.11.66, 00:00:14, Serial1/0
```

### R1 라우터 - 요약 경로 확인
```
R1# show ip route eigrp
     131.116.0.0/21 is subnetted, 1 subnets
D       131.116.0.0 [90/40665600] via 121.160.11.97, 00:01:33, Serial1/1
```

### EIGRP Neighbor 형성 확인 (예: ISP-1)
```
ISP-1# show ip eigrp neighbors
IP-EIGRP neighbors for process 100
H   Address          Interface     Hold Uptime   SRTT   RTO  Q  Seq
                                   (sec)         (ms)       Cnt Num
2   121.160.11.94    Se1/0          11  00:01:27   27  1140  0  39
1   121.160.11.65    Se1/1           9  00:01:30   51  2280  0  26
0   121.160.11.70    Fa0/0          14  00:01:34   54   324  0  58
```

### EIGRP 프로토콜 정보 (예: ISP-1)
```
ISP-1# show ip protocols
Routing Protocol is "eigrp 100"
  Automatic network summarization is not in effect
  Address Summarization:
    131.116.0.0/21 for Serial1/1
      Summarizing with metric 409600
  Maximum path: 4
  Routing for Networks:
    101.101.101.0/24
    121.160.11.64/30
    121.160.11.68/30
    121.160.11.92/30
  Passive Interface(s):
    FastEthernet0/1
    Serial1/2
```
