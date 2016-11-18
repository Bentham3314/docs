キューの確認
```
# postqueue -p
```

強制送信
```
# postqueue -f
```

---

### 滞留しているキュー/キュー数表示

```
# postqueue -p
```

### キューの宛先/送信元調査

* 送信元アドレスでキューが多いやつを調査

```
# mailq | tail -n '+2'  | grep -v '^ *(' | awk 'BEGIN { RS = "" } {  print $7 }'| tr -d '*!'|sort |uniq -c|sort -n
```

* 宛先アドレスでキューが多いやつを調査

```
# mailq | tail -n '+2'  | grep -v '^ *(' | awk 'BEGIN { RS = "" } {  print $8 }'| tr -d '*!'|sort |uniq -c|sort -n
# mailq | tail -n '+2'  | grep -v '^ *(' | awk 'BEGIN { RS = "" } {  print $9 }'| tr -d '*!'|sort |uniq -c|sort -n
```

- 宛先アドレスが複数ある場合1つ目のコマンドだけでは1つ目の宛先アドレスしか調査できないので、両コマンドを実行して確認する

### キュー削除
アドレスは foo@bar で

* [送信元アドレス]を指定して削除  
**$7に送信元アドレス** を指定

```
# mailq | tail -n '+2'  | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $7 == "_FROM" ) print $1 }' | tr -d '*!'
# mailq | tail -n '+2'  | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $7 == "_FROM" ) print $1 }' | tr -d '*!' | postsuper -d -
# mailq | tail -n '+2'  | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $7 == "_FROM" ) print $1 }' | tr -d '*!'
```

* [宛先アドレス]を指定して削除  
**$8に宛先アドレス** を指定

```
# mailq | tail -n '+2'  | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $8 == "_TO" ) print $1 }' | tr -d '*!'
# mailq | tail -n '+2'  | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $8 == "_TO" ) print $1 }' | tr -d '*!' | postsuper -d -
# mailq | tail -n '+2'  | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $8 == "_TO" ) print $1 }' | tr -d '*!'
```

* [送信元アドレス]と[宛先アドレス]を両方指定して削除  
**$7に送信元アドレス $8に宛先アドレス** を指定

```
# mailq | tail -n '+2' | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $7 == "_FROM" && $8 == "_TO" ) print $1 }' | tr -d '*!'
# mailq | tail -n '+2' | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $7 == "_FROM" && $8 == "_TO" ) print $1 }' | tr -d '*!'| postsuper -d -
# mailq | tail -n '+2' | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $7 == "_FROM" && $8 == "_TO" ) print $1 }' | tr -d '*!'
```

* [送信元がMAILER-DAEMON]を削除する場合  
**$7に送信元アドレス** を指定

```
# mailq | tail -n '+2' | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $7 == "MAILER-DAEMON" ) print $1 }' | tr -d '*!'
# mailq | tail -n '+2' | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $7 == "MAILER-DAEMON" ) print $1 }' | tr -d '*!' | postsuper -d -
# mailq | tail -n '+2' | grep -v '^ *(' | awk 'BEGIN { RS = "" } { if ( $7 == "MAILER-DAEMON" ) print $1 }' | tr -d '*!'
```

* キュー全削除

```
# postsuper -d ALL
```
