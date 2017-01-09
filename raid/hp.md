# raid周り


- status

```
# df -h > ./df-h
# fdisk -lu > ./fdisk-lu
# hpacucli ctrl all show config detail > ./config-detail
# hpacucli ctrl slot=0 ld all show > ./hpacucli-ld
# hpacucli ctrl slot=0 pd all show > ./hpacucli-pd
```

- rebuild check

```
# hpacucli ctrl slot=0 ld all show status
```


- check

```
# hpacucli ctrl all show config detail | diff ./config-detail -
# hpacucli ctrl slot=0 ld all show | diff ./hpacucli-ld -
# hpacucli ctrl slot=0 pd all show | diff ./hpacucli-pd -
```

---

- LED

```
# hpacucli ctrl slot=0 pd 1I:1:1 modify led=on
# hpacucli ctrl slot=0 pd 1I:1:1 modify led=off
```

---

- 略

```
ctrl = controller
pd = physicaldrive
ld = logicaldrive
```


- スロットナンバー確認  

```
# hpacucli ctrl all show config
```

- failしたディスク確認  

```
# hpacucli ctrl slot=0 pd 1I:1:1 show
```

- logicaldrive確認  

```
# hpacucli ctrl slot=0 ld all show detail
```

- 特定のlogicaldriveのみ  

```
# hpacucli ctrl slot=0 ld 13 show detail
```


---


### Rebuildが開始されない場合

```
# hpacucli ctrl slot=0 ld 2 modify reenable
```

---

### そもそもfaildしてないディスクを抜く時
やんごとなき理由(バックアップ用diskとか)でシングルボリュームを  
強引にRAID 0で認識させて使用している場合  
ディスクI/Oを発生させないため一度deleteし、OSから認識させなくする。  

I/Oが発生しない事が分かってるディスクならreenableで。

```
# hpacucli ctrl slot=0 ld all show
# hpacucli ctrl slot=0 array B delete
# hpacucli ctrl slot=0 ld all show
# hpacucli ctrl slot=0 pd all show

# hpacucli ctrl slot=0 ld all show
# hpacucli ctrl slot=0 create type=ld drives=1I:1:3 raid=0
# hpacucli ctrl slot=0 ld all show
```
