# Network config for CentOS7
nmcli環境下でのネットワーク設定

## env
CentOS7.3  
7.2と7.3ですでに動き違うとかいい加減すぎるでしょ…とは思う

`nmcli c` は設定ファイルが生成されるのみ  

### 従来のNIC名を使用
GRUB_CMDLINE_LINUX のオプションに下記設定でreboot

- /etc/default/grub

```
biosdevname=0 net.ifnames=0
```

```
# grub2-mkconfig -o /boot/grub2/grub.cfg
```

## 設定確認コマンド

```
    # ip a
    # ip r

    # nmcli c
    # nmcli d
    # nmcli d sh
    # nmcli d sh [eth]
```

## 新規アドレス設定

### 新規で1つ設定

```
    # nmcli c mod eth1 ipv4.method manual ip4 "[IP1]/[NetMask]" gw4 "[gwIP]"
    # nmcli c up eth1
```

- /etc/sysconfig/network-scripts/ifcfg-eth1

```
TYPE=Ethernet
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth1
UUID=5e24d6ef-2799-4266-b100-0b31ea054311
DEVICE=eth1
ONBOOT=yes
IPADDR=192.168.1.10
PREFIX=24
GATEWAY=192.168.1.1
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
```

### 変更  
7.2だとAliasとして追加される(ifcfg-eth1:1みたいなファイルができる)とか挙動違うので注意

```
    # nmcli c mod eth1 ipv4.method manual ip4 "[IP1]/[NetMask]" gw4 "[gwIP]"
    # nmcli c down eth1
    # nmcli c up eth1
```

既存にalias振ってある場合、aliasのIPがおそらく消える？

### alias

```
    # nmcli c mod eth1 ipv4.method manual ip4 "[IP1]/[NetMask], [IP2]/[NetMask]" gw4 "[gwIP]"
    # nmcli c up eth1
```

- /etc/sysconfig/network-scripts/ifcfg-eth1

```
TYPE=Ethernet
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth1
UUID=5e24d6ef-2799-4266-b100-0b31ea054311
DEVICE=eth1
ONBOOT=yes
IPADDR=192.168.1.10
PREFIX=24
IPADDR1=192.168.1.20
PREFIX1=24
GATEWAY=192.168.1.1
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
```

## bonding
LACP(802.3ad)の設定(旧mode4)、ググってもでなさすぎでしょ。  
`# modprobe bonding` とかやらなくても勝手にロードされた。

```
eth0 -|
      |-- bond0
eth1 -|
```

- bond0 作成

```
    # nmcli c add type bond ifname bond0 con-name bond0 mode 802.3ad
    # nmcli c mod bond0 ipv4.method manual ip4 "[IP1]/[NetMask]" gw4 "[gwIP]"
```

- slave作成

```
    # nmcli c add type bond-slave ifname eth0 master bond0
    # nmcli c add type bond-slave ifname eth1 master bond0
```

- up

```
    # nmcli c up bond-slave-eth0
    # nmcli c up bond-slave-eth1
    # nmcii c up bond0
```

- /etc/sysconfig/network-scripts/ifcfg-bond0

```
DEVICE=bond0
BONDING_OPTS=mode=802.3ad
TYPE=Bond
BONDING_MASTER=yes
BOOTPROTO=dhcp
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=bond0
UUID=fa70c818-c6ed-40d8-b59f-fc961d322b7e
ONBOOT=yes
```

- /etc/sysconfig/network-scripts/ifcfg-bond-slave-eth0

```
TYPE=Ethernet
NAME=bond-slave-eth0
UUID=7e0a3be1-d2a9-4fe2-a082-742c184ed5ad
DEVICE=eth0
ONBOOT=yes
MASTER=bond0
SLAVE=yes
```

- 解除

```
    # nmcli c del bond-slave-eth0
    # nmcli c del bond-slave-eth0
    # nmcli c del bond0
```


## bonding + vlan
8021qも特に何もせずinterfaceあげたらロードされてた。


```
eth0 -|
      |-- bond0 -- vlan10
eth1 -|
```

- bond0/slave作成

```
    # nmcli c add type bond ifname bond0 con-name bond0 mode 802.3ad
    # nmcli c mod bond0 ipv4.method disabled ipv6.method ignore
    # nmcli c add type bond-slave ifname eth0 master bond0
    # nmcli c add type bond-slave ifname eth1 master bond0
```

