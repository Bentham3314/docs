# Bonding + Vlan + Bridge

ex):  
-  KVM HOST server

## sysconfig

- /etc/sysconfig/network 

```
NETWORKING=yes
NETWORKING_IPV6=no
HOSTNAME=
GATEWAY=
VLAN=yes
VLAN_NAME_TYPE=DEV_PLUS_VID_NO_PAD
```

|VLAN_NAME_TYPE|disp|
|:-:|:-:|
|VLAN_PLUS_VID|vlan0010|
|VLAN_PLUS_VID_NO_PAD|vlan10|
|DEV_PLUS_VID|eth0.0010|
|DEV_PLUS_VID_NO_PAD|eth0.10|

## modprove
load bonding module

- /etc/modprove.d/bonding.conf

```
alias ipv6 off
options ipv6 disable=1
alias bond0 bonding
options bonding max_bonds=1
```

## Network interface setting

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
mode=4: lacp

```
DEVICE=bond0
BOOTPROTO=none
ONBOOT=yes
BONDING_OPTS="mode=4 miimon=100"
```

- /etc/sysconfig/network-scripts/ifcfg-bond0.355  
355: vlan number 

```
DEVICE=bond0.355
ONBOOT=yes
BOOTPROTO=none
BRIDGE=br355
```

- /etc/sysconfig/network-scripts/ifcfg-br355  
355: vlan number 

```
DEVICE=br355
BOOTPROTO=static
ONBOOT=yes
TYPE=Bridge
IPADDR=
NETMASK=
```


## restart network

```
# service network restart
```

## check

```
# ip a
# lsmod | egrep '8021q|bond'
# cat /proc/net/bonding/bond0
# ethtool bond0
# brctl show
# netstat -nr

# ping [gw]
# ping 8.8.8.8
```
