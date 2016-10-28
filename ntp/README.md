#ntp

### 時刻合わせ
gap出た時。再起動した時とかたまにズレる。
そのうち同期されるけどミドル上げる前に調整しないとログの時間とかおかしくなる。

```
# ntpd -p
# service ntpd status
# service ntpd stop
# service ntpd status
# date
# ntpdate -d time.data-hotel.net
# ntpdate time.data-hotel.net
# ntpdate -d time.data-hotel.net
# date; hwclock --show
# service ntpd status
# service ntpd start
# service ntpd status
```

ntpd -q を使えって言われてる。
http://www.asahi-net.or.jp/~AA4T-NNGK/ntpd.html

---


ドリフトファイル

```
/etc/ntp.conf:driftfile /var/lib/ntp/drift
```

- 時刻の"進め方"のズレを書き込む(どれだけズレているかではない)
このズレをntpが理解していることでNTP参照回数を減らしても自己調整できるようになる。

- サーバによって時刻の進み方が早い/遅いなどの癖がある
この癖は1時間ほどのntpdによる観察で判明する。その結果をドリフトに書き込む。
そのため、他のサーバからドリフトファイルをコピーしてくると自己調整開始に時間がかかる場合があるのでコピーはしないほうがいい。

- ドリフトファイルのタイムスタンプを見ることでntpサーバと同期をとった時間がわかる。


### CentOS 7 / CentOS 6.8 以降は chrony とかいうのに変わった
