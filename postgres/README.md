# postgres

### ggr note
- postgresql.conf  

```
### mem
shared_buffers
wal_ buffers

checkpoint_segments  ### disk flush timing

log_line_prefix='[%t] %rT%d: '   ### log

### tcp session
tcp_keepalives_idle =
tcp_keepalives_interval =
tcp_keepalives_count =
```

- /etc/sysctl.conf

```
kernel.shmall
kernel.shmmax
```

### Process list
path注意  
postgresユーザ指定

```
# echo "SELECT procpid, start, now() - start AS lap, current_query FROM (SELECT backendid, pg_stat_get_backend_pid(S.backendid) AS procpid,  pg_stat_get_backend_activity_start(S.backendid) AS start, pg_stat_get_backend_activity(S.backendid) AS current_query FROM (SELECT pg_stat_get_backend_idset() AS backendid) AS S ) AS S WHERE current_query <> '' ORDER BY lap DESC\x" | psql -U postgres
```

ユーザ変更して実行  
やってる事一緒だけど見やすくしてみた

```
# su - postgres
$ whoami
$ echo "SELECT \
    procpid, \
    start, \
    now() - start AS lap, \
    current_query \
FROM \
    (SELECT \
        backendid, \
        pg_stat_get_backend_pid(S.backendid) AS procpid, \
        pg_stat_get_backend_activity_start(S.backendid) AS start, \
        pg_stat_get_backend_activity(S.backendid) AS current_query \
    FROM \
        (SELECT pg_stat_get_backend_idset() AS backendid) AS S \
    ) AS S \
WHERE \
    current_query <> '' \
ORDER BY \
    lap DESC\x" | psql
```
