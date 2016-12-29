# raid周り


- status

```
# df -h > ./df-h
# fdisk -lu > ./fdisk-lu
# hpacucli ctrl all show config detail > ./config-detail
# hpacucli ctrl slot=0 ld all show > ./hpacucli-ld
# hpacucli ctrl slot=0 pd all show > ./hpacucli-pd
```

- rebuild

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

