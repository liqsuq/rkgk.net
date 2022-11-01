---
title: "よく使うコマンドのメモ"
date: 2022-11-01T16:00:12+09:00
categories:
  - "Memo"
tags:
  - "Linux"
  - "Ubuntu"
  - "Bash"
draft: false
---

よく使うけどよく忘れるコマンドのメモ。
エイリアス登録していても常に自分の環境を触る訳でも無いので…

<!--more-->

# 一般系

catしたファイルをクリップボードにコピー。
vimで競技プログラミングしてる人とかは便利だと思う。

``` bash
$ cat test.cpp | xsel --clipboard --input
```

逆にクリップボードの内容をファイルにリダイレクト。

``` bash
$ xsel --clipboard --output > $1
```

dfコマンド打っても最近のUbuntuだとSnapの関係で/dev/loop\*とかがたくさんあって分かりづらいので仮想ファイルシステムを除外してついでに見やすく。

```
$ df -hT -x squashfs -x tmpfs -x devtmpfs
```

# debパッケージ関連

そのうち書く

