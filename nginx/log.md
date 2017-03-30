# log

## X-Forwarded-For
プロキシやLBがクライアントとの間に入っている場合、  
そのIPアドレスがログに記録されてしまうので実際のクライアントIPが分からない。  
X-Forwarded-Forをプロキシ側で有効にし、WEBサーバ側はその値を見る必要がある。

- conf  
set_real_ip_from はプロキシサーバのIPアドレスを記載。  
プロキシ段数分書く必要がある？

```
set_real_ip_from [IP Addr / Mask];
set_real_ip_from [IP Addr / Mask];
real_ip_header X-Forwarded-For;
real_ip_recursive on;
```


