---
title: "ROS2インストール済みだとdo-release-upgradeが失敗する"
date: 2022-04-04T14:39:11+09:00
categories:
  - "Linux"
tags:
  - "Linux"
  - "Ubuntu"
  - ""
draft: true
---

ROS2はUbuntuのリリースに厳密に紐ついている上にパッケージ名でROS2のバージョンを区分けしているから、ROS2が入ったままUbuntuを18.04から20.04にアップグレードしようとすると失敗する。
最近はdo-release-upgrade実行時に分かりやすい警告が出るようになったが結局は一時的にROS2をアンインスコする他ない。

<!--more-->

ROS2はリリースバージョン毎にサポートされるOSを厳密に定義しており(https://www.ros.org/reps/rep-2000.html)、初期(Crystal以前)を除き基本的にサポートされるUbuntuリリースは1つとなっている。
EloquentまではUbuntu 18.04のみサポートし、以降Galacticまでは20.04のみをサポートといった具合。
従ってUbuntuを18.04から20.04にアップグレードしようとするとここが問題になる。

> [ROSの入ったUbuntu16.04LTSを18.04LTSにアップデート | Dream Drive !!](https://dream-drive.net/2019/05/15/9217/)
> [PCのubuntu18.04LTS+ROS melodic環境をubuntu20.04LTS+ROS noetic環境にアップグレードする - Qiita](https://qiita.com/porizou1/items/2558ba1f55e2bd35800b)

ならaptの機能を活かして自動でROS2パッケージ群をアップグレードしてくれれば良いじゃんと思うが、ROS2はパッケージをros-eloquent-*の様なROS2リリース毎の接頭辞でパッケージを命名しているのでそれも難しい模様。

最近は次の様なエラーで失敗する様になった。
以前は不明瞭なエラーで失敗したはず。UbuntuはROS2を推進している様子なのでそれの一環か。

``` bash
:
The Robot Operating System (ROS) is installed 

It appears that ROS is currently installed. Each ROS release is very 
strict about the versions of Ubuntu it supports, and Ubuntu upgrades 
can fail if that guidance isn't followed. Before continuing, please 
either uninstall ROS, or ensure the ROS release you have installed 
supports the version of Ubuntu to which you're upgrading. 

For ROS 1 releases, refer to REP 3: 
https://www.ros.org/reps/rep-0003.html 

For ROS 2 releases, refer to REP 2000: 
https://www.ros.org/reps/rep-2000.html 
:
```

結局ROS2をアンインストールするしか無い訳だが、肝心のアンインストール方法はROS2公式ドキュメントには無い。まぁクロージングがおざなりなのはOSSではいつもことか。とりあえず以下を実行してdo-release-upgradeは通った。その後で20.04に対応したROS2をインストール出来る様に/etc/apt/sources.list.d/内の*.listファイルを編集して入れ直せば問題はない。

``` bash
$ sudo apt remove ros-eloquent-*
$ sudo apt autoremove
```

autoremoveでROS2インストール時の色々な依存ファイルは削除されるはず。