# VSCode LM API Host 仕様書

## 概要

VSCode LM API Host は、Visual Studio Code の Language Model (LM) API を外部アプリケーションから利用可能にする拡張機能です。この拡張機能は、HTTP API サーバーを提供し、VSCode の Language Model 機能に外部からアクセスできるようにします。

### 主な機能

1. **API アクセス**
   - HTTP プロトコルを介したアクセス
   - OpenAI 互換の API エンドポイント
   - エンドポイントのバージョン管理

2. **サーバー機能**
   - 設定可能なホストとポート
   - セキュリティ設定（CORS、レート制限など）
   - ステータスモニタリング

3. **UI/UX 要素**
   - ステータスバー表示
   - サーバー制御用コマンド
   - 設定インターフェース

## 技術仕様

### アーキテクチャ

VSCode LM API Host は以下のコンポーネントで構成されます：

1. **VSCode 拡張機能コア**
   - 拡張機能のライフサイクル管理
   - コマンドとイベントハンドリング
   - VSCode との統合

2. **LM API サービス**
   - VSCode LM API へのインターフェース
   - モデル情報の取得と管理
   - リクエスト処理とレスポンス変換

3. **API サーバー**
   - HTTP リクエスト処理
   - エンドポイントルーティング
   - リクエスト検証とエラーハンドリング

### ビルドシステム（esbuild）

拡張機能の開発には esbuild を使用します：

- **選定理由**：
  - 高速なビルド時間
  - シンプルな設定
  - TypeScript のネイティブサポート
  - 軽量な出力バンドル

- **ビルド設定**：
  - エントリーポイント: src/extension.ts
  - 出力形式: CommonJS
  - 外部依存関係: vscode
  - ソースマップ生成
  - Tree-shaking と最小化

### APIサーバー（Fastify）

API サーバーの実装には Fastify を使用します：

- **選定理由**：
  - 高いパフォーマンス
  - 低いメモリフットプリント
  - スキーマベースのバリデーション
  - プラグインシステム

- **サーバー設定**：
  - デフォルトポート: 3000
  - デフォルトホスト: localhost
  - CORS 設定: 設定可能
  - コンテンツタイプ: application/json

### テスト駆動開発（TDD）アプローチ

開発プロセスにはテスト駆動開発アプローチを採用します：

- **基本原則**：
  - テストファースト：機能実装前にテストを作成
  - リファクタリング：テストが通った後にコードを改善
  - 小さなサイクル：テスト・実装・リファクタリングの短いサイクル

- **テスト構造**：
  - ユニットテスト：各モジュールの機能テスト
  - 統合テスト：モジュール間の連携テスト
  - エンドツーエンドテスト：API全体の動作確認

- **テストツール**：
  - Mochaテストフレームワーク
  - モッキングライブラリ
  - VSCode API テストユーティリティ

## API 仕様

### エンドポイント

#### 1. モデル一覧取得

```
GET /v1/models
```

**レスポンス**：

```json
{
  "object": "list",
  "data": [
    {
      "id": "gpt-4o",
      "object": "model",
      "created": 1677610602,
      "owned_by": "openai"
    },
    {
      "id": "claude-3-opus-20240229",
      "object": "model",
      "created": 1677649963,
      "owned_by": "anthropic"
    }
  ]
}
```

#### 2. チャット補完

```
POST /v1/chat/completions
```

**リクエスト**：

```json
{
  "model": "gpt-4o",
  "messages": [
    {
      "role": "system",
      "content": "あなたは役立つアシスタントです。"
    },
    {
      "role": "user",
      "content": "こんにちは！"
    }
  ],
  "temperature": 0.7,
  "max_tokens": 100
}
```

**レスポンス**：

```json
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1677652288,
  "model": "gpt-4o",
  "choices": [{
    "index": 0,
    "message": {
      "role": "assistant",
      "content": "こんにちは！お手伝いできることがあれば、お気軽にお尋ねください。"
    },
    "finish_reason": "stop"
  }]
}
```

### スキーマ定義

#### リクエストスキーマ

チャット補完リクエストのJSON Schemaを以下に示します：

