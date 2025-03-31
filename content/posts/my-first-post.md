+++
date = '2025-03-31T19:17:43+09:00'
draft = true
title = 'Hugoを導入したメモ'
+++

Hugo でブログ（ここ）を作ってみたのでメモ

### 環境

- WSL 2 (Ubuntu 24.04)

## 導入

- [ここ](https://gohugo.io/installation/linux/)を参考にする

いつも通り apt で入れようとしたが、ドキュメントをよく見ると

> The Hugo version available in package repositories varies based on Linux distribution and release, and in some cases will not be the latest version.
>
> Use one of the other installation methods if your package repository does not provide the desired version.

だった。
入れてみると、

```bash
$ apt list -a hugo
Listing... Done
hugo/noble-updates,noble-security,now 0.123.7-1ubuntu0.2 amd64 [installed]
hugo/noble 0.123.7-1build1 amd64
```

で古かったので、[Release](https://github.com/gohugoio/hugo/releases) から.deb を落とした。

### インストール

```bash
$ sudo apt install hugo_0.145.0_linux-amd64.deb
```

### ブログの建て方

```bash
$ hugo new site hoge --force
$ git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
$ echo "theme = 'ananke'" >> hugo.toml
$ hugo server
```

### Github Pages で使う

- https://gohugo.io/host-and-deploy/host-on-github-pages/
  - ここを参考にする
  - タイムゾーンを JST にしておく
  - baseURL も変えておく

### おわりに

- [いろんなテーマ](https://themes.gohugo.io/)があるらしい
  - フォントがあまり気に入ってないのでよさそうなものを探したい
  - or フォントだけ変えるか・・・
- Github Pages に建てるのは 2 回目なので続いたら別の環境で建ててみてもいいかもしれない
- ブログを建てたのは 4 年ぶりだったので続くかな
