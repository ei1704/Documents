---
tags: [Linux]
title: Linuxに関するメモ(php・mysql環境構築)
created: '2022-06-15T12:25:46.876Z'
modified: '2022-06-15T15:09:33.726Z'
---

# Linuxに関するメモ(php・mysql環境構築)
- 実行環境:CentOS7

> 参考
> https://haloechoes.com/category/server-operations/home-server/

## PHPのインストール

```
yum -y install php php-mbstring php-mysqli
```

> php-mbstringはphpでマルチバイトを使えるようにするモジュール

## Mysqlのインストール

1. リポジトリを追加する
>https://dev.mysql.com/downloads/repo/yum/
```
yum localinstall --skip-broken https://dev.mysql.com/get/mysql80-community-release-el7-6.noarch.rpm
```
- 確認
```
[root@localhost ~]# cd /etc/yum.repos.d
[root@localhost yum.repos.d]# ls
CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-Sources.repo  CentOS-fasttrack.repo      mysql-community-source.repo
CentOS-CR.repo    CentOS-Media.repo      CentOS-Vault.repo    CentOS-x86_64-kernel.repo  mysql-community.repo
```

2. MySQLのインストール
```
yum install -y mysql-community-server
.
~省略~
.
GPG 鍵の取得に失敗しました: [Errno 14] curl#37 - "Couldn't open file /etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022"
```
- GPGキー取得
```
rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
```

- 再インストール、確認
```
mysqld --version
/usr/sbin/mysqld  Ver 8.0.29 for Linux on x86_64 (MySQL Community Server - GPL)
```

- rootユーザの初期パスワードを見る
```
cat /var/log/mysqld.log | grep password
```
- 初期設定コマンド
```
mysql_secure_installation
```

- ログイン
```
[root@localhost ~]# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 15
Server version: 8.0.29 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)
```



