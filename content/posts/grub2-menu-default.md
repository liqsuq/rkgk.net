---
title: "UbuntuでGRUB2メニューが起動時に出る様にしておく"
date: 2022-04-01T10:57:01+09:00
categories:
  - "Linux"
tags:
  - "Linux"
  - "Ubuntu"
  - "GRUB2"
draft: false
---

(いつからかは覚えてないけど)最近のUbuntuは起動時にGRUB2ブートローダのメニュー画面が出なくなった。
普通に使っている分には起動画面がキレイになっていいんだけど、内部を弄くり倒している時に困る。
致命的なコンフィグミスをしてデフォルトカーネル及び起動パラメータで起動しなくなる→レスキューブートや別カーネルでブートしたいけどデフォルトだとLinuxカーネルに入る前で操作を受け付けない→詰み、みたいな。

<!--more-->

# 手順

/boot/grub/grub.cfg自動生成のテンプレートになっている/etc/default/grubを書き換える。デフォルトでは以下になっているはずなので、

```
:
GRUB_TIMEOUT_STYLE=hidden
GRUB_TIMEOUT=0
:
```

以下に書き換える。

```
:
GRUB_TIMEOUT_STYLE=menu
GRUB_TIMEOUT=5
:
```

そして変更を/boot/grub/grub.cfgに反映させる。

``` bash
$ sudo update-grub2
```

GRUB_TIMEOUTは任意の値。キーボード操作が無い場合にデフォルトのメニューエントリで起動するまでの時間。最悪1にしても起動後にShiftキー連打してればGRUB2メニューから遷移しなくなる。

