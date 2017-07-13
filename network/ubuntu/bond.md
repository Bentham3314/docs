# Ubuntu bonding
LACP

- /etc/modules

```
loop
lp
rtc
bonding
```

- /etc/network/interfaces.d/ifcfg-eth0

```
auto eth0
iface eth0 inet manual
bond-master bond0
```

- /etc/network/interfaces.d/ifcfg-eth1

```
auto eth1
iface eth1 inet manual
bond-master bond0
```

- /etc/network/interfaces.d/ifcfg-bond0

```
auto bond0
iface bond0 inet static
address x.x.x.x
gateway x.x.x.x
netmask x.x.x.x

bond-mode 4
bond-miimon 100
bond-lacp-rate 1 # 0 = slow / 1 = fast
bond-slaves eth0 eth1
```

```
# service networking restart
```


```
# cat /proc/net/bonding/bond0
# ethtool bond0   ### 2000Mb/s
```

