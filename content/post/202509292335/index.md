+++
title = "hugoで静的サイト作ってみた"
date = "2025-09-29"
description = ""
tags = []
categories = []
series = ["hugo"]
+++

## インストール

```bash
brew install hugo
```

## サイト生成

```bash
hugo new site quickstart
```

## テーマ反映

使用したいテーマをサブモジュールとして追加

```bash
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

`hugo.toml` に下記を追加

```hugo.toml
theme = 'ananke'
```

## 記事の作成

```bash
hugo new content content/posts/my-first-post.md
```

## サーバ起動

```bash
hugo serve -D
```

## リンク

- [Quick start](https://gohugo.io/getting-started/quick-start/)
