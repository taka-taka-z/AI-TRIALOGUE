# AI TRIALOGUE

**Claude × ChatGPT × Gemini — 三者AI会談ツール**

3つのAI（Claude / ChatGPT / Gemini）がリアルタイムで議論する、ブラウザ完結型の対話ツールです。テーマを入力するだけで、異なるAIが独自の視点から意見を交わし、最終的に議論の総括レポートを生成します。

<p align="center">
  <img src="docs/social-preview.png" alt="AI TRIALOGUE" width="720">
</p>

## 特徴

- **三者同時議論** — Claude (Anthropic)・ChatGPT (OpenAI)・Gemini (Google) が1つのテーマについて議論
- **ラウンド制** — 3R / 4R / 5R / ∞（無限）から選択可能
- **ホストフィードバック** — 各ラウンド終了後に議論の方向性を指示・軌道修正
- **ホスト名設定** — 名前を入力すると、AIが「〇〇さん」として認識。総括レポートにも反映
- **自動総括** — 全ラウンド終了後、Claudeが議論の総括レポートを生成
- **議事録エクスポート** — 会談中いつでもMarkdown形式の議事録をダウンロード可能
- **完全ローカル** — APIキーはブラウザのメモリ上のみに保持。サーバー送信なし
- **単一HTMLファイル** — 依存なし。ブラウザで開くだけで動作

## クイックスタート

### 1. APIキーを取得

| AI | 取得先 | 使用モデル |
|---|---|---|
| Claude | [Anthropic Console](https://console.anthropic.com/) | claude-sonnet-4-20250514 |
| ChatGPT | [OpenAI Platform](https://platform.openai.com/) | gpt-4o |
| Gemini | [Google AI Studio](https://aistudio.google.com/) | gemini-2.5-flash |

### 2. ツールを開く

**方法A: GitHub Pages（推奨）**

👉 [https://YOUR_USERNAME.github.io/ai-trialogue/](https://taka-taka-z.github.io/ai-trialogue/)

> ※ `YOUR_USERNAME` をご自身のGitHubユーザー名に置き換えてください

**方法B: ローカル実行**

```bash
git clone https://github.com/YOUR_USERNAME/ai-trialogue.git
cd ai-trialogue
open index.html  # macOS
# または
start index.html  # Windows
```

### 3. 使い方

1. **CONFIG** をクリックし、3つのAPIキーを入力
2. **あなたの名前（Host）** 欄に名前を入力（省略可。入力すると議論中にAIが「〇〇さん」として認識します）
3. ステータスドットが3つとも点灯したら準備完了
4. テーマを入力して **START**
5. 各ラウンド後にフィードバック（任意）を入力、またはSkip
6. 全ラウンド終了後、自動で総括レポートが生成されます
7. 入力欄左の **ダウンロードボタン** で議事録（Markdown形式）をいつでもエクスポートできます

## アーキテクチャ

```
┌──────────────────────────────────────────────┐
│  ブラウザ（単一HTML）                         │
│  ┌─────────────────────────────────────────┐ │
│  │  UIレイヤー (HTML/CSS)                  │ │
│  │  - ダークテーマ / WCAG AA準拠           │ │
│  │  - タイプライターアニメーション          │ │
│  │  - レスポンシブ対応                     │ │
│  ├─────────────────────────────────────────┤ │
│  │  オーケストレーション (JavaScript)      │ │
│  │  - ラウンド管理                         │ │
│  │  - トランスクリプト蓄積                 │ │
│  │  - フィードバック注入                   │ │
│  ├─────────────────────────────────────────┤ │
│  │  APIアダプター                          │ │
│  │  - Anthropic Messages API               │ │
│  │  - OpenAI Chat Completions API          │ │
│  │  - Google Generative Language API       │ │
│  │  - 指数バックオフ付きリトライ           │ │
│  └─────────────────────────────────────────┘ │
└──────────────────────────────────────────────┘
         │              │              │
    Anthropic API   OpenAI API   Google AI API
    (Claude)        (ChatGPT)    (Gemini)
```

## セキュリティとプライバシー

- APIキーはブラウザの **JavaScript変数** としてのみ保持されます
- いかなるサーバーにも送信されません
- ページをリロード/閉じるとキーは消去されます
- 各AIのAPIエンドポイントに対してのみ直接通信します
- CORS制約により、Anthropic APIは `anthropic-dangerous-direct-browser-access` ヘッダーを使用しています

### ⚠ API経由のデータ送信に関する注意

本ツールでは、入力されたテーマや議論内容が **各AIプロバイダーのAPIエンドポイントに送信** されます。送信されたデータがモデルの学習に使用されるかどうかは、各社のAPIデータポリシーに依存します。

| プロバイダー | APIデータの学習利用 | ポリシー確認先 |
|---|---|---|
| Anthropic (Claude) | デフォルトで学習に不使用 | [API Data Usage](https://www.anthropic.com/policies) |
| OpenAI (ChatGPT) | APIデータはデフォルトで学習に不使用（2023年3月以降） | [API Data Usage](https://openai.com/policies/) |
| Google (Gemini) | 無料枠のAPIデータは学習に使用される場合あり | [Gemini API Terms](https://ai.google.dev/terms) |

**機密情報や個人情報を含むテーマでの利用は避けることを推奨します。** 各社のポリシーは変更される可能性があるため、利用前に最新の規約を確認してください。

## カスタマイズ

### 発言文字数の調整

`sysPrompt()` 関数内の以下を変更：

```javascript
'1回の発言は250〜400文字程度。'
```

### AIモデルの変更

各 `call*()` 関数内のモデル名を変更：

```javascript
// Claude
model: 'claude-sonnet-4-20250514'

// ChatGPT
model: 'gpt-4o'

// Gemini（URLに含まれる）
gemini-2.5-flash
```

### 総括レポートの構成変更

`runConversation()` 関数内の `summaryInstruction` を編集することで、総括の構成・項目をカスタマイズできます。

## 技術スタック

- **UI**: Vanilla HTML/CSS（フレームワーク不使用）
- **フォント**: [DM Sans](https://fonts.google.com/specimen/DM+Sans) + [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono)
- **API**: Anthropic Messages API / OpenAI Chat Completions / Google Generative Language API
- **デザイン**: ダークテーマ / Tinted Neutrals / WCAG AAコントラスト比準拠

## 動作環境

- モダンブラウザ（Chrome / Edge / Firefox / Safari の最新版）
- 各AIサービスの有効なAPIキーおよびクレジット

## 既知の制約

- ブラウザからの直接API呼び出しのため、CORSポリシーに依存します
- 企業プロキシ環境ではAPI通信がブロックされる場合があります（接続テスト機能で確認可能）
- APIの利用料金は各サービスの料金体系に従います

## ライセンス

[MIT License](LICENSE)

---

> *"3つのAIが会話に加わったら、何が起きるか？"*