Vlan interface作成

```
    # nmcli c add type vlan ifname bond0.10 con-name bond0.10 dev bond0 id 10
    # nmcli c mod bond0.10 ipv4.method manual ip4 "[IP1]/[NetMask]" gw4 "[gwIP]"
    # nmcli c up bond-slave-eth0
    # nmcli c up bond-slave-eth1
    # nmcii c up bond0
    # nmcli c up bond0.10
```

-  /etc/sysconfig/network-scripts/ifcfg-bond0

```
DEVICE=bond0
TYPE=Bond
BONDING_MASTER=yes
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=no
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=bond0
UUID=1efa71c5-ad0c-4e63-8685-8d14045cb769
ONBOOT=yes
BONDING_OPTS=mode=802.3ad
```

- /etc/sysconfig/network-scripts/ifcfg-bond0.10

```
VLAN=yes
TYPE=Vlan
DEVICE=bond0.10
PHYSDEV=bond0
VLAN_ID=10
REORDER_HDR=yes
GVRP=no
MVRP=no
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=bond0.10
UUID=501b8c1a-624b-4ae3-9974-d96efe5f16f8
ONBOOT=yes
IPADDR=192.168.1.10
PREFIX=24
GATEWAY=192.168.1.1
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
```

### Bonding + VLAN + Bridge

KVM等でホストにtag(trunk)で引き込み、  
ゲストが接続するbridgeはuntagにする方法

```
eth2 -|                   |- br100 -- vm
      |-- bond0 -- vlan --|
eth3 -|                   |- br200 -- vm

```

- br[12]00を作成

```
    # nmcli c add type bridge ifname br100 con-name br100
    # nmcli c mod br100 bridge.stp no
    # nmcli c mod br100 ipv4.met manual ip4 "192.168.100.10/24" gw4 "192.168.100.1"
    # nmcli c down br100
    # nmcli c up br100

    # nmcli c add type bridge ifname br200 con-name br200
    # nmcli c mod br200 bridge.stp no
    # nmcli c mod br200 ipv4.met manual ip4 "192.168.200.10/24" gw4 "192.168.200.1"
    # nmcli c down br200
    # nmcli c up br200
```

- IPアドレスを持たないbond0を作成

```
    # nmcli c add type bond ifname bond0 con-name bond0 mode 802.3ad
    # nmcli c mod bond0 ipv4.method disabled ipv6.method ignore
    # nmcli c add type bond-slave ifname eth2 master bond0
    # nmcli c add type bond-slave ifname eth3 master bond0
```

- vlan[12]00を作成  
connection.(master|slave-type)でvlanに接続

```
    # nmcli c add type vlan ifname vlan100 con-name vlan100 dev bond0 id 100
    # nmcli c mod vlan100 connection.master br100 connection.slave-type bridge

    # nmcli c add type vlan ifname vlan200 con-name vlan200 dev bond0 id 200
    # nmcli c mod vlan200 connection.master br200 connection.slave-type bridge
```

### bridge

```
    # nmcli c add type bridge ifname br0 con-name br0
    # nmcli c mod br0 ipv4.met manual ip4 "192.168.0.10/24" gw4 "192.168.0.1"

    # nmcli con add type bridge-slave ifname eth0 master br0
    # nmcli con add type bridge-slave ifname eth1 master br0

    # brctl show
    # brctl showstp br0
```

### DNS/route

```
    # nmcli c mod eth1 ipv4.dns "[DNS_IP] [DNS_IP2]"
    # nmcli c mod eth1 ipv4.routes "[NETWORK_IP/MASK] [GW_IP]"
```

### del

```
    # nmcli d show
    # nmcli c del eth1
    # nmcli d show
```

### add

```
    # nmcli d show
    # nmcli c add type ethernet ifname eth1 con-name eth1
    # nmcli d show
```

### hostname

```
    # nmcli g hostname [hostname]
    # uname -a
    # cat /etc/hostname
```


## ip commands

```
    # ip a add 192.168.0.3/255.255.255.0 dev eth2
    # ip a show eth2

    # ip route add default via 192.168.0.254  ### add
    # ip route del default via 192.168.0.254  ### del

    # ip route add 192.168.233.0/24 via 10.0.1.254 dev eth2
```

```
    # ip -s l  ### err/drop count
```
