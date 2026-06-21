# ✅ EX6 검증 — OSPF (Process 100 / Area 0)

> **결과 요약** — Neighbor 3쌍(R1↔SW1, SW1↔SW3, SW3↔R2) 전부 FULL. 전 장비 O(OSPF) 경로로 원격 대역 학습. R1↔R2 Loopback ping 100% 성공. Router-ID 명명규칙 준수.

---

## R1 (RID 13.13.11.11)

### show ip ospf neighbor
```text
Neighbor ID     Pri   State           Dead Time   Address         Interface
13.13.1.1         1   FULL/BDR        00:00:36    121.160.10.2    GigabitEthernet1/0
```
✅ SW1(13.13.1.1)과 **FULL**

### show ip ospf interface brief
```text
Interface    PID   Area   IP Address/Mask    Cost  State Nbrs F/C
Lo100        100   0      100.10.1.1/24      1     LOOP  0/0
Gi1/0        100   0      121.160.10.1/24    1     DR    1/1
```
✅ Lo100 + g1/0 OSPF 포함, RID 13.13.11.11

### show ip route ospf
```text
O   211.241.228.2 [110/4] via 121.160.10.2, Gi1/0
O   200.20.2.2    [110/4] via 121.160.10.2, Gi1/0
O   13.13.30.0    [110/2] via 121.160.10.2, Gi1/0
O   13.13.100.0   [110/2] via 121.160.10.2, Gi1/0
O   13.13.200.0   [110/2] via 121.160.10.2, Gi1/0
O   121.160.20.0  [110/3] via 121.160.10.2, Gi1/0
```
✅ R2 Loopback(200/211) + 13.13.x + 121.160.20.0 전부 학습

### ping (source 100.10.1.1)
```text
ping 200.20.2.2 source 100.10.1.1     → Success 100% (5/5)
ping 211.241.228.2 source 100.10.1.1  → Success 100% (5/5)
```
✅ R1 Lo100 → R2 Lo200 / Lo211 통신 성공

---

## R2 (RID 13.13.22.22)

### show ip ospf neighbor
```text
Neighbor ID     Pri   State           Dead Time   Address         Interface
13.13.3.3         1   FULL/BDR        00:00:30    121.160.20.2    GigabitEthernet1/0
```
✅ SW3(13.13.3.3)과 **FULL**

### show ip ospf interface brief
```text
Interface    PID   Area   IP Address/Mask    Cost  State
Lo200        100   0      200.20.2.2/24      1     LOOP
Lo211        100   0      211.241.228.2/24   1     LOOP
Gi1/0        100   0      121.160.20.1/24    1     DR
```
✅ Lo200 + Lo211 + g1/0 OSPF 포함, RID 13.13.22.22

### show ip route ospf
```text
O   100.10.1.1   [110/4] via 121.160.20.2, Gi1/0
O   13.13.30.0   [110/2] via 121.160.20.2, Gi1/0
O   13.13.100.0  [110/3] via 121.160.20.2, Gi1/0
O   13.13.200.0  [110/3] via 121.160.20.2, Gi1/0
O   121.160.10.0 [110/3] via 121.160.20.2, Gi1/0
```
✅ R1 Lo100 + 13.13.x + 121.160.10.0 전부 학습

### ping (source 200.20.2.2)
```text
ping 100.10.1.1 source 200.20.2.2     → Success 100% (5/5)
```
✅ R2 Lo200 → R1 Lo100 통신 성공

---

## SW1 (RID 13.13.1.1)

### show ip ospf neighbor
```text
Neighbor ID     Pri   State           Dead Time   Address         Interface
13.13.11.11       1   FULL/DR         00:00:34    121.160.10.1    GigabitEthernet2/3
13.13.3.3         1   FULL/DR         00:00:36    13.13.30.2      Vlan130
```
✅ R1(13.13.11.11) **FULL** + SW3(13.13.3.3) **FULL** — 2 Neighbor

