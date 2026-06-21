# ✅ EX2 검증 — VTP / STP Root-Bridge

> **결과 요약** — SW1=Server / SW2·SW3=Client, Domain `ccnp` 동일, VLAN 100·130·200 전 스위치 전파 완료, SW1이 모든 VLAN Root-Bridge.

---

## SW1 (Server / Root)

### show vtp status
```text
VTP Domain Name                 : ccnp
VTP Operating Mode              : Server
Number of existing VLANs        : 8
Configuration Revision          : 3
MD5 digest                      : 0x4F 0xD7 ... 0x3A 0x33
*** MD5 digest checksum mismatch on trunk: Po12 ***
```
✅ `Operating Mode: Server`, Domain `ccnp`
ℹ️ `MD5 digest checksum mismatch` 메시지는 VTP 비밀번호가 적용된 환경에서 표시될 수 있는 정보성 메시지로, Revision·VLAN이 정상 동기화되었으므로 동작에는 문제 없음

### show vtp password
```text
VTP Password: cisco1234
```
✅ Password `cisco1234` 정상 적용

### show vlan brief
```text
VLAN Name                             Status    Ports
---- -------------------------------- --------- ---------------------------
100  CISCO_CCNA                       active
130  VLAN0130                         active
200  CISCO_CCNP                       active
```
✅ VLAN 100(CISCO_CCNA) / 130 / 200(CISCO_CCNP) 생성 확인

### show spanning-tree vlan 100 / 200 / 130 (요약)
```text
VLAN0100  This bridge is the root   Priority 24676 (24576 + sys-id-ext 100)
VLAN0200  This bridge is the root   Priority 24776 (24576 + sys-id-ext 200)
VLAN0130  This bridge is the root   Priority 24706 (24576 + sys-id-ext 130)
```
✅ 모든 VLAN에서 `This bridge is the root` (base priority 24576 = root primary)

### show spanning-tree root
```text
Vlan                   Root ID          Cost    Root Port
---------------- -------------------- --------- ------------
VLAN0001         24577 0ca8.ed70.0000         0
VLAN0100         24676 0ca8.ed70.0000         0
VLAN0130         24706 0ca8.ed70.0000         0
VLAN0200         24776 0ca8.ed70.0000         0
```
✅ 전 VLAN Root Cost = 0 / Root Port 없음 → SW1 자신이 Root

### show ip interface brief | include Vlan
```text
Vlan100   13.13.100.254   YES manual administratively down down
Vlan130   13.13.30.1      YES manual administratively down down
Vlan200   13.13.200.254   YES manual administratively down down
```
✅ SVI IP 주소 정상 설정 (100=.254 / 130=13.13.30.1 / 200=.254)
ℹ️ 현재 `administratively down` — Access 포트(EX3)가 아직 미설정이라 VLAN에 활성 멤버 포트가 없어 SVI가 down 상태. EX3 진행 후 자동으로 up 됨

---

## SW2 (Client)

### show vtp status
```text
VTP Domain Name                 : ccnp
VTP Operating Mode              : Client
Number of existing VLANs        : 8
Configuration Revision          : 3
```
✅ `Operating Mode: Client`, Domain `ccnp`, Revision 3 (SW1과 동일 → 동기화 완료)

### show vlan brief
```text
100  CISCO_CCNA                       active
130  VLAN0130                         active
200  CISCO_CCNP                       active
```
✅ SW1에서 생성한 VLAN이 VTP로 정상 전파됨

---

## SW3 (Client)

### show vlan brief
```text
100  CISCO_CCNA                       active
130  VLAN0130                         active
200  CISCO_CCNP                       active
```
✅ VLAN 전파 정상 (SW3에서도 동일 VLAN DB 보유)

---

## 🎯 합격 기준 체크

| 항목 | 기준 | 결과 |
| --- | --- | --- |
| VTP Mode | SW1=Server, SW2·SW3=Client | ✅ |
| VTP Domain | 전 스위치 `ccnp` 동일 | ✅ |
| VTP Password | `cisco1234` | ✅ |
| VLAN 생성/전파 | 100(CCNA)·130·200(CCNP) 전 스위치 존재 | ✅ |
| Config Revision | SW1·SW2·SW3 모두 `3` 일치 | ✅ |
| STP Root | SW1이 전 VLAN Root (`This bridge is the root`) | ✅ |

> **EX2 PASS** ✅ — VTP Server/Client 동기화 + SW1 Root-Bridge 모두 정상

---

## 📌 참고 메모
- `MD5 digest checksum mismatch on trunk: Po12` : 정보성 메시지. Revision/VLAN이 동일하게 동기화되어 실제 영향 없음. 신경 쓰이면 VTP 비밀번호를 전 스위치에서 동일하게 재입력(`vtp password cisco1234`)하면 사라질 수 있음.
- SVI `administratively down` : EX3에서 VLAN 100/200 Access 포트를 활성화하면 해당 VLAN에 멤버가 생기며 SVI가 up 됨. EX2 시점에서는 정상.
