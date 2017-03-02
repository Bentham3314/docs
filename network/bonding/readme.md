# Linux bonding

## 手順
1. bondingモジュールのロード
2. interface作成
3. network restart

## 1. bondingモジュールのロード
8021qはtagVlan用なのでbondingオンリーの設定では不要

```
# vi /etc/modprobe.d/bonding.conf
---
alias ipv6 off
options ipv6 disable=1
alias bond0 bonding
options bonding max_bonds=1
---

# modprobe bonding
# lsmod | grep bonding
```

## 2. interface作成
viで適当に作成

- /etc/sysconfig/network-scripts/ifcfg-eth0

```
DEVICE=eth0
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
```

- /etc/sysconfig/network-scripts/ifcfg-eth1

```
DEVICE=eth1
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
```

- /etc/sysconfig/network-scripts/ifcfg-bond0

```
DEVICE=bond0
ONBOOT=yes
BONDING_OPTS="mode=4 miimon=100"
BOOTPROTO=static
Type=Ethernet
IPADDR=
NETMASK=
```

オプション  
- BONDING_OPTS="mode=4 miimon=100"  
lacp使用/NICを両方使用 (1gNICなら2Gになる)  
バランシングは適当なので宛先によっては偏るので注意
