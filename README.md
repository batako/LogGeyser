# Log Geyser

Hugo を用いた備忘録用のサイトです。

## 概要

- **ベースURL**: `https://log.batako.net/`
- **テーマ**: `themes/hugo-theme-stack`（Git サブモジュール）

## 必要要件

- Hugo

macOS（Homebrew）例:

```bash
brew install hugo
```

## セットアップ

クローン後、テーマのサブモジュールを初期化します。

```bash
git submodule update --init --recursive
```

## ローカル開発

ドラフト含めてローカルサーバーを起動します。

```bash
hugo server -D
```

- URL: `http://localhost:1313`

## コンテンツ作成

新規記事（日時ディレクトリ例）:

```bash
hugo new post/202509301045/index.md
```

フロントマター（TOML）例:

```toml
+++
title = "タイトル"
date = "2025-09-30"
description = ""
categories = []
tags = ["タグ"]
series = ["シリーズ名"]
+++
```

## ビルド

本番ビルド（最小化）:

```bash
hugo --minify
```

- 出力先: `public/`

## デプロイ

任意の静的ホスティング（GitHub Pages, Netlify 等）に `public/` を配置。
CI では `hugo --minify` を実行するだけで OK です。

## テーマの更新

テーマを最新に更新:

```bash
git submodule update --remote --merge themes/hugo-theme-stack
```

## ディレクトリ構成（抜粋）

- `content/`: 記事・固定ページ
- `assets/`: 画像など（例: `assets/img/avatar.png`）
- `public/`: ビルド出力
- `themes/hugo-theme-stack/`: テーマ（サブモジュール）
- `hugo.yaml`: サイト設定
