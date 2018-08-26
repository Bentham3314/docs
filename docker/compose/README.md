# docker-compose

- 起動/停止

```
# docker-compose up -d
# docker-compose down
```

- jenkins  

```
ホストのディレクトリをマウントする
volumes:
	- ./jenkins_home:/var/jenlins_home

同一docker-compose上の他コンテナ(slave01)の名前解決を可能に
links
	- slave01

環境変数の設定
enviroment
	- JENKINS_SLAVE_SSH_PUBKEY=ssh-ras ......
```