```json
{
  "type": "object",
  "required": ["model", "messages"],
  "properties": {
    "model": {
      "type": "string",
      "description": "使用するモデルのID"
    },
    "messages": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["role", "content"],
        "properties": {
          "role": {
            "type": "string",
            "enum": ["system", "user", "assistant", "function"],
            "description": "メッセージの送信者のロール"
          },
          "content": {
            "type": "string",
            "description": "メッセージの内容"
          }
        }
      },
      "description": "会話履歴を表すメッセージの配列"
    },
    "temperature": {
      "type": "number",
      "minimum": 0,
      "maximum": 2,
      "default": 1,
      "description": "サンプリング温度（高いほどランダム性が増す）"
    },
    "max_tokens": {
      "type": "integer",
      "minimum": 1,
      "default": 1000,
      "description": "生成するトークンの最大数"
    },
    "stream": {
      "type": "boolean",
      "default": false,
      "description": "ストリーミングレスポンスを有効にするかどうか"
    }
  }
}
```

## 互換性要件

### VSCode バージョン
- Visual Studio Code 1.93.1以上をサポート

### OS サポート
- Windows
- macOS
- Linux

### Node.js 環境
- Node.js v22.12.0 以上
- npm v10.9.0 以上

## セキュリティ仕様

### アクセス制御

APIサーバーは以下のセキュリティ機能を備えています：

1. **ホスト制限**
   - デフォルトでは localhost のみにバインド
   - 設定で変更可能（ユーザーの責任で）

2. **CORS 設定**
   - デフォルトで同一オリジンのみ許可
   - オプションで CORS 設定をカスタマイズ可能

3. **レート制限**
   - 設定可能なリクエスト数の制限
   - 一定期間内のリクエスト数を制限

### データ保護

1. **ローカル処理**
   - すべてのデータはローカル環境内で処理
   - データは VSCode と LM プロバイダー間でのみ送信

2. **設定データ**
   - 設定はVSCodeの安全なストレージに保存
   - 機密情報は暗号化して保存

## 設定オプション

拡張機能は以下の設定オプションを提供します：

| 設定キー                                  | 説明                                             | デフォルト値             | 型      |
| ----------------------------------------- | ------------------------------------------------ | ------------------------ | ------- |
| `lmApiHost.server.enabled`                | サーバーを有効にするかどうか                     | `false`                  | boolean |
| `lmApiHost.server.port`                   | サーバーが使用するポート                         | `3000`                   | number  |
| `lmApiHost.server.host`                   | サーバーがバインドするホスト                     | `"localhost"`            | string  |
| `lmApiHost.server.autoStart`              | VSCode起動時にサーバーを自動的に起動するかどうか | `false`                  | boolean |
| `lmApiHost.security.cors.enabled`         | CORS を有効にするかどうか                        | `false`                  | boolean |
| `lmApiHost.security.cors.origin`          | 許可するオリジン                                 | `["http://localhost:*"]` | array   |
| `lmApiHost.security.rateLimit.enabled`    | レート制限を有効にするかどうか                   | `true`                   | boolean |
| `lmApiHost.security.rateLimit.max`        | 時間枠あたりの最大リクエスト数                   | `100`                    | number  |
| `lmApiHost.security.rateLimit.timeWindow` | レート制限の時間枠（ミリ秒）                     | `60000`                  | number  |
| `lmApiHost.models.preferred`              | 優先的に使用するモデル                           | `"gpt-4o"`               | string  |
| `lmApiHost.logging.level`                 | ログレベル                                       | `"info"`                 | string  |

## パフォーマンス要件

- **メモリ使用量**: 最大 100MB
- **CPU 使用率**: アイドル時 <1%、リクエスト処理時 <10%
- **起動時間**: <2秒
- **リクエスト処理時間**: <100ms（LMによる生成時間を除く）

## ユーザーエクスペリエンス

### コマンド

拡張機能は以下のコマンドを提供します：

1. `lmApiHost.startServer`: サーバーを起動
2. `lmApiHost.stopServer`: サーバーを停止
3. `lmApiHost.restartServer`: サーバーを再起動
4. `lmApiHost.showStatus`: サーバーステータスを表示
5. `lmApiHost.openSettings`: 拡張機能の設定を開く

### ステータス表示

ステータスバーには以下の情報が表示されます：

- サーバーの実行状態（実行中/停止中）
- 使用しているポート番号
- 現在選択されているモデル

### エラー通知

エラーが発生した場合は、以下の方法で通知します：

- VSCode の通知 UI によるエラーメッセージ
- ステータスバーの視覚的なインジケーター
- 詳細なエラーログ
