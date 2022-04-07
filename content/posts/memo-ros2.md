---
title: "ROS2メモ"
date: 2022-03-28T15:45:15+09:00
categories:
  - "メモ"
tags:
  - "Linux"
  - "Ubuntu"
  - "ROS2"
draft: true
---

ROS2に関する作業中のあれこれをメモしておく。

<!--more-->

# 追加のパッケージのインストール
ROS2 Galacticのインストールにおいて、2022/03/08確認時点ではパッケージが足りずドキュメント通りにやってもチュートリアルが実行できない。

まずは以前のドキュメントには書かれていたはずのcolconとrosdepのインストール手順。

```
$ sudo apt install python3-rosdep python3-colcon-common-extensions
$ sudo rosdep init
$ rosdep update
```

更にシミュレータ周りで追加のパッケージが必要になるが、これはROS2のバージョン依存。

```
$ sudo apt install ros-galactic-ros-ign-bridge ros-galactic-webots-ros2-driver
```

# rosdepが失敗する
Cubicを使う等してOS名を変更していた場合、既存のOS名に該当しないため`rosdep update`が失敗することがある。

```
rospkg.os_detect.OSNotDetected: Could not detect OS, tried ['zorin', ...]
```

/etc/os-releaseを元のリリースに戻すことで解決する。
さもなければ自前のカスタムOSをサポート済みOSにしてもらえる様にコミュニティ活動すること。