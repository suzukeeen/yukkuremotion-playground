# yukkuremotion プロジェクト構成図

このドキュメントでは、Remotionを使ったゆっくり動画制作プロジェクトの構成をMermaidダイアグラムで視覚化します。

## 📊 1. 全体アーキテクチャ

```mermaid
graph TB
    subgraph "開発者"
        A[台本作成<br/>transcripts/firstvideo.tsx]
    end
    
    subgraph "音声生成フェーズ"
        B[generateYukkuriVoiceFiles.ts]
        C[AquesTalk10<br/>音声合成エンジン]
        D[AqKanji2Koe<br/>漢字→音声変換]
        E[音声ファイル<br/>public/audio/yukkuri/*.wav]
    end
    
    subgraph "レンダリングフェーズ"
        F[Root.tsx<br/>Remotion エントリ]
        G[YukkuriVideo.tsx<br/>メイン動画コンポーネント]
        H[YukkuriSequence.tsx<br/>キャラクター表示]
        I[TalkSequence.tsx<br/>会話シーケンス]
        J[Subtitle.tsx<br/>字幕表示]
        K[YukkuriFace.tsx<br/>表情制御]
    end
    
    subgraph "静的アセット"
        L[BGM<br/>public/audio/bgm/]
        M[画像<br/>public/image/]
        N[動画<br/>public/video/]
    end
    
    subgraph "出力"
        O[最終動画<br/>out/video.mp4]
    end
    
    A -->|台本| B
    B --> C
    B --> D
    C --> E
    D --> E
    
    A -->|設定| F
    E -->|音声| G
    L -->|BGM| G
    M -->|画像| G
    N -->|背景動画| G
    
    F --> G
    G --> H
    G --> I
    I --> J
    H --> K
    
    G -->|Remotion Render| O
    
    style A fill:#e1f5ff
    style B fill:#fff3cd
    style F fill:#d4edda
    style O fill:#f8d7da
```

## 🔄 2. データフロー図

```mermaid
flowchart LR
    subgraph "入力"
        A1[台本テキスト]
        A2[話者設定]
        A3[表情・口の形]
    end
    
    subgraph "変換処理"
        B1[AqKanji2Koe]
        B2[読み変換]
        B3[AquesTalk10]
        B4[音声合成]
    end
    
    subgraph "生成物"
        C1[WAV音声<br/>ID.wav]
        C2[音声時間<br/>audioDurationFrames]
    end
    
    subgraph "動画合成"
        D1[音声再生]
        D2[口パク制御]
        D3[表情表示]
        D4[字幕表示]
        D5[BGM再生]
    end
    
    subgraph "出力"
        E1[動画フレーム]
    end
    
    A1 --> B1
    B1 --> B2
    B2 --> B3
    B3 --> B4
    B4 --> C1
    B4 --> C2
    
    A2 --> D1
    A3 --> D2
    A3 --> D3
    
    C1 --> D1
    C2 --> D2
    A1 --> D4
    
    D1 --> E1
    D2 --> E1
    D3 --> E1
    D4 --> E1
    D5 --> E1
    
    style A1 fill:#e1f5ff
    style C1 fill:#fff3cd
    style E1 fill:#d4edda
```

## 📁 3. ディレクトリ構造

