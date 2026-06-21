# ✅ EX7 검증 — NTP (인증 / Stratum)

## R2 (NTP Master)
```text
show clock
show ntp status
show ntp associations
show ntp authentication-status
show running-config | include ntp
```

## R1 (NTP Client)
```text
show clock
show ntp status
show ntp associations detail
ping 121.160.20.2
```

## SW1 (NTP Client)
```text
show clock
show ntp status
show ntp associations detail
ping 121.160.20.2
```

## SW2 (NTP Client)
```text
show ip interface brief | include Vlan130
show ip default-gateway
ping 13.13.30.1
ping 121.160.20.2
show clock
show ntp status
show ntp associations detail
```

## 🎯 합격 기준
- R2: `Clock is synchronized, stratum 2, reference is .LOCL.`
- Client: `Clock is synchronized, stratum 3, reference is 121.160.20.2`
- `show ntp associations detail` 에 **authenticated** 표시
- `our_master`, `sane`, `valid` 모두 표시
- SW2는 사전에 `ping 121.160.20.2` 성공 필수

> ⏱️ NTP 초기 동기화에 **5~15분** 소요 — 즉시 확인 시 unsynchronized 보일 수 있음
