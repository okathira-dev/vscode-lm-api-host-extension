# VSCode LM API Host 拡張機能ユーザーガイド

## 概要

VSCode LM API Host 拡張機能は、VSCodeのLanguage Model APIへの外部アクセスを提供する拡張機能です。これにより、他のアプリケーションからVSCodeのLLM機能を利用することができます。

## インストール要件

- Visual Studio Code 1.93.1以上
- Node.js v22.12.0 以上
- npm v10.9.0 以上
- 有効なGitHub Copilotサブスクリプション

## インストール手順

1. VSCodeの拡張機能マーケットプレイスから「LM API Host」を検索
2. 「インストール」ボタンをクリック
3. VSCodeを再起動して拡張機能を有効化

または、VSIXファイルからの手動インストール:

```powershell
code --install-extension lm-api-host-0.1.0.vsix
```

## 使用方法

### APIサーバーの起動

1. VSCodeのコマンドパレットを開く（`Ctrl+Shift+P` または `Cmd+Shift+P`）
2. 「LM API Host: サーバーを起動」コマンドを実行
3. ステータスバーにサーバーのステータスとポート情報が表示されます

### APIエンドポイント

拡張機能は以下のAPIエンドポイントを提供します：

#### モデル一覧の取得

```
GET http://localhost:3000/v1/models
```

レスポンス例:

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

#### チャット補完

```
POST http://localhost:3000/v1/chat/completions
```

リクエスト例:

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
  ]
}
```

レスポンス例:

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

### 設定オプション

VSCodeの設定から以下のオプションを変更できます：

- `lmApiHost.server.port`: APIサーバーが使用するポート（デフォルト: 3000）
- `lmApiHost.server.host`: サーバーがバインドするホスト（デフォルト: localhost）
- `lmApiHost.server.autoStart`: VSCode起動時に自動的にサーバーを起動（デフォルト: false）
- `lmApiHost.models.preferred`: 優先的に使用するモデル（デフォルト: gpt-4o）

## トラブルシューティング

### サーバーが起動しない場合

1. ポートが既に使用されていないか確認してください
2. 別のポートを設定で指定してみてください
3. VSCodeを管理者/sudoモードで実行してみてください

### API接続エラー

1. GitHub Copilotのサブスクリプションが有効か確認してください
2. VSCodeが最新バージョンに更新されているか確認してください
3. 拡張機能を再インストールしてみてください

## サポート

問題が解決しない場合は、GitHubリポジトリにイシューを作成してください：
[https://github.com/yourusername/vscode-lm-api-host/issues](https://github.com/yourusername/vscode-lm-api-host/issues)
