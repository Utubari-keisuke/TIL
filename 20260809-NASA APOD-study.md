# NASA APOD Discord自動通知Bot開発とGitHub連携 (2026-08-09)

## 🌌 プロジェクト概要
NASAの「Astronomy Picture of the Day (APOD)」APIから毎日の天体写真と解説を取得し、Google翻訳APIを経由して日本語化した上で、Discordへ自動配信するRubyスクリプトを構築・公開した。

---

## 🛠 本日学んだこと・構築した技術スタック

### 1. RubyによるREST API連携とデータ処理
- **Net/HTTP & JSON**: 外部API（NASA APOD API / Google Translate API / Discord Webhook）との通信およびJSONデータのパース。
- **データ型による分岐処理**: APODのメディア形式（`image` / `video`）を判定し、Discord側のEmbed表示（画像組み込み / 動画プレビュー）を適切に切り替える処理の実装。
- **環境変数の利用 (`ENV`)**: APIキーやWebhook URLなどの機密情報をコード内に直書きせず、`ENV['NASA_API_KEY']` のように外部から注入してセキュリティを担保する構成への最適化。

### 2. Discord Webhook & Embedカードデザイン
- **Embed形式の活用**: テキスト直貼りではなく、タイトル・色・画像・折りたたみテキスト（800文字制限対応）を組み合わせた見やすいカード型の通知フォーマット構築。

### 3. macOS `cron` によるタスク自動化
- **パスの特定**: `which ruby` や `pwd` で環境に依存する絶対パスを確認。
- **crontab設定**: `crontab -e`（Vimエディタ）を使用し、`30 7 * * *` 形式で毎朝定時にスクリプトを全自動実行するスケジューリングの設定。

### 4. Git / GitHub によるバージョン管理と公開
- **ローカルリポジトリ構築**: `git init` から `git add`、`git commit` までの基本フロー。
- **GitHubへのプッシュ**: リモートリポジトリの設定（`git remote add origin`）とメインブランチへの送信。

### 5. GitHub Profiles & Markdown表現力の強化
- **画像サイズ・レイアウト調整**: Standard Markdownでは行えない画像サイズ変更や中央寄せを HTML（`<img width="..." align="center">`）でカスタマイズ。
- **Shields.io（バッジ）活用**: `for-the-badge` スタイルを用いて、使用技術（Ruby, Discord, NASA API, cron, 各種AI等）を統一感のあるデザインでアピールする記述方法の習得。
