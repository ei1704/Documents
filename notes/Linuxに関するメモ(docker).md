---
attachments: [Clipboard_2022-05-04-18-00-42.png, Clipboard_2022-05-04-18-06-03.png, Clipboard_2022-05-04-18-21-45.png]
tags: [Linux]
title: Linuxに関するメモ(docker)
created: '2022-05-04T08:36:55.710Z'
modified: '2022-05-05T05:28:58.970Z'
---

# Linuxに関するメモ(docker)
> https://www.youtube.com/watch?v=t6ykv1-Kvps

### user:ono pass:ntts3375

- dockerはサービスとして在中している
  つまりsystemctlで制御する(類似するものにserviceコマンドがある)
> https://sunadarake.github.io/qfr8l41pigu2v05ztwbc/

```
systemctl start docker
```

### ubuntuコンテナのデプロイ
```
docker run -it --name ubu1 ubuntu:bionic /bin/bash

[root@localhost ~]# docker run -it --name ubu1 ubuntu:bionic /bin/bash
[Unable to find image 'ubuntu:bionic' locally
Trying to pull repository docker.io/library/ubuntu ...
bionic: Pulling from docker.io/library/ubuntu
40dd5be53814: Pull complete
Digest: sha256:d21b6ba9e19feffa328cb3864316e6918e30acfd55e285b5d3df1d8ca3c7fd3f
Status: Downloaded newer image for docker.io/ubuntu:bionic
[root@e2d8edbedea9:/#
```
ホストOSであるcentosのカーネルを共有している

### コンテナの動作状況
- -aで終了されているコンテナの情報も表示
```
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
e2d8edbedea9        ubuntu:bionic       "/bin/bash"         2 minutes ago       Exited (0) 34 seconds ago                       ubu1
9048685508ae        hello-world         "/hello"            7 minutes ago       Exited (0) 7 minutes ago                        wonderful_darwin
```

### コンテナ作成、バックグラウンド実行
```
docker run -dit --name ubu2 ubuntu:bionic /bin/bash
```
- -dがそのオプションにあたる

### 裏で動くコンテナでコマンド実行
```
[root@localhost ~]# docker exec ubu2 cat /etc/os-release
NAME="Ubuntu"
VERSION="18.04.6 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.6 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic
```

### コンテナに入る
```
docker attach ubu2(NAME)
```
- コンテナ内ではCtrl+p Ctrl+qでデタッチ

### コンテナ削除
```
docker rm (NAME|ID(重複しない限り二桁まで短縮可))
```

### 停止中のコンテナを全削除
```
docker container prune
```

### インストールしたイメージと削除
```
[root@localhost ~]# docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
docker.io/ubuntu        bionic              c6ad7e71ba7d        4 days ago          63.2 MB
docker.io/hello-world   latest              feb5d9fea6a5        7 months ago        13.3 kB
[root@localhost ~]# docker rmi fe
Untagged: docker.io/hello-world:latest
Untagged: docker.io/hello-world@sha256:10d7d58d5ebd2a652f4d93fdd86da8f265f5318c6a73cc5b6a9798ff6d2b2e67
Deleted: sha256:feb5d9fea6a5e9606aa995e879d862b825965ba48de054caab5ef356dc6b3412
Deleted: sha256:e07ee1baac5fae6a26f30cabfe54a36d3402f96afda318fe0a96cec4ca393359
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/ubuntu    bionic              c6ad7e71ba7d        4 days ago          63.2 MB
```

### Docker Hubの公式apacheコンテナを複数動かす
```
docker run -d --name apa01 -p 8081:80 httpd
docker run -d --name apa02 -p 8082:80 httpd
```
ポート部分は（ホストOS:ゲストOS）という表記
![](@attachment/Clipboard_2022-05-04-18-00-42.png)

### コンテナ内にホストOSのファイルをコピーする
```
docker cp web1/index.html apa01:/usr/local/apache2/htdocs/
```
![](@attachment/Clipboard_2022-05-04-18-06-03.png)


------------------------------------------------------------------------
### dockerfile(カスタムコンテナイメージ)の作成
```
[root@localhost ~]# cat Dockerfile
FROM httpd
COPY index.html /usr/local/apache2/htdocs
```
#### dockerfileを使ってイメージ作成
```
[root@localhost ~]# docker build -t apaimg .
Sending build context to Docker daemon 99.93 MB
Step 1/2 : FROM httpd
 ---> c30a46771695
Step 2/2 : COPY index.html /usr/local/apache2/htdocs
 ---> 96f23d90d3c2
Removing intermediate container ba0b16cffbc5
Successfully built 96f23d90d3c2
[root@localhost ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
apaimg              latest              96f23d90d3c2        About a minute ago   144 MB
docker.io/ubuntu    bionic              c6ad7e71ba7d        4 days ago           63.2 MB
docker.io/httpd     latest              c30a46771695        13 days ago          144 MB
[root@localhost ~]# docker run -d --name apa03 -p 8083:80 apaimg
dd20ac7321542a4c9f981c0f6e763b77493456559969ba03028b5f3a74cc4e0b
```
![](@attachment/Clipboard_2022-05-04-18-21-45.png)



