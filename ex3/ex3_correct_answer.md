# ✅ EX3 검증 — VLAN Access / PortFast

> **결과 요약** — SW1·SW3의 Gi0/0-0/1=VLAN100, Gi0/2-0/3=VLAN200 Access 배치 완료. 4개 포트 모두 PortFast(edge) 활성, 포트 즉시 Forwarding.

---

## SW1

### show vlan brief
```text
100  CISCO_CCNA                       active    Gi0/0, Gi0/1
130  VLAN0130                         active
200  CISCO_CCNP                       active    Gi0/2, Gi0/3
```
✅ Gi0/0~0/1 → VLAN 100, Gi0/2~0/3 → VLAN 200 정상 배치

### show interfaces gi0/0 switchport
```text
Name: Gi0/0
Administrative Mode: static access
Operational Mode: static access
Access Mode VLAN: 100 (CISCO_CCNA)
```
✅ `static access` / Access VLAN 100

### show interfaces status
```text
Port      Status       Vlan       Duplex  Speed Type
Gi0/0     connected    100        a-full   auto RJ45
Gi0/1     connected    100        a-full   auto RJ45
Gi0/2     connected    200        a-full   auto RJ45
Gi0/3     connected    200        a-full   auto RJ45
Po12      connected    trunk      a-full   auto
```
✅ PC 포트 4개 모두 `connected`, VLAN 100/200 정상

### show spanning-tree interface portfast (gi0/0~0/3)
```text
gi0/0   VLAN0100   enabled
gi0/1   VLAN0100   enabled
gi0/2   VLAN0200   enabled
gi0/3   VLAN0200   enabled
```
✅ 4개 포트 모두 PortFast `enabled`

### show running-config interface gi0/0
```text
interface GigabitEthernet0/0
 switchport access vlan 100
 switchport mode access
 negotiation auto
 spanning-tree portfast edge
```
✅ `spanning-tree portfast edge` 적용 확인

---

## SW3

### show vlan brief
```text
100  CISCO_CCNA                       active    Gi0/0, Gi0/1
130  VLAN0130                         active
200  CISCO_CCNP                       active    Gi0/2, Gi0/3
```
✅ Gi0/0~0/1 → VLAN 100, Gi0/2~0/3 → VLAN 200 정상 배치

### show interfaces gi0/0 switchport
```text
Name: Gi0/0
Administrative Mode: static access
Operational Mode: static access
Access Mode VLAN: 100 (CISCO_CCNA)
```
✅ `static access` / Access VLAN 100

### show interfaces status
```text
Port      Status       Vlan       Duplex  Speed Type
Gi0/0     connected    100        a-full   auto RJ45
Gi0/1     connected    100        a-full   auto RJ45
Gi0/2     connected    200        a-full   auto RJ45
Gi0/3     connected    200        a-full   auto RJ45
Po23      connected    trunk      a-full   auto
```
✅ PC 포트 4개 모두 `connected`, VLAN 100/200 정상

### show spanning-tree interface portfast (gi0/0~0/3)
```text
gi0/0   VLAN0100   enabled
gi0/1   VLAN0100   enabled
gi0/2   VLAN0200   enabled
gi0/3   VLAN0200   enabled
```
✅ 4개 포트 모두 PortFast `enabled`

### show running-config interface gi0/0
```text
interface GigabitEthernet0/0
 switchport access vlan 100
 switchport mode access
 negotiation auto
 spanning-tree portfast edge
```
✅ `spanning-tree portfast edge` 적용 확인

---

## 🎯 합격 기준 체크

| 항목 | 기준 | 결과 |
| --- | --- | --- |
| VLAN 100 Access | Gi0/0~0/1 (SW1·SW3) | ✅ |
| VLAN 200 Access | Gi0/2~0/3 (SW1·SW3) | ✅ |
| Operational Mode | `static access` | ✅ |
| 포트 상태 | 4개 포트 `connected` | ✅ |
| PortFast | gi0/0~0/3 모두 `enabled` (portfast edge) | ✅ |

> **EX3 PASS** ✅ — SW1·SW3 VLAN 100/200 Access 배치 + PortFast 즉시 Forwarding 모두 정상

---

## 📌 참고 메모
- 이제 VLAN 100/200에 활성 멤버 포트(connected)가 생겼으므로, EX2에서 `administratively down` 이던 SW1의 SVI(Vlan100/200)도 Access 포트 활성화 + `no shutdown` 조건이 맞으면 up 됩니다. EX4(DHCP)·EX6(OSPF) 진행 전 `show ip interface brief | include Vlan` 로 SVI가 `up/up` 인지 한 번 확인하면 좋습니다.
- `portfast edge` 표기는 최신 IOS에서 PortFast의 정상 표기입니다(기존 `portfast` 와 동일 동작).
