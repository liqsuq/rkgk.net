---
title: "ROS2メモ"
date: 2022-03-28T15:45:15+09:00
categories:
  - "Memo"
tags:
  - "Linux"
  - "Ubuntu"
  - "ROS2"
draft: false
---

ROS2に関する作業中のあれこれをメモしておく。

<!--more-->

# 追加のパッケージのインストール
ROS2 Galacticのインストールにcolconとrosdepが言及されていないので自分のソースのビルドとかが出来ない。以前のドキュメントには書かれていたはずだけど…

colconとrosdepのインストール/セットアップ手順。

```
$ sudo apt install python3-rosdep python3-colcon-common-extensions
$ sudo rosdep init
$ rosdep update
```

# rosdepが失敗する
Cubicを使う等してOS名を変更していた場合、既存のOS名に該当しないため`rosdep update`が失敗することがある。

```
rospkg.os_detect.OSNotDetected: Could not detect OS, tried ['zorin', ...]
```

/etc/os-releaseを元のリリースに戻すことで解決する。
もしくは自前のカスタムOSをサポート済みOSにしてもらえる様にコミュニティ活動する。
