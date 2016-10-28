# init

## 起動順序
1. init起動
2. /etc/inittab ロード
3. /etc/rc.d/rc.sysinit 実行
4. /etc/rc.d/rc[runlevel].dに応じたプロセス実行
5. ログイン受付プロセスの起動


### 2. /etc/inittab の読み込み
デフォルトのrunlevelが書いてあるだけ

### 3. /etc/rc.d/rc.sysinit 実行

3.1 ホスト名の設定
3.2 NW初期化
3.3 /proc, /sys のマウント

### 4./etc/rc.d/rc[runlevel]に応じたプロセス実行

4.1 /etc/rc.d/rc[runlevel].d 内のスクリプト実行
4.2 /etc/rc.d/rc.local 実行