```mermaid
graph TD
    ROOT[yukkuremotion-playground]
    
    ROOT --> PUBLIC[public/]
    ROOT --> SRC[src/]
    ROOT --> SCRIPTS[scripts/]
    ROOT --> TRANSCRIPTS[transcripts/]
    ROOT --> VENDOR[vendor/]
    
    PUBLIC --> AUDIO[audio/]
    PUBLIC --> IMAGE[image/]
    PUBLIC --> VIDEO[video/]
    
    AUDIO --> BGM[bgm/<br/>BGMファイル]
    AUDIO --> YUKKURI[yukkuri/<br/>音声ファイル]
    AUDIO --> SOZAI[sozai/<br/>効果音]
    
    IMAGE --> TELOP[telop/<br/>テロップ画像]
    IMAGE --> IMGS[各種画像素材]
    
    VIDEO --> VIDS[背景動画素材]
    
    SRC --> COMPONENTS[components/<br/>共通コンポーネント]
    SRC --> YUKKURI_COMP[yukkuri/<br/>ゆっくり関連]
    SRC --> SUBTITLE[Subtitle/<br/>字幕コンポーネント]
    SRC --> UTILS[utils/<br/>ユーティリティ]
    
    YUKKURI_COMP --> FACE[Face/<br/>表情管理]
    YUKKURI_COMP --> TALK[Talk/<br/>会話シーケンス]
    
    SCRIPTS --> GEN_AUDIO[generateYukkuriAudios.ts<br/>音声生成]
    SCRIPTS --> GEN_VOICE[generateYukkuriVoiceFiles.ts<br/>音声ファイル生成]
    SCRIPTS --> CHECK_BGM[checkBgmFiles.ts<br/>BGMチェック]
    SCRIPTS --> KEYS[aquest-keys.ts<br/>ライセンスキー]
    
    TRANSCRIPTS --> CONFIG[firstvideo.tsx<br/>動画設定・台本]
    TRANSCRIPTS --> CUSTOM[CustomObjects.tsx<br/>カスタムオブジェクト]
    
    VENDOR --> AQUEST[AquesTalk.framework]
    VENDOR --> KANJI[AqKanji2Koe.framework]
    VENDOR --> DIC[aq_dic_large/]
    
    style ROOT fill:#e1f5ff
    style PUBLIC fill:#fff3cd
    style SRC fill:#d4edda
    style SCRIPTS fill:#f8d7da
    style TRANSCRIPTS fill:#d1ecf1
```

## 🎬 4. Remotion コンポーネント階層

```mermaid
graph TD
    A[Root.tsx<br/>Remotionエントリポイント]
    B[Composition<br/>FirstVideo]
    C[YukkuriVideo.tsx<br/>メインビデオ]
    
    D[Section Loop<br/>各セクション処理]
    
    E1[Background<br/>背景動画/BGM]
    E2[TalkSequence<br/>会話シーケンス]
    E3[YukkuriSequence<br/>キャラクター表示]
    E4[Transition Movies<br/>前後の動画]
    
    F1[Audio<br/>BGM再生]
    F2[LoopedOffthreadVideo<br/>背景動画ループ]
    
    G1[Subtitle<br/>字幕表示]
    G2[SubtitleBackground<br/>字幕背景]
    G3[Audio<br/>音声再生]
    
    H1[YukkuriFace<br/>霊夢の顔]
    H2[YukkuriFace<br/>魔理沙の顔]
    
    I1[Body<br/>体]
    I2[Face<br/>表情]
    I3[Eye<br/>目/瞬き]
    I4[Mouth<br/>口/口パク]
    
    A --> B
    B --> C
    C --> D
    
    D --> E1
    D --> E2
    D --> E3
    D --> E4
    
    E1 --> F1
    E1 --> F2
    
    E2 --> G1
    E2 --> G2
    E2 --> G3
    
    E3 --> H1
    E3 --> H2
    
    H1 --> I1
    H1 --> I2
    H1 --> I3
    H1 --> I4
    
    H2 --> I1
    H2 --> I2
    H2 --> I3
    H2 --> I4
    
    style A fill:#e1f5ff
    style C fill:#fff3cd
    style E2 fill:#d4edda
    style E3 fill:#f8d7da
```

## ⚙️ 5. 音声生成プロセス

```mermaid
sequenceDiagram
    participant Dev as 開発者
    participant Script as generateYukkuriVoiceFiles.ts
    participant Config as firstvideo.tsx
    participant Kanji as AqKanji2Koe
    participant TTS as AquesTalk10
    participant FS as ファイルシステム
    
    Dev->>Config: 台本を編集
    Dev->>Script: npm run generate:yukkuri-voice:force
    
    Script->>Config: VideoConfig を読み込み
    
    loop 各セクション
        loop 各会話
            Script->>Kanji: テキストを音声用に変換
            Kanji-->>Script: 読み仮名テキスト
            
            alt 霊夢&魔理沙同時
                Script->>TTS: 霊夢の声で合成
                TTS-->>Script: 音声バイナリ(霊夢)
                Script->>FS: 霊夢の音声保存
                
                Script->>TTS: 魔理沙の声で合成
                TTS-->>Script: 音声バイナリ(魔理沙)
                Script->>FS: 魔理沙の音声保存
            else 通常の会話
                Script->>TTS: 指定話者の声で合成
                TTS-->>Script: 音声バイナリ
                Script->>FS: 音声保存
            end
            
            Script->>Script: ID生成して設定に追加
        end
    end
    
    Script->>Config: 更新された設定を書き込み
    Script-->>Dev: 音声生成完了
```

