# docker

## commands

- list/images  
- stop/rm  
- start  
- pull/commit/build/push  
- attach/dettach  
- ユーザ指定  
- ホストのファイルをマウント  
- cp  

#### list/images

```
# docker ps -a
# docker images
```

#### stop/rm

```
# docker rm [CONTAINER_ID]
# docker kill $(docker ps -a)
# docker rm $(docker ps -a -q)

# docker rm -f $(docker ps -a -q)
```

#### start

```
# docker run -i -t ubuntu /bin/bash
# docker run -p 8080:80 nginx
```

- options  
i 標準入出力  
t 擬似端末  
p ホストの8080番ポートに接続するとコンテナの80番ポートへアクセスする  


#### pull/commit/build/push

- pull  
docker imageのDL  
- commit  
コンテナ内のファイルからimageを作成  
- build  
dockerfileで記述した構築方法を元にdockerイメージをビルド  
- push  
dockerイメージをDocker Hubに保存  

#### デタッチ/アタッチ

- dettach  
Ctrl-P -> Ctrl-Q  

- attach  

```
# docker attach [CONTAINER_ID]
# docker exec -i -t [CONTAINER_ID] /bin/bash
```

#### ユーザ指定
コンテナ内のrootユーザはホストのrootユーザと同等となるため、セキュリティ上変更するほうが望ましい  
v1.10以降はユーザ名前空間の実装によりホスト/コンテナ間のユーザが完全に分離されているので気にしなくてもいいかも。

```
# docker run -i -t -u nginx ubuntu /bin/bash
```

#### ホストのファイルをマウント
`-v` オプションを使用する。  
`-v マウント元:マウント先`

```
# docker run -i -t -v $HOME/testdir:testdir ubuntu /bin/bash
```

#### cp
ホスト/コンテナ間でのファイルコピー

```
# docker cp host.txt [CONTAINTER_ID]:/path/to/host.txt
# docker cp [CONTAINER_ID]:/path/to/container.txt .
```

---

## dockerの内部

### ディレクトリ

- dockerのデータは以下  
/var/lib/docker

```
# ls -l /var/lib/docker/
total 40
drwx------ 2 root root 4096 Sep  2  2015 containers
drwx------ 4 root root 4096 Sep  2  2015 devicemapper
drwx------ 2 root root 4096 Sep  2  2015 graph
drwx------ 2 root root 4096 Sep  2  2015 init
-rw-r--r-- 1 root root 5120 Feb 19  2016 linkgraph.db
-rw------- 1 root root   19 Sep  2  2015 repositories-devicemapper
drwx------ 2 root root 4096 Sep  2  2015 tmp
drwx------ 2 root root 4096 Dec 20  2015 trust
drwx------ 2 root root 4096 Sep  2  2015 volumes
```

- コンテナに関する情報  
/var/lib/docker/containers

- コンテナのファイルシステム  
/var/lib/docker/images/aufs/leyerdb/mounts  
  mount-idの情報があるのはここ

- コンテナ内のファイル  
/var/lib/docker/images/aufs/mnt/[mount_id]

---

## コンテナからDockerイメージを作成

1. コンテナをシェルで実行  
2. コンテナ内でアプリケーション/ミドルウェアのインストールを行う  
3. コンテナからdockerイメージを作成する  

1. コンテナをシェルで実行  

```
# docker run -i -t ubuntu /bin/bash
```

2. ミドルウェアのインストール(コンテナ内で実行)

```
# apt-get update
# apt-get install -y curl
# curl -sL https://deb.nodesource.com/setup_4.x | bash -
# apt-get install -y nodejs
```

Crtl+D で終了

```
# docker ps -a
```

3. コンテナからdockerイメージを作成

```
# docker commit [CONTAINER_ID] [NAME]
# docker images
```

## dockerfileからdockerイメージを作成

```
# mkdir nodejs
# cd !$ ; pwd
# vi node
```

dockerfile:

```
FROM ubuntu

RUN apt-get update
RUN apt-get install -y curl
RUN # curl -sL https://deb.nodesource.com/setup_4.x | bash -
RUN apt-get install -y nodejs
```

build

```
書式: # docker image build -t {image_naem}[:{tag}] /path/to/Dockerfile_dir
# docker build -t node .
```

tagは指定しない場合latestになる

```
# docker images
```

### example

- wordpress  
DB用とウェブサーバの2つのコンテナを動かす

```
# docker run -d -e MYSQL_ROOT_PASSWORD=password --name db mysql
# docker run --link db:mysql -p 8080:80 -e WORDPRESS_DB_PASSWORD=password --name web wordpress
```

- options  
d バックグラウンドで実行  
e 環境変数  
name コンテナに名前付け(db)  

