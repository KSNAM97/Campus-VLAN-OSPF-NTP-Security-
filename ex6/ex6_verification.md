# ✅ EX6 검증 — OSPF (Process 100 / Area 0)

## 공통 (R1, R2, SW1, SW3)
```text
show ip ospf neighbor
show ip ospf interface brief
show ip ospf
show ip route ospf
show ip protocols
```

## R1
```text
show ip ospf neighbor
show ip ospf interface g1/0
show ip route ospf
ping 200.20.2.2 source 100.10.1.1
ping 211.241.228.2 source 100.10.1.1
```

## SW1
```text
show ip ospf neighbor
show ip ospf interface brief
show ip route ospf
```

## SW3
```text
show ip ospf neighbor
show ip ospf interface brief
show ip route ospf
```

## R2
```text
show ip ospf neighbor
show ip route ospf
ping 100.10.1.1 source 200.20.2.2
```

## End-to-End (PC)
```text
! PC1 (13.13.100.1)
ping 100.10.1.1
ping 200.20.2.2
ping 211.241.228.2
```

## 🎯 합격 기준
- 모든 OSPF Neighbor State가 **FULL**
- R1↔SW1, SW1↔SW3, SW3↔R2 총 **3쌍** Neighbor
- Router-ID가 명명규칙대로 (R=13.13.XX.XX, SW=13.13.X.X)
- `show ip route ospf` 에서 **O(OSPF)** 경로로 원격 네트워크 학습
- R1↔R2 Loopback ping 성공
