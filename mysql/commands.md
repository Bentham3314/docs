# MySQL commands

- 変なcnfを読み込んで起動する  

```
# mysqld_safe --defaults-file=/etc/my57.cnf &
```

### mysqldump

```
# mysqldump -S [sock] --single-transaction --master-data=2 --events --routines [db] -u[username] -p | gzip > [db].gz
# mysqldump --single-transaction --master-data=2 --events --routines --include-master-host-port --all-databases | gzip > dump_all.gz
```

- all-databases  
全DB

- single-transaction  
整合性を取る/transactionがサポートされてる場合のみ有効

- master-data=2  
dumpしたファイルの中に change master とかつけてくれる  

- events/routines  
トリガーとか設定されている場合に指定する  

- include-master-host-port  
スレーブ作らない場合は不要


### Restore

```
# mysql < zcat dump_all.gz
# zcat dump.gz | mysql [db]
```

### status

```
> show status;
> show full processlist\G
> show engine innodb status\G
> show global variables;
> show global status;
```

- show current binlog file & position  

```
> show master status;
```


- show all binlog file  

```
> show master logs;  
```

### log

- rotate binlog file  
実行するだけではただファイルを開き直すため、  
logファイルはmvなりしてから実行する  

```
> flush [binary] logs;
```


### Confirmation purge log
!!! binlogは直接rmしないこと !!!  
1. indexから消す必要があるため  
2. slaveが参照していないか確認してからpurgeする

- Check slave  

```
> show slave status\G
```
CHECK [master log file]

- Check master  

```
> show master status;
> show master logs;
```

```
> PURGE MASTER LOGS TO '';
```

## Restore後

- MySQL5.5/performance_schema  
ユーザの権限周りの設定をいい感じにやってくれる  
https://dev.mysql.com/doc/refman/5.6/ja/mysql-upgrade.html  

乱暴にいえば `FLUSH PRIVILEGES;` 

```
# mysql_upgrade
```


## Skip error for replication
```
> show slave status\G
> set global sql_slave_skip_counter =1;
> start slave;
> show slave status\G
```

## Add user
```
> select user,host,password from mysql.user;
> grant all privileges on *.* to Bentham3314@localhost identified by '******';
> show grants for Bentham3314@localhost;
> select user,host,password from mysql.user;
```

- mysql5.7

```
> select host,user,authentication_string from mysql.user;
> create user 'hoge'@'10.100.233.%' identified with mysql_native_password as 'hoge';
> show grants for hoge@10.100.233.% ;
```

GRANT, REVOKE, or SET PASSWORDで追加した場合 `FLUSH PRIVILEGES;` は不要  
INSERT,UPDATE,DELETEの場合は必要


## Create db
`> create database hoge default character set=utf8;`
