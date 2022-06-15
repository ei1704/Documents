---
tags: [Linux]
title: Linuxに関するメモ(ブートローダ・systemd)
created: '2022-05-05T04:47:06.632Z'
modified: '2022-05-05T05:27:32.470Z'
---

# Linuxに関するメモ(ブートローダ・systemd)
> https://www.youtube.com/watch?v=eVyW3UkYxFw

## ブートローダ
> カーネルを選択して起動するプログラム

## カーネル
> Linuxの中心部を構成するプログラム
>> CPU,メモリ,ストレージなどのリソースを制御する

## initramfs
> システム起動用の小規模ファイルシステム
>> ルートファイルシステムをルートディレクトリにマウントする

## systemd
> システム起動時最初に生成されるプロセス、デーモンとしてメモリに常駐しシステムを統括(PIDは1)
>> ・ルートファイルシステムがマウントされたらカーネルはsystemdを読みだす
>> ・systemdは設定ファイルに従ってシステムの初期化とサービスの起動を行う
>> ・systemdの設定ファイルはユニットと呼ばれ12タイプある
>>> service,target,socket,device,mount,automount,swap,path,timer,snapshot,slice,scope
## ユニットの例
|unit|働き|
|----|-----|
|service|サービスの起動と停止|
|target|ユニットをグループ化したもの、システムの起動状態を制御|

## default.target
>　システム起動時、systemdはdefault.targetの設定に従ってシステムを起動する
>> 一般的にはgraphical.targetまたはmulti-user.taregetへのシンボリックリンクである

|ターゲット|起動モード|システムの状態|initのランレベル|
|---------|---------|-------------|---------------|
|**graphical.target**|**グラフィカルモード**|マルチユーザ+GUI|5|
|**multi-user.target**|**マルチユーザモード**|マルチユーザ+CUI|3|
|rescue.target|レスキューモード|シングルユーザ(rootのみ)|1|
|poweroff.target|電源オフ|システム停止|0|
|reboot.target|システム再起動||6|

### ・systemdを使ってシステムを管理するにはsystemctlコマンドを使用する
全てのactiveユニットを表示
```
systemctl
```

ロードされている全てのユニットを表示
```
systemctl -a
```

## デフォルトターゲットの確認とマルチユーザモードへの変更
```
[root@localhost ~]# systemctl get-default
graphical.target
[root@localhost ~]# systemctl set-default multi-user.target
Removed symlink /etc/systemd/system/default.target.
Created symlink from /etc/systemd/system/default.target to /usr/lib/systemd/system/multi-user.target.
[root@localhost ~]# systemctl get-default
multi-user.target
[root@localhost ~]# ls -l /etc/systemd/system/default.target
lrwxrwxrwx. 1 root root 41  5月  5 14:22 /etc/systemd/system/default.target -> /usr/lib/systemd/system/multi-user.target
```
- 上記の設定で再起動を行うとCUIで起動する
