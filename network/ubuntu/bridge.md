# Ubuntu KVM Bridge network

## Env:
Ubuntu 16.04 LTS

```
       |          |
       |          |
   +---+--+   +---+--+
 +-| eth2 |---| eth3 |--+
 | +---+--+   +---+--+  |
 |     |          |     |
 | +---+---+  +-------+ |
 | | br100 |  | br200 | |
 | +---+---+  +---+---+ |
 |     |          |     |
 |     |          |     |
 | +---+--+   +------+  |
 | | vm01 |   | vm02 |  |
 | +------+   +------+  |
 +----------------------+
```


- /etc/network/interfaces.d/ifcfg-eth2

```
auto eth2
iface eth2 inet manual
```

- /etc/network/interfaces.d/ifcfg-eth3

```
auto eth3
iface eth3 inet manual
```

- brctl

```
# brctl addbr br100
# brctl addif br100 eth2

# brctl addbr br200
# brctl addif br200 eth3
```

- ifup

```
# ifup eth2
# ifup eth3

# ip link set dev br100 up
# ip link set dev br200 up
```
