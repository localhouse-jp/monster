# 🚆 Monster Timetable

次世代の公共交通時刻表表示システム。Raspberry Pi × balenaCloud で実現する、スマートな駅・バス停情報ディスプレイ。

## ✨ 特徴

- **リアルタイム発車時刻表示** - 秒単位でカウントダウン
- **マルチ交通機関対応** - 近鉄電車、JR、近鉄バスを統合
- **スマートスケジューリング** - 平日・休日を自動判別
- **Raspberry Pi最適化** - fbdevドライバで直接描画、軽快動作
- **balenaCloud対応** - リモート管理・自動アップデート

## 🏗️ アーキテクチャ

```
monster-timetable/
├── api/          # 🌐 APIサーバー (Bun + Hono)
├── app/          # 📺 表示アプリ (Tauri + React)
└── xeyes/        # 🔧 X11リファレンス実装
```

### APIサーバー (`/api`)
高速なBunランタイムで動作する時刻表データAPI。Webスクレイピングで最新情報を取得し、1時間キャッシュで効率化。

### ディスプレイアプリ (`/app`)
X11サーバー統合型のTauriアプリケーション。1コンテナで完結する設計により、シンプルかつ安定した動作を実現。

## 🚀 クイックスタート

### ローカル開発

```bash
# APIサーバー
cd api
bun install
bun run dev

# ディスプレイアプリ
cd app
bun install
npm run tauri dev
```

### Docker Compose

```bash
docker-compose up -d
```

## 🔧 設定

### 環境変数

| 変数名 | 説明 | デフォルト |
|--------|------|------------|
| `VITE_API_BASE_URL` | APIサーバーURL | `http://api:3000` |
| `ROTATE_DISPLAY` | 画面回転 | `normal` |
| `WINDOW_SIZE` | ウィンドウサイズ | `1920x1080` |

### 時刻表データソース
`api/config.json`で各交通機関のスクレイピング対象URLを設定。

## 📱 balenaCloudデプロイ

1. balenaCloudアカウントを作成
2. Raspberry Pi用アプリケーションを作成
3. このリポジトリをプッシュ

```bash
git remote add balena <your-balena-git-url>
git push balena main
```

## 🛠️ 開発コマンド

### API開発
```bash
bun run dev          # 開発サーバー起動
bun run debug:dev    # デバッグモード
bun run build        # プロダクションビルド
```

### アプリ開発
```bash
npm run dev          # フロントエンド開発
npm run tauri dev    # Tauriアプリ開発
npm run tauri build  # リリースビルド
```

## 🎯 使用例

- 駅構内の発車案内表示
- バス停の時刻表ディスプレイ
- オフィス・学校の交通情報モニター
- 商業施設の案内表示

## 📄 ライセンス

このプロジェクトはMITライセンスのもとで公開されています。

---

Built with ❤️ for public transportation lovers