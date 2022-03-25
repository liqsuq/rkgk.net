---
title: "CentOSで動いてたシェルスクリプトがUbuntuで動かない"
date: 2021-04-15T09:34:24+09:00
categories:
  - "Linux"
tags:
  - "Linux"
  - "CentOS"
  - "Ubuntu"
draft: false
---

誰かが作ったCentOS用のシェルスクリプトを「まあsh準拠なら動くやろ」と思って気軽に動かした時に気づいたけど、CentOSはシバンを`#!/bin/sh`にしてても実際にはbashが動いてる。試しに`ls -l`してみればshはbashにシンボリックリンクされていることが分かる。

一方でUbuntuは6.10以降shをdash(Debian Almquist shell)にシンボリックリンクしているそうだ。dashはashのDebian派生らしく、POSIXに準拠している。

これで何が怖いかっていうとCentOSで軽い気持ちでshをシバンにしてシェルスクリプトを組んで、動作することを以て正常性を確認すると気付かぬ内にbash拡張やらを盛り込んで互換性を大きく損なっているということ。もっともUbuntuでもdashへの変更当時はbash拡張使用してるのにおまじない感覚で`#!/bin/sh`打ち込んでる人が多くて騒がれたみたいだけど。具体的にはこういう構文が動かなくなる。

``` bash
function test{
    ...
}
```

解決方法としてはシバンを書きかえるか、`sudo dpkg-reconfigure dash`を実行して/bin/shのシンボリックリンクをbashに変えるか、単に一発実行すればいいなら`./xxxx.sh`じゃなくて`bash xxxx.sh`にすれば動きそう。

確かにshを意図したスクリプトがbashで動いてもまず問題ないけど、RHEL系で作成されたshを呼ぶ類が全部POSIX準拠か信用ならなくなるのでは…?どう考えてもRHEL系がshを想定したスクリプトでbashを呼ぶガバい挙動してるのが悪い気がする。