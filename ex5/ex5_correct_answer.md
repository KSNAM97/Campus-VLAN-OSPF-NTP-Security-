# ✅ EX5 검증 — Loopback / Routed-Interface / SVI

> **결과 요약** — R1 Lo100 / R2 Lo200·Lo211 up, R1↔SW1(121.160.10.0/24)·R2↔SW3(121.160.20.0/24) Routed-Interface 정상, SVI Vlan130 양쪽 up. 직접 연결 구간 ping 전부 성공.

---

## R1

### show ip interface brief
```text
Interface             IP-Address      OK? Method Status      Protocol
GigabitEthernet1/0    121.160.10.1    YES manual up          up
Loopback100           100.10.1.1      YES manual up          up
```
✅ Lo100(100.10.1.1) up/up, g1/0(121.160.10.1) up/up

### show interfaces loopback 100
```text
Loopback100 is up, line protocol is up
  Internet address is 100.10.1.1/24
```
✅ Lo100 정상

### show interfaces g1/0
```text
GigabitEthernet1/0 is up, line protocol is up
  Internet address is 121.160.10.1/24
  Full-duplex, 1000Mb/s
```
✅ Routed g1/0 정상

### ping 121.160.10.2 (→ SW1)
```text
Sending 5, 100-byte ICMP Echos to 121.160.10.2:
.!!!!
Success rate is 80 percent (4/5)
```
✅ R1↔SW1 통신 성공 (첫 패킷 `.` 은 ARP 학습 지연, 정상)

---

## R2

### show interfaces loopback 200
```text
Loopback200 is up, line protocol is up
  Internet address is 200.20.2.2/24
```
✅ Lo200(200.20.2.2) 정상

### show interfaces loopback 211
```text
Loopback211 is up, line protocol is up
  Internet address is 211.241.228.2/24
```
✅ Lo211(211.241.228.2) 정상

### show interfaces g1/0
```text
GigabitEthernet1/0 is up, line protocol is up
  Internet address is 121.160.20.1/24
  Full-duplex, 1000Mb/s
```
✅ Routed g1/0(121.160.20.1) 정상

### ping 121.160.20.2 (→ SW3)
```text
Sending 5, 100-byte ICMP Echos to 121.160.20.2:
.!!!!
Success rate is 80 percent (4/5)
```
✅ R2↔SW3 통신 성공

---

## SW1

### show ip interface brief
```text
Interface             IP-Address      OK? Method Status      Protocol
GigabitEthernet2/3    121.160.10.2    YES manual up          up
Vlan100               13.13.100.254   YES manual up          up
Vlan130               13.13.30.1      YES manual up          up
Vlan200               13.13.200.254   YES manual up          up
```
✅ gi2/3(Routed, 121.160.10.2) up/up
✅ SVI Vlan100/130/200 모두 **up/up** (EX2 no shutdown + EX3 access 포트 활성 효과)

### show interfaces gi2/3 switchport
```text
Name: Gi2/3
Switchport: Disabled
```
✅ `Switchport: Disabled` → no switchport 적용 (Routed Port)

### show running-config interface gi2/3
```text
interface GigabitEthernet2/3
 no switchport
 ip address 121.160.10.2 255.255.255.0
 negotiation auto
```
✅ no switchport + IP 정상

### show running-config interface vlan 130
```text
interface Vlan130
 ip address 13.13.30.1 255.255.255.0
```
✅ SVI Vlan130 (13.13.30.1)

### ping 121.160.10.1 (→ R1)
```text
Sending 5, 100-byte ICMP Echos to 121.160.10.1:
!!!!!
Success rate is 100 percent (5/5)
```
✅ SW1↔R1 통신 100% 성공

---

## SW3

### show ip interface brief
```text
Interface             IP-Address      OK? Method Status      Protocol
GigabitEthernet2/3    121.160.20.2    YES manual up          up
Vlan130               13.13.30.2      YES manual up          up
```
✅ gi2/3(Routed, 121.160.20.2) up/up, SVI Vlan130(13.13.30.2) up/up

### show interfaces gi2/3 switchport
```text
Name: Gi2/3
Switchport: Disabled
```
✅ `Switchport: Disabled` → no switchport 적용 (Routed Port)

### show running-config interface gi2/3
```text
interface GigabitEthernet2/3
 no switchport
 ip address 121.160.20.2 255.255.255.0
 negotiation auto
```
✅ no switchport + IP 정상

### show running-config interface vlan 130
```text
interface Vlan130
 ip address 13.13.30.2 255.255.255.0
```
✅ SVI Vlan130 (13.13.30.2)

### ping 121.160.20.1 (→ R2)
```text
Sending 5, 100-byte ICMP Echos to 121.160.20.1:
!!!!!
Success rate is 100 percent (5/5)
```
✅ SW3↔R2 통신 100% 성공

---

## 🎯 합격 기준 체크

| 항목 | 기준 | 결과 |
| --- | --- | --- |
| R1 Lo100 | 100.10.1.1/24 up | ✅ |
| R2 Lo200 | 200.20.2.2/24 up | ✅ |
| R2 Lo211 | 211.241.228.2/24 up | ✅ |
| R1↔SW1 Routed | 121.160.10.0/24, gi2/3 no switchport | ✅ |
| R2↔SW3 Routed | 121.160.20.0/24, gi2/3 no switchport | ✅ |
| SVI Vlan130 | SW1=13.13.30.1, SW3=13.13.30.2 | ✅ |
| 인터페이스 상태 | 전부 up/up | ✅ |
| 직접 연결 ping | R1↔SW1, R2↔SW3 성공 | ✅ |

> **EX5 PASS** ✅ — Loopback · Routed-Interface · SVI · 직접 연결 통신 모두 정상

---

## 📌 참고 메모
- ping 첫 패킷의 `.`(R1/R2 → 스위치 방향)은 첫 ICMP 전송 시 ARP 해석 대기로 1개 누락된 것으로, 이후 4개 성공이면 통신 정상. 반대로 스위치 → 라우터 방향(SW1/SW3)은 ARP가 이미 학습되어 5/5(100%) 성공.
- SW1/SW3의 gi2/3는 `Switchport: Disabled` 한 줄만 출력되는 것이 정상(Routed Port는 switchport 속성이 없음).
- 아직 라우팅 프로토콜(OSPF) 미설정이라 SW1↔SW3 간(VLAN130 경유) 원격 대역 통신은 EX6 이후 확인. EX5에서는 **직접 연결 구간** 통신만 확인 대상.
