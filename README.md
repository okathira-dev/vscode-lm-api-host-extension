# VSCode LM API Host

**このプロジェクトは開発中です。このファイルの内容は事実とは限りません。**

VSCode LM API Hostは、Visual Studio Codeの言語モデル（Language Model）APIを外部アプリケーションから利用可能にする拡張機能です。HTTP APIサーバーを提供し、VSCodeのLanguage Model機能に外部からアクセスできるようにします。

## 機能

- **OpenAI互換API**：OpenAI APIと互換性のあるエンドポイントを提供
- **複数モデルサポート**：VSCodeで利用可能な言語モデルへのアクセス
- **ステータス管理**：稼働状況をステータスバーで確認可能
- **設定カスタマイズ**：ポート、ホスト、セキュリティ設定などをカスタマイズ可能

## インストール

- VSCode Marketplaceからインストール（公開後）
- または `.vsix` ファイルからローカルインストール

## 要件

- Visual Studio Code 1.93.1以上
- Node.js v22.12.0以上（推奨）
- インターネット接続（言語モデルへのアクセスに必要）

## 使い方

1. コマンドパレット（Ctrl+Shift+P / Cmd+Shift+P）を開く
2. `LM API Host: Start Server` を実行
3. 外部アプリケーションから `http://localhost:3000` （デフォルト）にアクセス

## API エンドポイント

- **GET /v1/models** - 利用可能な言語モデルの一覧取得
- **POST /v1/chat/completions** - チャット補完リクエスト

## 拡張機能の設定

この拡張機能は以下の設定項目を提供します：

- `lmApiHost.server.port`: APIサーバーのポート番号（デフォルト: 3000）
- `lmApiHost.server.host`: ホスト設定（デフォルト: localhost）
- `lmApiHost.server.cors`: CORS設定の有効/無効
- `lmApiHost.security.enabled`: セキュリティ制限の有効/無効

## 開発情報

- 言語：TypeScript
- ビルドツール：esbuild
- APIサーバー：Fastify
- テスト：Mocha

## リリースノート

### 0.0.1（開発中）

- 初期開発版
- 基本的なプロジェクト構造の構築
- テスト環境のセットアップ

---

## 開発者向け情報

### プロジェクトのセットアップ

```bash
# 依存関係のインストール
npm install

# 開発モードでの実行
npm run watch
```

### コマンド

- `npm run compile` - 拡張機能のビルド
- `npm run watch` - 変更を監視して自動ビルド
- `npm run test` - テストの実行
- `npm run vsce:package` - VSIXパッケージの作成

### 貢献方法

イシューやプルリクエストを歓迎します。開発に参加する際は以下の点に注意してください：

1. コードの変更には対応するテストを含める
2. コミットメッセージは明確に
3. プルリクエスト前にテストが通ることを確認

## ライセンス

未定
