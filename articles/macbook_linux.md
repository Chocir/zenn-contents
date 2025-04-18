---
title: "Macbook pro 2017にArchLinuxをいれる"
emoji: "🙌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["linux","macbook"]
published: false
---


## はじめに

開発のデバッグやサブ機がほしかったため

参考にした記事：

calloc134様
[Manjaro Linuxでしあわせ環境を構築する](https://zenn.dev/calloc134/articles/0a9eae7c8df0d3)

MacBookPro14
- CPU: Intel i7-7660U
- GPU: Intel Iris Plus Graphics
- メモリ: 16GB
- ストレージ: 512GB

## 問題

基本は記事通り。
　
しかし音が出ず、困る。
色々ライバーなどを設定してみてもうまく行かず。
GUI上で設定->パッケージ管理からアップデートをすると音が出るようになった。

### Atcoderの環境構築

どうやら環境を汚さないためにPEP886というのに準拠して、

- グローバルにパッケージをインストールする時はpacman
- 対応してなかった場合は仮想環境かpipxを使え

とりあえずpythonを導入（python3.12系）

online-judge-toolsがdistutilsに依存しており、3.12系では削除されているため動かない


setuptoolsを導入、
online-judge-toolsを導入

仮想環境と相性が悪いため、PEP668を無視してゴリ押した