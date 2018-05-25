# filter base fowarding

FBF

一部の経路のnext-hopだけ曲げたい時用(主にテスト用)

## Env:
model: Juniper EX4200  
OS   : Junos 12.3R6.6

[TechLibrary](https://www.juniper.net/documentation/en_US/junos/topics/example/filter-based-forwarding-example.html)


## Overview:

Quick Configuration

```
set firewall family inet filter TEST_FBF term one from source-address A.A.A.A/32
set firewall family inet filter TEST_FBF term one then count FBF_COUNTER
set firewall family inet filter TEST_FBF term one then log
set firewall family inet filter TEST_FBF term one then routing-instance TEST_GW
set firewall family inet filter TEST_FBF term end then accept
set routing-instances TEST_GW instance-type forwarding
set routing-instances TEST_GW routing-options static route 0.0.0.0/0 next-hop B.B.B.B
set routing-options interface-routes rib-group inet fbf-group
set routing-options rib-groups fbf-group import-rib inet.0
set routing-options rib-groups fbf-group import-rib TEST_GW.inet.0
set interfaces vlan unit N family inet filter input TEST_FBF
set interfaces vlan unit N family inet address C.C.C.C/24
```

## config

- interface configuration  
input filterでのみ動作する(outputでは設定不可)

```
set interfaces vlan unit N family inet filter input TEST_FBF
set interfaces vlan unit N family inet address C.C.C.C/24
```

- firewall filter  
term end を忘れずに

```
set firewall family inet filter TEST_FBF term one from source-address A.A.A.A/32
set firewall family inet filter TEST_FBF term one then count FBF_COUNTER
set firewall family inet filter TEST_FBF term one then log
set firewall family inet filter TEST_FBF term one then routing-instance TEST_GW
set firewall family inet filter TEST_FBF term end then accept
```

- routing-instances  
type: forwarding [TechLibrary](https://www.juniper.net/documentation/en_US/junos/topics/reference/configuration-statement/instance-type-edit-routing-instances-vp.html)  

interfaceを関連付けない(VRF/virtual-router等はinstanceが稼働するinterfaceを指定する必要がある)routing-instance

```
set routing-instances TEST_GW instance-type forwarding
set routing-instances TEST_GW routing-options static route 0.0.0.0/0 next-hop B.B.B.B
```

- routing-options  

* fbf-groupを定義(inet.0とTEST_GW.inet.0から生成)  
* interface-routesを設定することでforwardingが有効になる  

```
set routing-options interface-routes rib-group inet fbf-group
set routing-options rib-groups fbf-group import-rib inet.0
set routing-options rib-groups fbf-group import-rib TEST_GW.inet.0
```
