# command
Misc Linux commands

## performance

```
# uptime
# vmstat 1
# free -m
# top
# dmesg | tail
# tail /var/log/messages

// Require sysstat pkg
# pidstat 1
# iostat -xz 1
# mpstat -P ALL 1
# sar -n DEV 1
# sar -n TCP,ETCP 1

// install sysstat
# yum install -y sysstat
```

### openssl
証明書の確認/SSLを使用するtelnet的な使い方が可能

**証明書系**  

* COMMON NAME確認
```
$ echo | openssl s_client -connect hoge:443 -showcerts 2>&1 | openssl x509 -noout -subject
```

* 期限確認
```
$ echo | openssl s_client -connect hoge:443 -showcerts 2>&1 | openssl x509 -noout -issuer -dates
```

* SNI
```
$ echo | openssl s_client -connect hoge:443 -servername fuga 2>&1
```

* key-pair check  
```
# openssl x509 -noout -modulus -in <cert_file>
# openssl rsa -noout -modulus -in <key_file>
# diff <(openssl x509 -noout -modulus -in <cert_file>) <(openssl rsa -noout -modulus -in <key_file>)
```

* 証明書から発行元、ドメイン、期限などを確認する  
```
# openssl x509 -noout -issuer -subject -dates -in /tmp/server.crt
```

* version確認

```
$ openssl version -a
# rpm -q openssl
# rpm -q openssl --chengelog | head -n30
```

---

### curl
- 標準的なオプション  
`-s` ダウンロードの進捗とエラーメッセージを表示しない  
`-S` エラーメッセージは表示する / `-sS` でエラーメッセージを表示  
`-L` リダイレクト先にアクセス   
`-I` HTTPリクエスト時にHEADメソッドを使い、レスポンスヘッダのみを取得  
`-o [file name]` 取得結果をファイルに保存(このオプションを指定しないと、標準出力に吐き出される)  
`--trace --trace-ascii` HTTPリクエスト・レスポンスのデータを全て dump
`-v` リクエストヘッダとレスポンスヘッダを標準エラー出力に表示  
`-A [user-agent]` Agent指定  

- SSL系オプション  
`-k` 証明書のエラーを無視  
`--sslv2`  
`--sslv3`  
`--tlsv1`  
`--tlsv1.0`  
`--tlsv1.1`  
`--tlsv1.2`  

- よく使う  

```
$ curl -ILSs [URL]
```

- BASIC認証 / いろいろなやつ  

```
$ curl --user [user]:[pass] [URL]
$ curl --anyauth --user [user]:[pass] [URL]
```

- referer  

```
$ curl -ILSs --referer "hoge" URL
```


---

### zip/unzip

- passつきzip  

```
# zip -P[pass] hoge.zip hoge    ### ひすとりにパスワード残るから注意
# zip -e hoge.zip hoge          ### 標準入力からパスワードいれる
```

- unzip  

```
# unzip -p hoge.zip           ###中身みるだけ、解凍はしない
# unzip hoge.zip
```

---

### ssh

- PortForward

```
サーバ上で
$ ssh -o 'Gatewayports yes' -L 13389:172.16.200.14:3389 localhost -i .ssh/hoge.pem

手元から
$ ssh -L13389:target:3389 remote -lhoge -i ~/.ssh/hoge
```

- fingerprint

```
ssh -o "StrictHostKeyChecking=no"
```

- for / while

`-n` オプションがミソ

```
# cat list | while read host ip ; do echo "--- $host / $ip ---" ; ssh -n $ip "ip a | grep inet" ; done
# for x in `cat list` ; do echo "--- $x --- " ; ssh -n $x "ip a | grep inet" ; done
```

