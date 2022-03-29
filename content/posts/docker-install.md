---
title: "Dockerインストール(Ubuntu)"
date: 2022-03-29T15:28:24+09:00
categories:
  - "メモ"
tags:
  - "Linux"
  - "Ubuntu"
  - "Docker"
draft: false
---

[公式のドキュメント](https://docs.docker.com/engine/install/)より簡単なスクリプトを公式が用意しているのでメモっとく。
~~このスクリプトをドキュメントで採用すればいいのでは?~~

<!--more-->

実行環境

- amd64計算機
- Ubuntu 20.04

前提コマンドのcurlをインストール・スクリプトをダウンロードして実行・現在のユーザをdockerグループに追加(rootless化は保留)・docker-composeのインストール

~~~
$ sudo apt update && sudo apt install curl
$ sudo wget https://get.docker.com -O -|sudo sh
$ sudo usermod -aG docker $USER
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
~~~
