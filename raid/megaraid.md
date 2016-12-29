# 富士通 megaraid

```
# /opt/MegaRAID/MegaCli/MegaCli64 -PDINFO -PHYSDRV[252:0] -aALL
# /opt/MegaRAID/MegaCli/MegaCli64 -PDINFO -PHYSDRV[252:1] -aALL
# /opt/MegaRAID/MegaCli/MegaCli64 -LDINFO -L0 -aALL
# /opt/MegaRAID/MegaCli/MegaCli64 -AdpBbuCmd -GetBbuStatus -aALL
# /opt/MegaRAID/MegaCli/MegaCli64 -FwTermLog -Dsply -aALL

# Rebuild 確認
# /opt/MegaRAID/MegaCli/MegaCli64 -PDRbld -ShowProg -PHYSDRV[252:1] -aALL
```

リビルド実行

```
# /opt/MegaRAID/MegaCli/MegaCli64 -PDRbld -Start -PhysDrv[252:0] -a0
```


---


備考

```
-a0 アダプタ番号
下記のAdapter 0 がそう。RAIDが複数組まれている場合は他の番号がつく。

# /opt/MegaRAID/MegaCli/MegaCli64 -LDINFO -L0 -a0


Adapter 0 -- Virtual Drive Information:
Virtual Disk: 0 (Target Id: 0)
```
