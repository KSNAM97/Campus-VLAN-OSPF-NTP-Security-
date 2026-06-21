# ✅ EX4 검증 — DHCP (VLAN100 / VLAN200)

## SW1
```text
show ip dhcp pool
show ip dhcp binding
show ip dhcp server statistics
show ip dhcp conflict
show running-config | section dhcp
```

## PC (VPCS)
```text
ip dhcp
show ip
```

## 🎯 합격 기준
- `show ip dhcp pool` 에 **vlan100, vlan200** 두 풀 존재
- 각 풀의 Network, Mask, `lease 5 days` 확인
- PC가 `ip dhcp` 명령 후 **13.13.100.x** 또는 **13.13.200.x** IP를 받음
- Gateway가 **.254**, DNS가 **168.126.63.1** 로 설정
- `show ip dhcp binding` 에서 할당된 IP 목록 확인
