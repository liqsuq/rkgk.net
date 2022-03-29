---
title: "Ubuntuの/media/以下のACLがたまにバグる"
date: 2022-03-29T14:55:11+09:00
categories:
  - "メモ"
tags:
  - "Linux"
  - "Ubuntu"
  - "ACL"
draft: false
---

今日び滅多にないのだがたまにインストーラがDVDのLinuxソフトウェアが存在する。
そしてそのインストーラが自分以外のユーザ(aptとか)でメディア内のファイルを実行する場合、UbuntuのデフォルトACLに引っかかってインストールが失敗する。
そのためACLを変更する必要があるのだが、なぜかたまにACLがバグってて変更出来なくなる。

<!--more-->

基本的にDVDを自動マウントするとユーザに合わせたディレクトリが作成され、ACLも設定される。

``` bash
$ getfacl /media/liqsuq/
getfacl: 絶対パス名から先頭の '/' を削除

# file: media/liqsuq/
# owner: root
# group: root
user::rwx
user:liqsuq:r-x
group::---
mask::r-x
other::---
```

これをグループもしくは他のユーザーでも実行可能にすれば良いので本来は以下のコマンドを実行すれば良い。

``` bash
$ sudo setfacl -m g::5,o::5 /media/*
```

しかし何回もUbuntuを再インストールしているとこんな事象に出くわす。見ての通り"user:liqsuq:r-x"行が複数存在しているせいでsetfaclコマンドが失敗している。

``` bash
$ sudo setfacl -m g::5,o::5 /media/*
[sudo] liqsuq のパスワード: 
setfacl: /media/liqsuq: Malformed access ACL 'user::rwx,user:liqsuq:r-x,user:liqsuq:r-x,group::r-x,mask::r-x,other::r-x': 重複エントリ at entry 3
$ getfacl /media/liqsuq/
getfacl: 絶対パス名から先頭の '/' を削除

# file: media/liqsuq/
# owner: root
# group: root
user::rwx
user:liqsuq:r-x
user:liqsuq:r-x
group::---
mask::r-x
other::---
```

これを修正するには以下を実行する

``` bash
$ sudo setfacl -x user:liqsuq /media/liqsuq
$ getfacl /media/liqsuq/
getfacl: 絶対パス名から先頭の '/' を削除

# file: media/liqsuq/
# owner: root
# group: root
user::rwx
user:liqsuq:r-x
group::---
mask::r-x
other::---

$ sudo setfacl -m g::5,o::5 /media/*
$ getfacl /media/liqsuq/
getfacl: 絶対パス名から先頭の '/' を削除

# file: media/liqsuq/
# owner: root
# group: root
user::rwx
user:liqsuq:r-x
group::r-x
mask::r-x
other::r-x
```

対処法は分かるが原因は不明。トリガーとなる操作も分からず本当に不定期で発生するのが気持ち悪い。