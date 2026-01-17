# 操作マニュアル 📖

このドキュメントでは、動画の背景、キャラクター、セリフ、テロップ（字幕）をカスタマイズする方法を説明します。

## 1. 動画制作の基本的な流れ

動画制作は、主に以下の4ステップで行います。

1.  **台本を編集する**
    - `transcripts/firstvideo.tsx` ファイルを開き、セリフやキャラクターの表情などを編集します。
2.  **音声ファイルを生成する**
    - ターミナルで以下のコマンドを実行し、台本に合わせた音声ファイルを自動生成します。
      ```bash
      npm run generate:yukkuri-voice:force
      ```
3.  **プレビューで確認する**
    - 以下のコマンドでプレビューサーバーを起動し、ブラウザで変更内容をリアルタイムに確認します。
      ```bash
      npm start
      ```
4.  **動画を書き出す**
    - 最終的な動画ファイルを生成します。
      ```bash
      npm run build
      ```
    - `out/video.mp4` に動画ファイルが出力されます。

---

## 2. セリフの変更方法

セリフや会話の流れは、すべて `transcripts/firstvideo.tsx` ファイルで管理します。

ファイル内の `talks` という配列に、会話のデータが格納されています。

```typescript
// transcripts/firstvideo.tsx

export const FirstVideoConfig: VideoConfig = {
  sections: [{
    // ...
    talks: [
      {
        text: "ねえねえ魔理沙", // 表示するセリフ
        speaker: "reimu",     // 話すキャラクター
        face: "kirakira",     // キャラクターの表情 (任意)
        // ...
      },
      {
        text: "なんだぜ霊夢",
        speaker: "marisa",
        // ...
      },
    ],
    // ...
  }],
};
```

### `talks` の主な設定項目

-   `text`: 画面に表示され、音声が生成されるセリフです。
-   `speaker`: 話すキャラクターを指定します。`reimu` や `marisa` など、`src/yukkuri/yukkuriVideoConfig.ts` の `SPEAKER` で定義されている名前を使います。
-   `face`: キャラクターの表情を指定します。`kirakira` や `angry` など。指定できる表情はキャラクターの画像ファイルに依存します。
-   `customDuration`: セリフの表示時間を手動で調整したい場合（フレーム数）に使います。

---

## 3. キャラクターの変更・追加方法

### 既存キャラクターに変更する

`transcripts/firstvideo.tsx` の `talks` 内で、`speaker` の値を変更します。

```typescript
{
  text: "これは魔理沙のセリフだぜ",
  speaker: "marisa", // "reimu" から "marisa" に変更
}
```

### 新しいキャラクターを追加する

新しいキャラクターを追加するには、画像ファイルの準備と設定の追加が必要です。

**ステップ1: 画像ファイルの準備**

1.  `public/` ディレクトリに、新しいキャラクター用のフォルダを作成します（例: `public/sanae/`）。
2.  作成したフォルダ内に、以下の4つのサブフォルダを作成します。
    -   `body` (体)
    -   `eye` (目)
    -   `mouth` (口)
    -   `face` (表情差分、例: 青ざめ、汗など)
3.  各フォルダに、連番のPNG画像（`00.png`, `01.png`, ...）を配置します。既存の `reimu` や `marisa` のフォルダ構成を参考にしてください。

**ステップ2: 設定ファイルにキャラクターを追加**

1.  `src/yukkuri/yukkuriVideoConfig.ts` を開きます。
2.  `SPEAKER` オブジェクトに、新しいキャラクターの名前を追加します。

```typescript
// src/yukkuri/yukkuriVideoConfig.ts

export const SPEAKER = {
  reimu: 'reimu',
  marisa: 'marisa',
  leon: 'leon',
  sanae: 'sanae', // ← 新しいキャラクターを追加
  reimuAndMarisa: 'reimuAndMarisa',
} as const;
```

**ステップ3: キャラクターを描画するコンポーネントの修正**

キャラクターの描画ロジックは `src/yukkuri/YukkuriSequence.tsx` にあります。新しいキャラクターの画像パスなどを解決するために、このファイルの修正が必要になる場合があります。

---

## 4. 背景の変更方法

### 動画を背景にする

`transcripts/firstvideo.tsx` の `backgroundVideo` に、使用したい動画ファイルのパスを指定します。動画ファイルは `public/video/` ディレクトリに配置してください。

```typescript
// transcripts/firstvideo.tsx

export const FirstVideoConfig: VideoConfig = {
  sections: [{
    title: "背景テスト",
    backgroundVideo: "/video/my-custom-background.mp4", // ← ここで指定
    // ...
  }],
};
```

### 静止画を背景にする

静止画を背景として使いたい場合は、`src/YukkuriVideo.tsx` の修正が必要です。

1.  `public/image/` などに背景画像を配置します。
2.  `src/YukkuriVideo.tsx` を開き、`LoopedOffthreadVideo` コンポーネントを `Img` コンポーネントに置き換えます。

**変更前:**
```typescript
{section.backgroundVideo && (
  <LoopedOffthreadVideo
    isMuted
    src={staticFile(section.backgroundVideo)}
  />
)}
```

**変更後:**
```typescript
{section.backgroundVideo && ( // ここは backgroundVideo を backgroundImage などに変えるとより分かりやすい
  <Img
    src={staticFile(section.backgroundImage)} // publicからのパスを指定
    style={{ width: '100%', height: '100%' }}
  />
)}
```
*この修正に合わせて、`transcripts/firstvideo.tsx` で指定するプロパティも変更する必要があります。*

---

## 5. テロップ（字幕）のカスタマイズ

### 文字のスタイルを変更する

字幕のフォント、色、大きさ、縁取りなどは `src/Subtitle/Subtitle.tsx` で定義されています。

このファイルの `subtitle` オブジェクトのCSSプロパティを編集することで、デザインを変更できます。

```typescript
// src/Subtitle/Subtitle.tsx

const subtitle: React.CSSProperties = {
  fontFamily: 'GenshinGothic', // フォント
  fontSize: 64,               // 文字サイズ
  fontWeight: '900',
  color: '#fff',              // 文字色
  WebkitTextStroke: '4px #C90003', // 縁取り (太さ 色)
  // ...
};
```

話者によって縁取りの色を変えたい場合は、`colorBySpeaker` オブジェクトを編集します。

```typescript
const colorBySpeaker = {
  [SPEAKER.reimu]: '4px #C90003',  // 霊夢の色
  [SPEAKER.marisa]: '4px #D3BB02', // 魔理沙の色
  // ...
};
```

### テロップの背景デザインを変更する

字幕が表示されるエリアの背景は `src/YukkuriVideo.tsx` の `jimakuBackground` で設定されています。

デフォルトでは `Cyber_telop2_black.png` という画像が使われています。

```typescript
// src/YukkuriVideo.tsx

const jimakuBackground: React.CSSProperties = {
  // ...
  backgroundImage: `url(${staticFile('image/telop/Cyber_telop2_black.png')})`,
  // ...
};
```

この `backgroundImage` を変更したり、`backgroundColor` を設定したりすることで、テロップの背景を自由に変更できます。

**例：半透明の黒い背景にする**
```typescript
const jimakuBackground: React.CSSProperties = {
  // ...
  // backgroundImage: `url(...)`, // 画像指定をコメントアウト
  backgroundColor: 'rgba(0, 0, 0, 0.7)', // 背景色を指定
  // ...
};
```