## 🎨 6. レンダリングプロセス

```mermaid
sequenceDiagram
    participant Dev as 開発者
    participant Remotion as Remotion CLI
    participant Video as YukkuriVideo
    participant Seq as TalkSequence
    participant Face as YukkuriFace
    participant Audio as Audio Component
    
    Dev->>Remotion: npm start (プレビュー)
    
    Remotion->>Video: VideoConfig を渡す
    
    loop 各フレーム
        Video->>Video: 現在のフレーム位置を計算
        
        alt BGM再生フレーム
            Video->>Audio: BGM再生
        end
        
        alt 背景動画
            Video->>Video: ループ背景動画表示
        end
        
        Video->>Seq: 会話シーケンス描画
        
        alt 話している場合
            Seq->>Audio: 音声再生
            Seq->>Seq: 字幕表示
        end
        
        Video->>Face: 霊夢の顔描画
        Face->>Face: 表情選択
        Face->>Face: 口パク計算
        Face->>Face: 瞬き処理
        
        Video->>Face: 魔理沙の顔描画
        Face->>Face: 表情選択
        Face->>Face: 口パク計算
        Face->>Face: 瞬き処理
        
        alt トランジション動画
            Video->>Video: 前後の動画表示
        end
    end
    
    Remotion-->>Dev: プレビュー表示
    
    Dev->>Remotion: npm run build (レンダリング)
    Remotion-->>Dev: out/video.mp4
```

## 🔧 7. 主要スクリプトと役割

```mermaid
mindmap
  root((Scripts))
    音声生成
      generateYukkuriVoiceFiles.ts
        台本から音声ファイル生成
        IDの自動付与
      generateYukkuriAudios.ts
        AquesTalk10呼び出し
        声質パラメータ設定
      generateYukkuriForTuning.ts
        調整用音声生成
    メタデータ生成
      generateFromFramesMap.tsx
        フレームマップ生成
      generateFaceFrameMap.ts
        表情フレーム計算
      generateMouthFrameMap.ts
        口パクフレーム計算
      addAudioDurations.ts
        音声時間情報追加
    ユーティリティ
      checkBgmFiles.ts
        BGMファイル確認
      aquest-keys.ts
        ライセンスキー管理
    テスト
      make_voice.mjs
        音声生成テスト
```

## 🎭 8. キャラクター（ゆっくり）の構成

```mermaid
graph LR
    subgraph "YukkuriFace コンポーネント"
        A[YukkuriFace]
        B[Body<br/>体画像]
        C[Face<br/>表情画像]
        D[Eye<br/>目画像]
        E[Mouth<br/>口画像]
    end
    
    subgraph "画像素材"
        F1[public/reimu/body/00.png]
        F2[public/reimu/face/10-43.png]
        F3[public/reimu/eye/00-30.png]
        F4[public/reimu/mouth/00-30.png]
        
        G1[public/marisa/body/00.png]
        G2[public/marisa/face/10-46.png]
        G3[public/marisa/eye/00-31.png]
        G4[public/marisa/mouth/00-27.png]
    end
    
    subgraph "制御パラメータ"
        H1[frame<br/>現在のフレーム]
        H2[face<br/>表情ID]
        H3[mouth<br/>口の形ID]
        H4[kuchipakuMap<br/>口パクデータ]
    end
    
    A --> B
    A --> C
    A --> D
    A --> E
    
    F1 --> B
    F2 --> C
    F3 --> D
    F4 --> E
    
    G1 --> B
    G2 --> C
    G3 --> D
    G4 --> E
    
    H1 --> A
    H2 --> C
    H3 --> E
    H4 --> E
    
    style A fill:#e1f5ff
    style H1 fill:#fff3cd
    style F1 fill:#d4edda
```