### show ip ospf interface brief
```text
Interface    PID   Area   IP Address/Mask    Cost  State Nbrs F/C
Gi2/3        100   0      121.160.10.2/24    1     BDR   1/1
Vl130        100   0      13.13.30.1/24      1     BDR   1/1
Vl200        100   0      13.13.200.254/24   1     DR    0/0
Vl100        100   0      13.13.100.254/24   1     DR    0/0
```
✅ Routed gi2/3 + SVI Vlan100/130/200 모두 OSPF 포함

### show ip route ospf
```text
O   100.10.1.1     [110/2] via 121.160.10.1, Gi2/3
O   121.160.20.0/24 [110/2] via 13.13.30.2, Vlan130
O   200.20.2.2     [110/3] via 13.13.30.2, Vlan130
O   211.241.228.2  [110/3] via 13.13.30.2, Vlan130
```
✅ R1 Lo100 + R2 대역 전부 학습 (Vlan130 경유)

---

## SW3 (RID 13.13.3.3)

### show ip ospf neighbor
```text
Neighbor ID     Pri   State           Dead Time   Address         Interface
13.13.22.22       1   FULL/DR         00:00:31    121.160.20.1    GigabitEthernet2/3
13.13.1.1         1   FULL/BDR        00:00:35    13.13.30.1      Vlan130
```
✅ R2(13.13.22.22) **FULL** + SW1(13.13.1.1) **FULL** — 2 Neighbor

### show ip ospf interface brief
```text
Interface    PID   Area   IP Address/Mask    Cost  State Nbrs F/C
Gi2/3        100   0      121.160.20.2/24    1     BDR   1/1
Vl130        100   0      13.13.30.2/24      1     DR    1/1
```
✅ Routed gi2/3 + SVI Vlan130 OSPF 포함

### show ip route ospf
```text
O   13.13.100.0/24 [110/2] via 13.13.30.1, Vlan130
O   13.13.200.0/24 [110/2] via 13.13.30.1, Vlan130
O   100.10.1.1     [110/3] via 13.13.30.1, Vlan130
O   121.160.10.0/24 [110/2] via 13.13.30.1, Vlan130
O   200.20.2.2     [110/2] via 121.160.20.1, Gi2/3
O   211.241.228.2  ...     via 121.160.20.1, Gi2/3
```
✅ SW1쪽 VLAN(100/200) + R1 Lo100 + R2 Loopback 전부 학습

---

## 🎯 합격 기준 체크

| 항목 | 기준 | 결과 |
| --- | --- | --- |
| Neighbor 상태 | 전부 FULL | ✅ |
| Neighbor 쌍 | R1↔SW1, SW1↔SW3, SW3↔R2 (3쌍) | ✅ |
| Router-ID | R=13.13.XX.XX(11/22), SW=13.13.X.X(1/3) | ✅ |
| OSPF 경로 학습 | 전 장비 O(OSPF) 원격 대역 학습 | ✅ |
| R1↔R2 Loopback | ping source 양방향 100% | ✅ |
| Process / Area | 100 / Area 0 단일 | ✅ |

> **EX6 PASS** ✅ — OSPF Neighbor 3쌍 FULL, 전 대역 학습, R1↔R2 Loopback 통신 정상

---

## 📌 참고 메모
- Neighbor 토폴로지: **R1 ⎯(121.160.10.0)⎯ SW1 ⎯(VLAN130 13.13.30.0)⎯ SW3 ⎯(121.160.20.0)⎯ R2** 일자형. SW1↔SW3 인접은 Vlan130(SVI)에서 형성됨.
- `auto-cost reference-bandwidth 1000` 적용으로 1G 링크 Cost=1. 경로 metric(예: R1→R2 Lo = 110/4)이 홉 수에 맞게 누적됨.
- SW1의 Vlan100/200은 `Nbrs F/C 0/0`(neighbor 없음) — 정상. 해당 SVI에는 PC만 있고 OSPF 라우터가 없어 인접이 안 생기는 것이 맞음. `passive-interface` 대신 network에 포함시켜 대역만 광고하는 형태.
- `show ip route ospf` 오타 주의: `how ip route ospf` 로 입력 시 `% Invalid input` — `show` 전체 입력 필요.
