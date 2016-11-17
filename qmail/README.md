## 注意点  

setuidgid を使う  
オーナーが違うと基本的に動かないので注意する. 特にvi時

---

qmail の制御ファイル


環境変数が指定されている場合、設定ファイルが無効になるケースが多い。  
環境変数の使用は非推奨っぽい。


## qmail-smtpd
#### badmailfrom  
受信拒否アドレスリスト (再起動不要)  
書式(一行にひとつ記載する)  

```   
hoge@hoge.com
@hoge.com
```

#### databytes
受け取れるメールの最大バイト数(デフォルト:0(無制限))

#### localiphost
ローカルIPアドレスを使用するメールメッセージ用のローカルホスト名(デフォルト:me)  
受け取る事ができるメールサーバのIPアドレスを認識していなければならない  
  ドメインベースで認識してると自分でDNS引いて、「あ、これ俺だ」って処理してないからかな。もうちょっと本読む。

#### morercpthosts
rcpthostsで指定するホスト名は50以下にするために使用  
qmail-smtpdはmorercpthosts.cdbを利用する

#### rcpthosts
別のホスト、ドメイン宛のメッセージをqmailで受け取れるようにする(指定されていないホスト、ドメイン宛は受け取らない)  
少なくともローカルホスト名を指定したものを用意する必要がある。ようは、スパムの中継(踏み台)にされる  
* 環境変数: RELAYCLIENT

#### smtpgreenting
qmail-smtpdがSMTP接続を開始するために使う挨拶メッセージ(デフォルト:me)  
meがない場合はqmail-smtpdは実行を拒否する  

#### timeoutsmtpd
SMTPサーバからのデータの受信を待つ時間(デフォルト:1200(20分))

#### qmail-qmqpc
qmqpservers  QMQP(Quick Mail Queuing Protocol)サーバのアドレスを指定する  
ワークステーション時代の遺産

## qmail-inject
#### defaultdomain
user@host.domain形式でない場合、domainを補完する(ローカル配送)(デフォルト:me)  
* 環境変数:QMAILDEFAULTDOMAIN

#### defaulthost
hostを補完する。ドメインが指定されなかった場合、@defaulthost.defaultdomainが付与される  
* 環境変数:QMAILDEFAULTHOST

#### idhost
Message-IDを作成に使用するホスト名。デフォルト(me)  
* 環境変数:QMAILIDHOST


#### plusdomain
プラス記号(＋)で終わるメールアドレスに追加するドメイン名(デフォルト:me)

## qmail-send
#### bouncefrom
バウンスメールのfromを設定(デフォルトはMAILER-DAEMON)

#### bouncehost
host名。ない場合はmeを使う。(デフォルト: MAILER-DAEMON@hostname)

#### concurrencylocal
同時に実行できるローカル配送プロセス数(デフォルト:10)

#### concurrencyremote
同時に実行できるリモート配送プロセス数(デフォルト:20)

#### doublebounchehost
バウンスメールがバウンスした時(ダブルバウンス)に転送するホスト名。(デフォルト:me)

#### doublebounceto
ダブルバウンスの送信先ユーザを指定(デフォルト:postmaster)

#### envnoathost
ホスト名が指定されていないアドレスであった場合、ホスト名を指定する。(デフォルト:me)

#### locals
qmailがローカルメールアドレスとみなすメールアドレスを指定(デフォルト:me/両方ない場合実行拒否)

#### me
ローカルメールサーバのホスト名

#### percenthack
(user%hostname)とかいうクソ古いメールアドレスをDNS形式に変換するか確認する。

#### queuelifetime
メールキュー内の未送信メッセージの保管期間(デフォルト:1週間(604800秒))。  
この時間が過ぎると再送を試みて無理なら削除する。

#### virtualdomains
ローカルメールサーバ以外のユーザのドメイン宛メールを受け取れるようにする。  
複数のドメインを持つことができるようになる。

## qmail-remote
#### helohost
リモートメールサーバとのSMTPセッションで使用するホスト名。ない場合はmeを使用。どちらもない場合は、実行を拒否?

#### smtproutes
メールを特定の宛先に配送するために使用できる静的なSMTP経路を指定する。

#### timeoutconnect
SMTPサーバからの接続許可を待つ時間(秒指定)。(デフォルト:60)

#### timeoutremote
SMTPサーバからの応答を待つ時間。(デフォルト:1200(20分))



## smtproutes

```
foo.net:bar.net
.hoge.com:
:fw.hoge.net:1000
```

* 1行目  
  foo.net宛のすべてのメッセージをbar.netへ転送する。これが指定されているとqmailはfoo.netのMXレコードを引かなくなる。
* 2行目  
  MXレコードのチェックを強制する。が、転送そのものはしない。
* 3行目  
  1,2行目の処理に該当しない場合のメールの処理設定。1000ってのはポート番号。
  foo.net宛ではないメールはfw.hoge.netの1000番ポートへ転送する。


smtproutesではqmailのSMTP経路をいくつでも指定できるが、指定された順番でアドレスの処理を行う。  
順番間違えると意図しない動作したり、転送ループになる。
