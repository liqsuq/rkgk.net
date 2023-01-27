---
title: "よく使うコマンドのメモ"
date: 2022-11-01T16:00:12+09:00
categories:
  - "Memo"
tags:
  - "Linux"
  - "Ubuntu"
  - "Bash"
draft: true
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

``` bash
$ df -hT -x squashfs -x tmpfs -x devtmpfs
```

# debパッケージ関連

(インストールした)パッケージに含まれるファイルを表示。

``` bash
$ dpkg -L vim
/.
/usr
/usr/bin
/usr/bin/vim.basic
/usr/share
/usr/share/bug
/usr/share/bug/vim
/usr/share/bug/vim/presubj
/usr/share/bug/vim/script
/usr/share/doc
/usr/share/doc/vim
/usr/share/doc/vim/NEWS.Debian.gz
/usr/share/doc/vim/changelog.Debian.gz
/usr/share/doc/vim/copyright
/usr/share/lintian
/usr/share/lintian/overrides
/usr/share/lintian/overrides/vim
```

逆にパス上のファイルをインストールしたパッケージの表示。

``` bash
$ dpkg -S /usr/bin/vim.basic 
vim: /usr/bin/vim.basic
```

debファイルをディレクトリに展開。
`ar x`でも似たようなことが出来るけどディレクトリ作って展開するからいくらか楽。

``` bash
$ dpkg -deb -R vim_2%3a8.1.2269-1ubuntu5.9_amd64.deb vim
$ ls
vim vim_2%3a8.1.2269-1ubuntu5.9_amd64.deb
```

# 開発系

gccの定義済みマクロの表示

``` bash
$ echo | gcc -dM -E -
#define __SSP_STRONG__ 3
#define __DBL_MIN_EXP__ (-1021)
#define __FLT32X_MAX_EXP__ 1024
#define __UINT_LEAST16_MAX__ 0xffff
:
```


随時追記予定

