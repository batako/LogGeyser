---
title: Giscusを使ってGitHub Discussionsでコメント機能を実装する
description:
date: 2025-10-03
slug:
image: thumbs/webdev.webp
categories:
  - Web開発
tags:
  - hugo
  - github
series:
---

このサイトはhugoで[hugo-theme-stack](https://github.com/CaiJimmy/hugo-theme-stack?tab=readme-ov-file)テンプレート使って作ってるんだけど、コメント機能を簡単に追加できそうだったので追加してみた。

候補は色々あったけど、簡単で無料で追加できるGiscusを選択。GithubのDiscussions上にコメントしていく形式のもの。Issuesに作成するUtterancesってのもある。

このサイトもgithubで管理していて、全部githubで完結するからこれが一番良さそうだった。

Githubのアカウント持ってないとコメントできないからコメントのハードルがあがるけど、日々のログをただ残しているだけだから別に良いかな🙄

---

## 前提条件

- Hugo サイトを hugo-theme-stack で構築済み
- コメントを保存する GitHub リポジトリを用意済み（例: `username/blog`）

## Step 1. GitHub Discussions を有効化

1. コメントを保存したい GitHub リポジトリにアクセス
2. **Settings → Features → Discussions** にチェックを入れて有効化

## Step 2. Giscus App をインストール

- 👉 [Giscus GitHub App](https://github.com/apps/giscus) を開く
- 「Install」から対象リポジトリにインストールする

これでサイトから Discussions にスレッドを作成できるようになります。

## Step 3. Giscus 設定を取得

1. 👉 [giscus.app](https://giscus.app) にアクセス
2. 対象リポジトリを入力
3. 「Discussion カテゴリ」を選択（例: `Comments`）
4. 下に出てきたコードから **repoID** と **categoryID** をコピー

## Step 4. Hugo 設定ファイルに追記

`config.yaml` （または `config.toml`）にコメント設定を追加します。

```yaml
params:
  comments:
    enabled: true
    provider: giscus

    giscus:
      repo: batako/LogGeyser
      repoID: R_kgDOP4-ueg # giscus.appで取得
      category: Comments
      categoryID: DIC_kwDOP4-ues4CwMIE # giscus.appで取得
      mapping: title
      lightTheme: light
      darkTheme: dark
      reactionsEnabled: 1
      emitMetadata: 0
