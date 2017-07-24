# install Cacti

## Env
CentOS7  
MySQL 5.6


- httpd and mysqld

```
# yum install -y httpd

# rpm -ivh http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
# ls -l /etc/yum.repos.d/
# cat /etc/yum.repos.d/mysql-community.repo
# vi /etc/yum.repos.d/mysql-community.repo   # Enable to use MySQL 5.6 / Disable MySQL 5.7
# yum -y install php-mysql mysql mysql-serve
# vi /etc/my.cnf
---
no_auto_rehash

innodb_buffer_pool_size = 2G
innodb_log_file_size    = 1G
log_bin              = /var/lib/mysql/mysql-bin
expire_logs_days     = 14

default-time-zone = 'Asia/Tokyo'
---
# service mysqld start
```

- yum install cacti

```
# yum install -y epel-release
# yum install -y php cacti

# cd /etc/ ; pwd
# cp -ip php.ini .backup/php.ini.`date +%Y%m%d`
# vi php.ini
---
default_charset = "UTF-8"
date.timezone = Asia/Tokyo
---
```

- configure httpd

```
# cd /etc/httpd/conf.d/ ; pwd
# cp -ip cacti.conf .backup/cacti.conf.`date +%Y%m%d`
# vi cacti.conf    ## edit Require or Order // Access list
# apachectl -t
# apachectl start
```

- setup mysql

```
# mysql_tzinfo_to_sql /usr/share/zoneinfo/ | mysql mysql
# mysql
mysql > create database cacti;
mysql > grant all privileges on cacti.* to cactiuser@127.0.0.1 identified by 'hoge';
mysql > grant select on mysql.time_zone_name to cactiuser@127.0.0.1 identified by 'hoge';
mysql > \q

# mysql cacti < /usr/share/doc/cacti-1.1.6/cacti.sql
# cd /etc/cron.d/ ; pwd
# cp -ip cacti .backup/cacti.org
# vi cacti   ### start poller cron
```
