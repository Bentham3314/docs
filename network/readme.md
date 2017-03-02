# Linux関係のnetwork系

## bonding
https://github.com/Bentham3314/memo/tree/master/network/bonding

## bonding + bridge
https://github.com/Bentham3314/memo/tree/master/network/bond_bridge

## bonding + vlan

## bonding + bridge + vlan


# IPを持つインターフェース
一番外側に設定されるインターフェースにIPアドレスを振る  

```
bonding -> bonding
bonding + bridge -> bridge
bonding + vlan -> vlan
bonding + bridge + vlan -> vlan
```

