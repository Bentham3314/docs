# postgres

インフラ屋がいじれる箇所がおもったより少ない、  
障害時に見るところもよく分からない。  
なんとなくログみればいいんじゃない。

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
