# バックアップとリストア

- path  
/usr/local/mysql/bin/  
- datadir  
/usr/local/mysql/data  
- my.cnf  
/etc/my.cnf


dump

```
# mysqldump -uroot -p --master-data=2 -S /tmp/mysql.sock --hex-blob --all-databases --single-transaction > dump.sql
```

restore

- datadir作成

```
# rm -rf /usr/local/mysql/data
# mkdir /usr/local/mysql/data
# chmod 755 /usr/local/mysql/data
# chown mysql:mysql /usr/local/mysql/data
```

- mysqld起動/restore

```
# vi /etc/my.cnf  ### bin-logの出力を停止
# mysqld --basedir=/usr/local/mysql --initialize-insecure
# mysqld --defaults-file=/etc/my.cnf --skip-networking --skip-grant-tables &
# mysql -uroot -p -S /tmp/mysql.sock < dump.sql
```

- restore後、mysql_upgrade

```
# mysqladmin -uroot -p -S /tmp/mysql.sock shutdown
# service mysqld start
# mysql_upgrade -uroot -p -S /tmp/mysql.sock
```

以下テーブルのエラーは無視してよい  
- mysql.innodb_table_stats  
- mysql.innodb_index_stats
