# lacp
リンクアグリゲーション(LAG)

- 参考  
http://www.juniper.net/documentation/en_US/junos15.1/topics/concept/interfaces-lag-overview.html#OYsQsQ9drzgEe7eV.97


### LACPのモード
- active  
LACPのネゴシエーション要求パケットを自ら送信する

- passive  
ネゴシエーション要求パケットは送信しないが、受信したら受け付ける

両端のポートが  
*Active同士*  
*どちらか一方がActive、もう一方がpassive*  
の時に成立する  

両端がpassiveである場合は両方がネゴシエーションしないために成立しない


### fast or slow
デフォルトでは毎秒LACPパケットを送信する  
slowである場合は30秒に1回送信する

## 設定例

LAGを含めるインターフェースの設定
```
# set interfaces ge-0/0/2 ether-options ether-options 802.3ad ae0
# set interfaces ge-0/0/3 ether-options ether-options 802.3ad ae0
```

LACPの設定(activeモード/periodic fast)
```
# set interfaces ae0 aggregated-ether-options lacp active periodic fast
```

LAGインターフェースをすべてのVLANに転送するレイヤー2トランクポートとして設定
```
# set interfaces ae0 unit 0 family ethernet-switching port-mode trunk vlan members all
```

確認
```
> show lacp interfaces ae0
```
