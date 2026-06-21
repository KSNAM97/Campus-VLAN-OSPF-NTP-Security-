# ✅ EX3 검증 — VLAN Access / PortFast

## SW1 / SW3 (공통)
```text
show vlan brief
show interfaces gi0/0 switchport
show interfaces status
show spanning-tree interface gi0/0 portfast
show spanning-tree interface gi0/1 portfast
show spanning-tree interface gi0/2 portfast
show spanning-tree interface gi0/3 portfast
show running-config interface gi0/0
```



## 🎯 합격 기준
- `show vlan brief` 에서 Gi0/0-0/1 은 VLAN 100, Gi0/2~0/3 은 VLAN 200
- 각 포트의 `Administrative Mode: static access`
- portfast 결과: **enabled**