## 📝 9. VideoConfig データ構造

```mermaid
classDiagram
    class VideoConfig {
        +sections: Section[]
    }
    
    class Section {
        +title: string
        +bgmSrc: string
        +backgroundVideo: string
        +afterMovie: string
        +talks: Talk[]
        +totalFrames: number
        +fromFramesMap: object
        +reimuKuchipakuMap: KuchipakuMap
        +marisaKuchipakuMap: KuchipakuMap
    }
    
    class Talk {
        +text: string
        +speaker: string
        +id: string
        +audioDurationFrames: number
        +face?: string
        +mouth?: string
        +customDuration?: number
        +image?: ImageConfig
        +audio?: AudioConfig
    }
    
    class KuchipakuMap {
        +frames: number[]
        +amplitude: number[]
    }
    
    class ImageConfig {
        +src: string
        +backgroundColor?: string
    }
    
    class AudioConfig {
        +src: string
        +from?: number
        +volume?: number
    }
    
    VideoConfig "1" --> "*" Section
    Section "1" --> "*" Talk
    Section "1" --> "1" KuchipakuMap
    Talk "1" --> "0..1" ImageConfig
    Talk "1" --> "0..1" AudioConfig
```

## 🚀 10. 開発・ビルドフロー

```mermaid
flowchart TD
    A[開始] --> B{何をする?}
    
    B -->|台本編集| C[transcripts/firstvideo.tsx を編集]
    B -->|BGM追加| D[public/audio/bgm/ に配置]
    B -->|画像追加| E[public/image/ に配置]
    B -->|動画追加| F[public/video/ に配置]
    
    C --> G[npm run generate:yukkuri-voice:force]
    G --> H[音声ファイル生成]
    
    D --> I[npm run check:bgm]
    I --> J{全て配置済み?}
    J -->|No| D
    J -->|Yes| K[YukkuriVideo.tsx のBGMコメント解除]
    
    E --> L[テロップ画像配置確認]
    L --> M{配置済み?}
    M -->|No| E
    M -->|Yes| N[YukkuriVideo.tsx のテロップコメント解除]
    
    F --> O[動画素材配置確認]
    
    H --> P[npm start]
    K --> P
    N --> P
    O --> P
    
    P --> Q[プレビュー確認]
    Q --> R{OK?}
    
    R -->|修正必要| B
    R -->|OK| S[npm run build]
    
    S --> T[out/video.mp4 生成]
    T --> U[完成！]
    
    style A fill:#e1f5ff
    style U fill:#d4edda
    style R fill:#fff3cd
```

---

## 📚 図の説明

### 1. 全体アーキテクチャ
プロジェクト全体の構成と各フェーズの関係を示します。

### 2. データフロー
テキストから音声、そして動画フレームまでのデータの流れを示します。

### 3. ディレクトリ構造
プロジェクトのファイル・ディレクトリの階層構造を示します。

### 4. Remotion コンポーネント階層
React/Remotionコンポーネントの親子関係を示します。

### 5. 音声生成プロセス
音声ファイル生成のシーケンスを時系列で示します。

### 6. レンダリングプロセス
動画レンダリング時の処理フローを時系列で示します。

### 7. 主要スクリプトと役割
scripts/ ディレクトリ内の各スクリプトの役割を整理します。

### 8. キャラクター構成
ゆっくりキャラクターの画像レイヤー構造を示します。

### 9. VideoConfig データ構造
設定データのクラス図を示します。

### 10. 開発・ビルドフロー
開発からビルドまでの作業フローを示します。

---

## 🔗 関連ドキュメント

- [QUICKSTART.md](QUICKSTART.md) - クイックスタートガイド
- [SETUP_GUIDE.md](SETUP_GUIDE.md) - セットアップガイド
- [BGM_DOWNLOAD_GUIDE.md](BGM_DOWNLOAD_GUIDE.md) - BGMダウンロード手順
- [MAKE_VOICE_GUIDE.md](MAKE_VOICE_GUIDE.md) - 音声生成テストガイド

