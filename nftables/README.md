# nftables
[wiki](https://wiki.nftables.org/wiki-nftables/index.php/Main_Page)  
[Example_servers](https://wiki.nftables.org/wiki-nftables/index.php/Simple_ruleset_for_a_server)  

```
// 現在の設定の確認
# nft list ruleset
# lsmod | grep ^nf

// 設定の保存/永続化
# nft list ruleset > /etc/nftables.conf

// fileから設定のロード
# nft -f filename
```

- /etc/nftables.confに書き出した後、そのままruleを追加して `nft -f /etc/nftables.conf` でロードするとルールが重複する.  
  ファイルの先頭に `flush ruleset` を追加してから `nft -f /etc/nftables.conf.new` とかでロードしてもう一回書き出す.  

# ToDo:
- NAT設定例

## Configuring tables

### Adding tables

```
# nft add table ip filter
```

### Show/List tables

```
# nft list tables
```

### Deleting tables

```
# nft delete table ip foo
```

### Flushing tables
指定したテーブルからすべてのルールを削除する.

```
# nft flush table ip filter
```

## Configuring chains
チェイン(chain)はルールを保持する.  
iptablesとは異なり、nftablesでは事前定義されているチェインが存在しないので、  
チェインを作成する必要がある.  

### Adding base chains

```
# nft add chain [<family>] <table-name> <chain-name> { type <type> hook <hook> priority <value> \; [policy <policy>]}
# nft 'add chain ip foo input { type filter hook input priority 0; }'
```

> Note: bashなどのshellの場合、セミコロンなどの特殊文字をエスケープする必要がある.  
> 以下のコマンドで対話モードでnftを実行できる  

```
# nft -i
```

priorityはチェインを実行する順序を決定する.  
inputチェインのプライオリティは `-12, 1, 0, 10` の順序である(値が低い方が優先される).  
同じpriorityを指定可能だが実行順は保証されない.  

トラフィックの転送を拒否する場合は以下を設定する.  

```
# nft 'add chain ip foo output { type filter hook output priority 0; }'
```

この時点ではフィルタされないが(default policy = accept)、  
これでinput/outputのフィルタの準備が整う.  

### Base chain hooks
- prerouting: routing実行前に実行される  
- input: ルーティング後、ローカルシステム宛の場合実行される  
- forward: ルーティング後、ローカルシステム宛でない場合実行される  
- output: ローカルマシンのプロセスから発信されたパケットを捕まえる  
- postrouting: ローカルシステム発とルーティングされた後に実行される  
- ingress(netdev familyのみ): Linux kernel 4.2からprerouting前のNICドライバを通過した後のトラフィックをフィルタできる. `tc` の代替.  

### Base chain priority
acceptされたパケットは同じhook内の優先順位が低いベースチェインが存在する場合は *再評価される* .  
つまりパケットはドロップされるかベースチェインが存在しなくなるまで特定のhookを通過する.  

```
table inet filter
    chain ssh {
        type filter hook input priority 0; policy accept;
        # ssh packet accepted
        tcp dport ssh accept
    }
    chain input {
        type filter hook input priprity 1; policy drop;
        # the same ssh packet is dropped here by means of default policy.
    }
```

### BAse chain policy
- accept: default  
- drop: Discard  

### Deleting chains

```
# nft delete chain ip foo input
```

### Flushing chain

```
# nft flush chain ip foo input
```

### Example configuration: Filtering traffic for your standalone computer

```
# nft add table filter
# nft 'add chain ip filter input { type filter hook input priority 0; }'
# nft 'add chain ip filter output { type filter hook output priprity 0; }'
```

## Simple rule management

### Appending new rules
対応するテーブルとチェインを指定する.  

```
# nft add rule filter output ip addr 8.8.8.8 counter
```

### Listing rule

```
# nft list table filter
# nft list table filter output
table ip filter {
  chain output {
    type filter hook output priority 0;
    ip daddr 8.8.8.8 counter packets 0 bytes 0
    tcp dport ssh counter packets 0 bytes 0
  }
}
```

### Testing your rule

```
# ping -c1 8.8.8.8
# nft -nn list table filter
table ip filter {
  chain input {
    type filter hook input priority 0;
  }

  chain output {
    type filter hook output priority 0;
    ip daddr 8.8.8.8 counter packets 1 bytes 84
    tcp dport 22 counter packets 0 bytes 0
  }
}
```

### Adding a rule at a given position

```
# nft list table filter -n -a
table filter {
        chain output {
                 type filter hook output priority 0;
                 ip protocol tcp counter packets 82 bytes 9680 # handle 8
                 ip saddr 127.0.0.1 ip daddr 127.0.0.6 drop # handle 7
        }
}
```

handle 8の次にルールを追加する場合:  

```
# nft add rule filter output position 8 ip daddr 127.0.0.8 drop
# nft list table filter -n -a
table filter {
        chain output {
                 type filter hook output priority 0;
                 ip protocol tcp counter packets 190 bytes 21908 # handle 8
                 ip daddr 127.0.0.8 drop # handle 10
                 ip saddr 127.0.0.1 ip daddr 127.0.0.6 drop # handle 7
        }
}
```

handle 8の前にルールを追加する場合(insert):  

```
# nft insert rule filter output position 8 ip daddr 127.0.0.8 drop
```

### Removing all the rules in a chain

```
# nft flush rule filter output
# nft flush rule filter
```

### Prepending new rules

```
# nft insert rule filter output ip daddr 192.168.1.1 counter
```

### Replacing rules

```
# nft list ruleset -a
table ip filter {
        chain input {
                type filter hook input priority 0; policy accept;
                ip protocol tcp counter packets 0 bytes 0 # handle 2
        }
}
```

```
# nft replace rule filter input handle2 counter
# nft list ruleset -a
table ip filter {
        chain input {
                counter packets 0 bytes 0 
        }
}
```

TCPパケットからすべてのパケットへ変更されている.  

## Atomic rule replacement

### Warning about Shell scripting + nftables
iptablesでは複数のiptablesコマンドをbash scriptにしてフィルタを設定するのは一般的である.  
これはアトミックではないため最適ではなく、bash script実行中は部分的に構成された状態になる.  
nftablesは `-f` オプションでアトミックなルール更新を導入している.  
nftablesはすべての設定ファイルを読み込んで、既存の構成と一緒にメモリ内にコンフィルオブジェクトを作成し、  
古い設定と新しい設定を入れ替えるため、フィルタが部分的な構成になることがない.  

### Atomic Rule Replacement

```
# nft -f file
```

fileはrule-setを含む. rule-setの保存は以下:  

```
# nft list table filter > filter-table
```

restore:

```
nft -f filter-table
```

#### Notes.
- Table Creation: `nft list table filter > filter-table` を読み込む前に `nft create table ip filter` を実行すると、テーブルが存在しないため、エラーとなる. 最新のリリースではこれにより一貫した動作をする.  
- Duplicate Rules: `filter-table` ファイルの先頭に `flush table filter` を追加すると `iptables-restore` と同じ動作をする. kernelはファイル内のルールコマンドを1回のトランザクションで処理するため、基本的に、新しいルールのflushとロードは1回のショットで行われる. テーブルをflushしないことを選択した場合、構成を再ロードするたびに重複するルールが表示される.  
- Flushing Sets: `flush table filter` はテーブル内のセットの定義をflushしない. setをflushするには `flush ruleset` (Linux 3.17から) か明示的にsetを削除する. セットがすでに存在する場合、早いバージョン( `Linux <= 3.16` ) ではインポートを許可しないが、後のバージョンでは許可する.  
- What happens when you include 2 file which each have a statement for the filter table? 2つのファイルから192.168.1.1と192.168.1.2を許可するルールがある場合、それぞれ追加される.  
- どちらかあるいはどちらにもないflushステートメントの扱い: インクルードされたファイルにflushがある場合、ファイルがロードされた時点ではなく、構成スワップが実行された時点で実行される. いずれにもflushステートメントが含まれない場合、ruleは重複する. flushステートメントを含めるとルールが重複することはなく、両方のファイル構成が含まれる.  


## Bulding rules through expressions
`nftables` は以下のbuilt-in operationを提供する:  

- **eq** `==`  
- **ne** `!=`  
- **lt** `<`  
- **gt** `>`  
- **le** `<=`  
- **ge** `>=`  

注意: `<` と `>` は標準入力と標準出力のリダイレクトとして認識されるためエスケープする必要がある(eg. `\<` )

```
# nft add rule filter input tcp dport != 22
# nft add rule filter input tcp dport >= 1024
```

## Operations at ruleset level

### Using native nft syntax
Linux Kernel 3.18 はルールセット全体を管理するために利用可能な操作に関するいくつかの改善が含まれる.  

#### Listing

```
# nft list ruleset

// per family:
# nft list ruleset arp
# nft list ruleset ip
# nft list ruleset ip6
# nft list ruleset bridge
# nft list ruleset inet
```

#### Flushing

```
# net flush ruleset

// per family
# nef flush ruleset arp
# nft flush ruleset ip
# nft flush ruleset ip6
# nft flush ruleset bridge
# nft flush ruleset inet
```

#### Backup/restore

```
# echo "nft flush ruleset" > backup.nft
# nft list ruleset >> backup.nft

// atomically load:
# nft -f backup.nft
```

## Scripting
shell scriptにするとコメントを追加でき、ヒューマンフレンドーにアレンジできる.  
shell scriptでルールを更新するとatomicではなくなる.  
nftablesはネイティブスクリプト環境を提供しているので、基本的に、rulesetファイルのインクルードや  
変数の定義、コメントの追加が可能である. restoreには `nft -f my-ruleset.file` コマンドを利用する.  
nftables scriptを作成する際はファイルの先頭に以下を追加する:  

```
#!/usr/sbin/nft -f
```

### Adding comments
`#` がコメント行

```
#!/usr/sbin/nft -f

#
# table declaration
#
add table filter

#
# chain declaration
#
add chain filter input { type filter hook input priprity 0; policy drop; }

#
# rule declaration
#
add rule filter input ct state established.related counter accept
```

### Including files
他のファイルは `include` ステートメントでインクルードできる.  
includeファイルの検索には `-I/--includepath` オプションを指定できる.  

`-I/--includepath` が未指定の場合、nft rulesのデフォルトファイルはコンパイル時に指定される.  
デフォルトのディレクトリは `-h/--help` オプションで確認できる.  
includeステートメントはワイルドカードシンボル( `\*,?,[]` ) をサポートしている.  
NOTE: dot(.)で始まるファイルはマッチングしない.  

```
#!/usr/sbin/nft -f

# include a single file using the default search path
include "ipv4-nat.ruleset"

# include all files ending in *.nft in the default search path
include "*.nft"

# include all files in a given directory using an absolute path
include "/etc/nftables/myruleset.nft"
```

### Defining variables
以下のように変数を定義できる:

```
#!/usr/sbin/nft -f

define google_dns = 8.8.8.8

add table filter
add chain filter input { type filter hook input priprity 0; }
add rule filter input ip saddr $google_dns counter
```

変数のsetを定義できる:

```
#!/usr/sbin/nft -f

define ntp_servers = { 84.77.40.132, 176.31.53.99, 81.19.96.148, 138.100.62.8 }

add table filter
add chain filter input { type filter hook input priority 0; }
add rule filter input ip saddr $ntp_servers counter
```

`{}` はセットを示すステートメントなので以下を使用しないように注意する:

```
// NG
define google_dns = { 8.8.8.8 }

// OK
define google_dns = 8.8.8.8
```

### File formats
`nft -f <filename>` は2つのフォーマットが許容される.  
1つは `nft list table` の出力の形式であり、2つ目は、 `nft` のバイナリを数回呼び出すがAtomicな方法である.  

```
#!/usr/sbin/nft -f

define ntp_servers = { 84.77.40.132, 176.31.53.99, 81.19.96.148, 138.100.62.8 }

#flush table nat
table ip nat {
	chain prerouting {
		type filter hook prerouting priority 0; policy accept;
                ip saddr $ntp_servers counter
	}

	chain postrouting {
		type filter hook postrouting priority 100; policy accept;
	}
}
```

```
#!/usr/sbin/nft -f

define ntp_servers = { 84.77.40.132, 176.31.53.99, 81.19.96.148, 138.100.62.8 }

add table filter
add chain filter input { type filter hook input priority 0; }
add rule filter input ip saddr $ntp_servers counter
```

## Ruleset debug/tracing
nftables v0.6とlinux kernerl 4.6からrulesetの debug/tracingがサポートされている.  
iptablesのメソッド -J TRACE と同様だがいくつか大きな改善がある.  
debug/tracingの有効化は以下:  
- give support in your ruleset for it (set nftrace in any of your rules)  
- monitor the trace events from the nft tool  


### enabling nftrace
パケットのnftraceの有効化は以下:

```
meta nftrace set 1
```

マッチングした特定パケットのみに有効化できる:  

```
iif lo ip protocol tcp meta nftrace set 1
```

debug/traceしたい特定のパケットのみに限定することが重要.  

### monitoring tracing events

```
# nft monitor trace
```

## Moving from iptables to nftables
NAT含めうまく変換してくれているように見える.

```
# iptables-save > save.txt
# iptables-restore-translate -f save.txt

# iptables-restore-translate -f save.txt > ruleset.nft
# nft -f ruleset.nft
```

## Moving from ipset to nftables
ipsetを変換する手段は今の所ないので手動でやる.  

## Matching packet header fields

### Matching transport protocol
TCP trafficにマッチするルール:

```
# nft add rule filter output ip protocol tcp
```

### Matching Ethernet header fields
ethernet(宛先:ff:ff:ff:ff:ff)にマッチするルール

```
# nft add rule filter input ether daddr ff:ff:ff:ff:ff counter
```

### Matching IPv4 header fields
IPv4 発信/受信のトラフィックを制御できる.

```
# nft add rule filter input ip saddr 192.168.100 ip daddr 192.168.1.1 counter
```

### Matching IPv6 header fields
IPv6 traffic

```
# nft add rule filter output ipv6 daddr abcd::100 counter
# nft add rule filter input ip6 nexthdr tcp counter
```

### Matching TCP/UDP/UDPlite traffic
1-2024ポートへのアクセスをすべて遮断する

```
# nft add rule filter input tcp dport 1 - 1024 counter drop
```

TCP flagsを利用して、SYNではないパケットをカウントすることもできる:  

```
# nft add rule filter input tcp flags != syn counter
```

更に複雑な例として、SYN ／ ACKがセットされたパケットをカウントする:  

```
# nft -i
nft> add rule filter output tcp flags & (syn|ack) == syn | ack counter log
```

以下の例は、MSSが500以下のパケットをドロップする:  

```
# nft add rule inet filter input tcp flags syn tcp option maxseg size 1-500 drop
```

### Matching ICMP traffic
ping拒否:  

```
# nft add rule filter input icmp type echo-request counter drop
```

- echo-reply  
- destination-unreachable  
- source-quench  
- redirect  
- echo-request  
- time-exceeded  
- parameter-problem  
- timestamp-request  
- timestamp-reply  
- info-request  
- info-reply  
- address-mask-request  
- address-mask-replay  


### Matching UDP/TCP headers in the same rule
いくつかのトランスポートプロトコルを1つのルールでマッチさせる事ができるようになった.  
tcp/udp port 53にマッチングする例:  

```
# nft add rule filter input meta l4proto { tcp,udp } @th,16,16 53 counter packets 0 bytes 0 accept comment \"accept DNS\"
```

### Matching packet metainformation

