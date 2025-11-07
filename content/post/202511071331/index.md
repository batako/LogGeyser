---
title: React Native Skia Sprite Animator — スプライト再生 × Expo FS 保存
description: React Native/Expo で Skia 上にスプライトシートを高速再生し、画像＋JSON を Expo FileSystem に保存できる最小ユーティリティの紹介と使い方。
date: 2025-11-07
slug: react-native-skia-sprite-animator
image: thumbs/webdev.webp
categories:
  - Web開発
tags:
  - react-native
  - expo
  - skia
  - sprite
series:
---

React Native/Expo プロジェクトで、Skia 上にスプライトシートを高速に描画できるコンポーネントと、Expo FileSystem への保存ユーティリティを提供するライブラリを作りました。

- リポジトリ: https://github.com/batako/react-native-skia-sprite-animator
- npm: `react-native-skia-sprite-animator`

UI は同梱せず「再生」「保存」の最小限に特化しているため、各アプリのデザインやエディタ UI に柔軟に組み込めます。

## 特長

- Skia Canvas での高速スプライト再生（`SpriteAnimator`）
- フレームごとの `duration`（ミリ秒）や `fps`/`loop`/`onEnd` をサポート
- Expo FileSystem ベースの保存ユーティリティ（`spriteStorage`）
- TypeScript/ESM 出力、React 19 & RN >= 0.81 対応
- Jest で主要挙動を自動テスト、CI（GitHub Actions）連携

## インストール

```bash
npm install react-native-skia-sprite-animator
# 依存ライブラリ（Expo 環境の場合）
npx expo install @shopify/react-native-skia expo-file-system
```

対応バージョン:

- React Native >= 0.81（Expo SDK 52 相当）
- React 19
- @shopify/react-native-skia >= 2.3

## クイックスタート（再生）

64×64 のフレームが横に 7 コマ並んだスプライトシートを再生する例です。

```tsx
import React from "react";
import { View } from "react-native";
import { SpriteAnimator } from "react-native-skia-sprite-animator";
// 画像は任意のアセットに置き換え
import spriteSheet from "../assets/piyo-sprite.png";

const frames = Array.from({ length: 7 }).map((_, i) => ({
  x: i * 64,
  y: 0,
  w: 64,
  h: 64,
}));

export function PiyoSprite() {
  return (
    <View style={{ width: 64, height: 64 }}>
      <SpriteAnimator
        image={spriteSheet}                // require(...) / import asset / SkImage をサポート
        data={{ frames }}                  // { x, y, w, h, duration? }[]
        fps={12}                           // duration 未指定フレームのデフォルト速度
        loop                               // ループ再生
        onEnd={() => console.log("Animation finished")}
        spriteScale={1}                    // 倍率。描画サイズ = w * scale, h * scale
        style={{ width: 64, height: 64 }}  // Canvas サイズ
      />
    </View>
  );
}
```

- `duration` を各フレームに指定すると、そのフレームだけミリ秒指定で再生できます（例: `{duration: 80}`）。
- ライブラリ内部では Skia `Group` のクリッピングと画像の平行移動で切り出しを行うため、任意のスプライトシートに対応します。

## 画像＋JSON の保存（spriteStorage）

アプリ内で生成したフレームやメタデータを保存・読み込みするユーティリティです。保存先:

- `.../sprites/images/`（PNG 等）
- `.../sprites/meta/`（JSON）
- `.../sprites/registry.json`（一覧）

```ts
import {
  saveSprite,
  loadSprite,
  listSprites,
  deleteSprite,
  configureSpriteStorage,
  getSpriteStoragePaths,
  clearSpriteStorage,
  type SpriteSavePayload,
} from "react-native-skia-sprite-animator";

// 任意のフレーム配列
const frames = [{ x: 0, y: 0, w: 64, h: 64 }, { x: 64, y: 0, w: 64, h: 64 }];

// 保存
const payload: SpriteSavePayload = {
  frames,
  meta: { displayName: "Piyo Walk", version: 1 },
  // 追加の任意フィールドもメタにマージ可能
};
const saved = await saveSprite({
  imageTempUri: "file:///path/to/temp.png", // 一時保存された画像URI
  sprite: payload,
});

// 一覧・読み込み・削除
const items = await listSprites();
const full = await loadSprite(saved.id);
await deleteSprite(saved.id);

// 保存パスを知りたい場合
const paths = getSpriteStoragePaths();
// { root, images, meta, registry }

// ルートをカスタムしたい場合
configureSpriteStorage({ rootDir: "file:///custom-root/" });

// すべて削除（テスト・リセット用）
await clearSpriteStorage();
```

## API 概要

### <SpriteAnimator />

Props:

- `image`: `require()`/バンドルアセット/SkImage
- `data`: `{ frames: {x,y,w,h,duration?}[] }`
- `fps?`: number（`duration`未指定フレームのデフォルト速度）
- `loop?`: boolean（false で最終フレームで停止＋`onEnd`一回）
- `autoplay?`: boolean（デフォルト true）
- `spriteScale?`: number（描画倍率）
- `style?`: Canvas サイズ指定
- `onEnd?`: () => void

### spriteStorage

- `saveSprite({ imageTempUri, sprite })` → 画像を `images/` にコピーし JSON を `meta/` に保存。`registry.json` を更新。
- `loadSprite(id)` → JSON を読み込み（存在しなければ `null`）。
- `listSprites()` → `registry.json` から一覧を取得。
- `deleteSprite(id)` → 画像と JSON を削除しレジストリを更新。
- `configureSpriteStorage({ rootDir? })` → 保存ルートを変更（標準は `documentDirectory/sprites/`）。
- `getSpriteStoragePaths()` → 各ディレクトリ/ファイルの実パスを返す。
- `clearSpriteStorage()` → 保存ツリーを丸ごと削除（テスト用）。

## よくある質問（トラブルシューティング）

- 「Invalid hook call」が出る
  本パッケージは ESM 出力かつ React を外部参照しています。npm から導入する限り React の二重取り込みは起きません。もし Git 参照やローカルリンクで使う場合、`node_modules/react-native-skia-sprite-animator/node_modules/react` が存在しないか確認してください（通常は存在しません）。

- Expo/Skia のバージョン要件は？
  React Native >= 0.81、React 19、@shopify/react-native-skia >= 2.3、Expo SDK 52 相当で動作確認済み。

- 速度が足りない/コマ落ちする
  Sprite シートの画像解像度を抑える、Canvas サイズと `spriteScale` を用途に合わせて調整、フレーム数を適正化するなどを試してください。

## フレーム自動生成 Tips

規則的な分割であればフレーム配列は簡単に生成できます。

```ts
function buildGridFrames(cols: number, rows: number, w: number, h: number) {
  const frames = [];
  for (let y = 0; y < rows; y++) {
    for (let x = 0; x < cols; x++) {
      frames.push({ x: x * w, y: y * h, w, h });
    }
  }
  return frames;
}

// 例: 横7×縦1、64px
const frames = buildGridFrames(7, 1, 64, 64);
```

## ライセンス / クレジット

- License: MIT
- Author: batako (https://batako.net)
- Contributors: Batako Studio (https://github.com/BatakoStudio)

## リンク

- GitHub: https://github.com/batako/react-native-skia-sprite-animator
- npm: https://www.npmjs.com/package/react-native-skia-sprite-animator

---

使い方や API で不明点があれば Issue/PR お待ちしています。
