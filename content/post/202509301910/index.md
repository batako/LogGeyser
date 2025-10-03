---
title: GithubとAmplifyを連携して自動で静的サイトを公開
description:
date: 2025-09-30
slug:
image: thumbs/webdev.webp
categories:
  - Web開発
tags:
  - amplify
  - aws
  - hugo
series:
---

## 静的サイトを作成してGithubにプッシュする

下記は hugo を使って作成した。

https://github.com/batako/batako.net

## Amplify からアプリを作成

### AWSのAmplifyのページに遷移し「新しいアプリを作成」を押下

![Amplifyでアプリ作成ステップ１](amplify-step1.webp)

### GitプロバイダーからGithubを選択

Githubへのアクセス許可を与える必要があるので適宜選択。
![Amplifyでアプリ作成ステップ２](amplify-step2.webp)

### リポジトリとブランチを選択

選択したブランチにプッシュすると自動でデプロイされる。
![Amplifyでアプリ作成ステップ３](amplify-step3.webp)

### ビルドコマンドの設定と公開するディレクトリを指定

hugoを使用した例
![Amplifyでアプリ作成ステップ４](amplify-step4.webp)

### 設定内容の確認

![Amplifyでアプリ作成ステップ５](amplify-step5.webp)

## カスタムドメインの割り当て

### カスタムドメインページに遷移

作成したアプリの概要ページから「カスタムドメインの追加」ボタンを押下する。
![カスタムドメインステップ１](domain-step1.webp)

### ドメイン追加ページに遷移

「ドメインを追加」ボタンを押下する。
![カスタムドメインステップ２](domain-step2.webp)

### 割り当てたいドメインを入力

![カスタムドメインステップ３](domain-step3.webp)

### サブドメインの設定

カスタムSSL証明書では「Amplifyマネージド証明書」を選択すると楽かも。

「ドメインを追加」ボタンを押下して反映されるまで待機する。
![カスタムドメインステップ４](domain-step4.webp)
