# MySQLレプリケーション

### レプリケーションにおける各スレッドの役割

|スレッド名|マニュアル等での名称|役割|
|:-:|:-:|:-:|
|Binlogダンプ|Senderスレッド|バイナリログ内容をスレーブに送信|
|ACK Receiver|ACK Receiverスレッド|スレーブからACKを受信|
|Slave I/O|Receiverスレッド|バイナリ内容を受信しリレーログに記録|
|Slave SQL|Applier|リレーログ内容をデータに反映|

|ファイル名|役割|
|:-:|:-:|
|リレーログ|マスターから受信した変更点を記録したファイル|
|master.info|Masterへの接続に必要な情報や、読み取りを開始するバイナリログが格納されているファイル|
|relay-log.info|リレーログをどこまで適用したかを記録しているファイル|


### レプリケーションのセットアップ

1. マスターを停止した状態でデータディレクトリをスレーブにコピー  
2. [スレーブ] データディレクトリのauto.cnfを削除  
  - auto.cnfはサーバを一意に識別するためのUUIDを格納するため、MySQL5.6から自動生成されるようになった。  
  **削除しない場合はレプリケーション開始時にUUID重複のエラーが発生する**  
3. [マスター]マスターとなるための設定の追加  
  - バイナリログ出力の有効化  
  - サーバIDを設定 / GTIDを有効にするための設定  
```
#my.cnf

[mysqld]
server_id = 1
log-bin

gtid_mode = on
enforce_gtid_consistency = on
```

4. [スレーブ]スレーブになるための設定  

```
#my.cnf

[mysqld]
server_id = 2 ### マスターとは別のIDを指定
log-bin

gtid_mode = on
enforce_gtid_consistency = on
```

5. [マスター]レプリケーション用ユーザの作成  
ユーザを作成した上でデータコピーすることでスレーブ側でのユーザ設定が不要になる(Master昇格が発生する場合)  

```
mysql> CREATE USER `repl`@`localhost` IDENTIFIED BY 'reql';
mysql> GRANT REPLICATION SLAVE ON *.* TO `repl`@`localhost`;
```

6. [スレーブ]レプリケーションスレーブの設定と開始

```
mysql> CHANGE MASTER TO MASTER_HOST='localhost', MASTER_USER='repl', MASTER_PASSWORD='repl', MASTER_AUTO_POSITION=1;
mysql> START SLAVE;
```

7. レプリケーションの動作確認

```
# [マスター]
mysql> show databases;

# [スレーブ]
mysql> show databases;

# [マスター]
mysql> CREATE DATABASE repl_test;
mysql> show databases;

# [スレーブ]
mysql> show databases;

# [マスター]
mysql> use repl_test;
mysql> CREATE TABLE t1 (col1 INT);
mysql> INSERT INTO t1 VALUES (1),(2);
mysql> SELECT * FROM repl_test.t1;

# [スレーブ]
mysql> SELECT * FROM repl_test.t1;
```

