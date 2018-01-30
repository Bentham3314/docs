# CISCO

## IOS

### 初期化方法

- ルータ  

```
# erase startup-config
# show startup-config
# reload
```

- Catalystスイッチ  
Vlan情報はvlan.datに保存されているため消す必要がある

```
# erase startup-config
# show startup-config
# show flash:
# delete flash:vlan.dat
# show flash:
```
