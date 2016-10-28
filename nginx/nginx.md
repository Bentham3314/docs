# nginx.md

## install
install/configure option

- https://www.nginx.com/resources/admin-guide/installing-nginx-open-source/


### nginx install
全部yumでいれた方が楽


- ngx_http_rewrite_module

```
# wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.37.tar.gz
# tar -zxf pcre-8.37.tar.gz
# cd pcre-8.37
# ./configure
# make && make install
```

- ngx_http_gzip_module

```
# wget http://zlib.net/zlib-1.2.8.tar.gz
# tar -zxf zlib-1.2.8.tar.gz
# cd zlib-1.2.8
# ./configure
# make && make install
```

- ngx_http_xslt_module

```
# yum install -y libxslt-devel libxml2 perl
```

- ngx_http_image_filter_module
yumじゃないとコケた…

```
# yum install -y libgd

# wget https://github.com/libgd/libgd/releases/download/gd-2.1.1/libgd-2.1.1.tar.gz
# tar zxf libgd-2.1.1.tar.gz
# cd libgd-2.1.1
# ./configure
# make && make install
```

- http_ssl_module

```
# wget https://www.openssl.org/source/openssl-1.0.2e.tar.gz
# tar zxf openssl-1.0.2e.tar.gz
# cd openssl-1.0.2e
# ./config shared
# make && make install
```

- nginx

```
# wget http://nginx.org/download/nginx-1.9.9.tar.gz
# tar zxf nginx-1.9.9.tar.gz
# cd nginx-1.9.9
# ./configure \
--user=nginx --group=nginx \
--prefix=/usr/local/nginx \
--sbin-path=/usr/local/nginx/sbin/nginx \
--conf-path=/usr/local/nginx/conf/nginx.conf \
--error-log-path=/usr/local/nginx/logs/error.log \
--http-log-path=/usr/local/nginx/logs/access.log \
--pid-path=/usr/local/nginx/logs/nginx.pid \
--lock-path=/usr/local/nginx/logs/lock/nginx.lock \
--http-client-body-temp-path=/var/lib/nginx/tmp/client_body \
--http-proxy-temp-path=/var/lib/nginx/tmp/proxy \
--http-fastcgi-temp-path=/var/lib/nginx/tmp/fastcgi \
--http-uwsgi-temp-path=/var/lib/nginx/uwsgi_temp \
--http-scgi-temp-path=/var/lib/nginx/scgi_temp \
--with-http_ssl_module --with-http_v2_module \
--with-http_realip_module \
--with-http_addition_module \
--with-http_xslt_module \
--with-http_sub_module --with-http_dav_module \
--with-http_flv_module --with-http_mp4_module \
--with-http_gunzip_module --with-http_gzip_static_module \
--with-http_random_index_module \
--with-http_secure_link_module \
--with-http_degradation_module \
--with-http_stub_status_module \
--with-pcre \
--with-debug \
--with-openssl=../openssl-1.0.2e

# make && make install
```

## commands

- 起動
```
# /usr/local/nginx/sbin/nginx
```

- 停止
```
# /usr/local/nginx/sbin/nginx -s stop
```

- config test
```
# /usr/local/nginx/sbin/nginx -t
```


## module
各種moduleについて

## $args / $is_args
- $args
GETのパラメータが入る
ex): FOO =123＆バー= blahblah

- $is_args
$argsに値がセットされている場合は `?` になる。
それ以外の場合は `""` がセットされる。

## MaxClient
error logに下記出力

```
[alert] 29477#0: 2048 worker_connections are not enough
```

`MaxClient = worker_processes * worker_connections`

## error while loading shared libraries: libpcre.so.1

```
[root@nginx01 conf.d]# /usr/local/nginx/sbin/nginx
/usr/local/nginx/sbin/nginx: error while loading shared libraries: libpcre.so.1: cannot open shared object file: No such file or directory
[root@nginx01 conf.d]#
```

```
[root@nginx01 conf.d]# find / -name libpcre.so.1
/root/pcre-8.37/.libs/libpcre.so.1
/usr/local/lib/libpcre.so.1
[root@nginx01 conf.d]# export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
[root@nginx01 conf.d]#
[root@nginx01 conf.d]#
[root@nginx01 conf.d]# /usr/local/nginx/sbin/nginx -V
nginx version: nginx/1.9.9
built by gcc 4.4.7 20120313 (Red Hat 4.4.7-16) (GCC)
built with OpenSSL 1.0.2e 3 Dec 2015
TLS SNI support enabled
configure arguments: --user=nginx --group=nginx --prefix=/usr/local/nginx --sbin-path=/usr/local/nginx/sbin/nginx --conf-path=/usr/local/nginx/conf/nginx.conf --error-log-path=/usr/local/nginx/logs/error.log --http-log-path=/usr/local/nginx/logs/access.log --pid-path=/usr/local/nginx/logs/nginx.pid --lock-path=/usr/local/nginx/logs/lock/nginx.lock --http-client-body-temp-path=/var/lib/nginx/tmp/client_body --http-proxy-temp-path=/var/lib/nginx/tmp/proxy --http-fastcgi-temp-path=/var/lib/nginx/tmp/fastcgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi_temp --http-scgi-temp-path=/var/lib/nginx/scgi_temp --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_degradation_module --with-http_stub_status_module --with-pcre --with-debug --with-openssl=../openssl-1.0.2e
[root@nginx01 conf.d]#
```

