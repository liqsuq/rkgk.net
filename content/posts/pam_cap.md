---
title: "pam_capで設定した内容がgnome-terminalに反映されない"
date: 2022-03-31T14:07:23+09:00
categories:
  - "メモ"
tags:
  - "Linux"
  - "Ubuntu"
  - "systemd"
  - "PAM"
draft: false
---

なんかpam_cap.so使って/etc/security/capability.conf設定して、非特権ユーザーに権限与えてもGUIの端末(gnome-terminal)だけ権限が落ちる事象に出くわしたのでメモ。

<!--more-->

# 状況

リアルタイムアプリケーションは頻繁にIPCやリアルタイムプロセス優先度やら低レベルインタフェースやら触るけど、Ubuntuの場合は毎回sudoしなきゃならない。
rootの権限全付与は危険だし非特権ユーザーにリアルタイム開発で必要な権限だけ付与するのは最低限の配慮として行っておきたい。

※CentOSで開発する場合はたいがいrootでログインしてる。今後怒られが発生するんじゃないかなあ。

幸いUbuntuにはlibpam-capがデフォルトで入っているので/etc/security/capability.confを編集するだけで終わる。以下ではデフォルトの`none  *`の上にエントリを追加している。
```
cap_ipc_lock,cap_sys_rawio,cap_sys_admin,cap_sys_nice,cap_sys_resource  liqsuq
none  *
```

…と思ったがなぜかgnome-terminalで設定が反映されない。
Ctrl+Alt+F3の仮想ターミナルや`su $(id -u)`で自分自身にスイッチしたら動くんだけど。

調べてみるとgnome-terminalはgnome-terminal-serverというサービスの下でシェルを起動していて、gnome-terminal-serverはsystemdのユーザインスタンス(systemd --user)の配下にいる。
そしてこのsystemd --userはPAMの設定もあるがケーパビリティが上手く継承されていない模様。(というよりも自分達でケーパビリティをコントロールしたいのでpam_capと干渉してる?)

# 環境

``` bash
$ lsb_release -ds
Ubuntu 20.04.4 LTS
$ uname -r
5.13.0-37-generic
```

# 作業内容
## /lib/systemd/system/user@.serviceを改変
```
$ sudo mkdir -p /etc/systemd/system/user@$(id -u).service.d
$ echo -e "[Service]\nAmbientCapabilities=CAP_IPC_LOCK CAP_SYS_RAWIO CAP_SYS_ADMIN CAP_SYS_NICE CAP_SYS_RESOURCE" | sudo tee /etc/systemd/system/user@$(id -u).service > /dev/null
```

## /usr/lib/systemd/user/gnome-terminal-server.serviceを改変
[Service]に以下の行を追加する。
```
AmbientCapabilities=CAP_IPC_LOCK CAP_SYS_RAWIO CAP_SYS_ADMIN CAP_SYS_NICE CAP_SYS_RESOURCE
```

…とやってみたがこうすると目当てのユーザでは上手くいくんだけどケーパビリティを設定していないユーザではgnome-terminalが起動できなくなってしまう。目当てのユーザーでsystemd --user実行した時には親のケーパビリティを引き継げるがその他のユーザーでは割り当てがないので、権限を拡張する動作になりEPERMするのかと思う。

``` 
... systemd[14039]: gnome-terminal-server.service: Failed to apply ambient capabilities (before UID change): Operation not permitted
```

お手上げ。バイナリにsetcapでケーパビリティ付与するかsetuidでrootユーザで実行するしかないかなあ。

# 参考
[SystemdのCapabilityBoundingSet/AmbientCapabilitiesの挙動](https://gloryof.hatenablog.com/entry/2018/10/27/104711)  
[Linux Capability - ケーパビリティについての整理](https://udzura.hatenablog.jp/entry/2016/06/24/181852)  
[明日使えない Linux の capabilities の話](https://nojima.hatenablog.com/entry/2016/12/03/000000)  
[Linux Kernel Capability(Linuxカーネルケーパビリティ)に関して(2020年版) - Part1](https://security.sios.com/security/os-db/capability-info-20200701.html)  
[PAM - ArchWiki](https://wiki.archlinux.jp/index.php/PAM)