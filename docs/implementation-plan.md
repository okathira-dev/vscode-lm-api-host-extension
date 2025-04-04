# VSCode LM API Host 拡張機能実装計画・ガイド

## 前提条件と開発環境

この実装計画・ガイドは、以下の開発環境を前提としています：

- Node.js v22.12.0
- npm v10.9.0
- VSCode 最新の安定版
- Git 2.x以上

### バージョン要件の統一に関する決定事項

- **決定**: バージョン要件の統一
  - Node.js v22.12.0
  - npm v10.9.0
  - VSCode 最新の安定版
  - **理由**: 最新の安定版を使用することで、最新の機能やセキュリティ修正を活用し、一貫した開発環境を確保

### 開発環境のセットアップ手順

#### 基本環境のインストール

1. Node.jsとnpmのインストール
   - Windows: Node.js公式サイトからインストーラーを使用
   - macOS: Homebrewを使用
   - Linux: パッケージマネージャーを使用

2. VSCodeのインストール
   - 公式サイトからインストーラーを入手して実行

## 計画概要

実装作業を以下の主要フェーズに分けて進めます。テスト駆動開発(TDD)アプローチを採用し、各機能の実装時にテストを作成・実行します：

1. VSCode Extension Generatorを使った初期環境構築
2. esbuildの導入と基本設定
3. コアコンポーネント実装
4. Fastifyを使用したAPIサーバー実装
5. 高度な機能実装
6. ドキュメント作成とリリース準備

### 開発手法に関する決定事項

- **決定**: テスト駆動開発（TDD）アプローチを採用
  - **理由**: 早期からの品質確保、リファクタリングの容易さ、テストがコードの使用例としても機能
  - **実装**: 各機能の実装前にテストを作成し、実装後にテストが通ることを確認

### 参考リソース

- Cline (<https://github.com/cline/cline>)
- VSCode Language Model API (<https://code.visualstudio.com/api/extension-guides/language-model>)
- VSCode 拡張機能開発ガイド (<https://code.visualstudio.com/api/get-started/your-first-extension>)

## 1. VSCode Extension Generatorを使った初期環境構築

この初期フェーズでは、VSCode公式ドキュメントに従ってYeomanとVSCode Extension Generatorを使用して基本的な拡張機能の骨組みを作成します。

### 1.1 Yeomanと拡張機能テンプレートのセットアップ

#### タスク詳細

1. **Yeomanと拡張機能ジェネレータのインストール**
   - Yeomanを一時的または恒久的にインストール
   - VSCode Extension Generatorをインストール

2. **拡張機能プロジェクトの生成**
   - テンプレートとしてTypeScript版を選択
   - プロジェクト名などの基本情報を設定
   - バンドラーは「unbundled」を選択（後でesbuildを導入するため）

3. **生成されたプロジェクトの確認とテスト環境の導入**
   - プロジェクト構造の確認
   - 基本的な拡張機能機能のテスト実行
   - 基本テストの実行確認

**成果物**:
- 基本的なVSCode拡張機能プロジェクト
- 最小限の動作する拡張機能コード
- テスト環境のセットアップ

### 1.2 テスト環境のセットアップ

テスト駆動開発(TDD)アプローチを採用するため、以下の手順でテスト環境を構築します：

1. **Jestテストフレームワークの設定**
   - Jestとその依存関係のインストール
   - Jest設定ファイルの作成

2. **VSCode用のテストモックの準備**
   - VSCode API用のモックライブラリのインストール
   - テストユーティリティの準備

3. **基本的なテストの作成**
   - 拡張機能のアクティベーションテスト
   - コマンド登録のテスト

**成果物**:
- Jestテスト環境
- VSCode APIのモック設定
- 基本的なテストケース

## 2. esbuildの導入と基本設定

このフェーズでは、生成されたプロジェクトにesbuildを導入し、ビルド設定を最適化します。

### ビルドシステムの技術選定

- **決定**: esbuildをビルドシステムとして採用
  - **理由**: 高速なビルド時間、シンプルな設定、TypeScriptのネイティブサポート
  - **代替案**: Vite（Node.js CJSサポートの問題により却下）

### 2.1 esbuildのセットアップ

#### タスク詳細

1. **esbuildのインストールと設定ファイルの作成**
   - esbuildパッケージのインストール
   - ビルド設定ファイルの作成

2. **esbuild構成ファイルの作成**
   - `esbuild.js`設定ファイルの作成
   - 外部依存関係の設定（vscode）
   - ソースマップの設定

3. **package.jsonスクリプトの更新とビルドテスト**
   - ビルドスクリプトの追加
   - watch modeの設定
   - VSIXパッケージング設定
   - ビルド処理のテスト

**成果物**:
- esbuildによるビルド設定
- 更新されたnpmスクリプト
- VSIXパッケージング設定
- ビルドテスト

### 2.2 プロジェクト構造の拡張

#### タスク詳細

1. **ディレクトリ構造の拡張**
   - 以下の構造に基づいたディレクトリ設計の実装:

   ```
   vscode-lm-api-host/
   ├── src/
   │   ├── extension.ts               # エントリーポイント
   │   ├── lm-service/                # LLMサービス実装
   │   │   ├── index.ts               # サービスエクスポート
   │   │   ├── lm-api-client.ts       # LM API クライアント
   │   │   ├── models.ts              # モデル管理
   │   │   └── __tests__/             # LMサービステスト
   │   ├── server/                    # APIサーバー関連
   │   │   ├── index.ts               # サーバーエクスポート
   │   │   ├── server.ts              # Fastifyサーバー実装
   │   │   ├── routes/                # APIルート定義
   │   │   └── __tests__/             # サーバーテスト
   │   ├── webview/                   # WebView UI関連
   │   │   ├── index.ts               # WebViewエクスポート
   │   │   ├── panels/                # パネル定義
   │   │   └── __tests__/             # WebViewテスト
   │   └── utils/                     # 共通ユーティリティ
   │       ├── index.ts               # ユーティリティエクスポート
   │       ├── logger.ts              # ロギング機能
   │       ├── config.ts              # 設定管理
   │       └── __tests__/             # ユーティリティテスト
   ├── dist/                          # ビルド出力ディレクトリ
   ├── test/                          # 統合テスト
   │   ├── suite/                     # テストスイート
   │   └── runTest.ts                 # テスト実行エントリーポイント
   ├── webview-ui/                    # WebView UI
   │   ├── src/                       # UI ソースコード
   │   └── dist/                      # UI ビルド出力
   ├── .vscode/                       # VSCode設定
   ├── .eslintrc.json                 # ESLint設定
   ├── .prettierrc                    # Prettier設定
   ├── tsconfig.json                  # TypeScript設定
   ├── jest.config.js                 # Jest設定
   ├── esbuild.js                     # esbuild設定
   ├── package.json                   # プロジェクト設定
   └── README.md                      # プロジェクト説明
   ```

2. **基本的な拡張機能コードの拡張**
   - コマンド登録の実装
   - ステータスバー項目の実装
   - 設定の読み込み機能
   - 対応するユニットテストの作成

3. **配布パッケージ設定の最適化**
   - `.vscodeignore`ファイルの更新
   - パッケージに含めるファイルの最適化

**成果物**:
- 拡張された完全なプロジェクト構造
- 基本的な拡張機能機能
- 最適化された配布設定
- 基本機能のテストコード

### 2.3 開発環境の強化

#### タスク詳細

1. **リンターとフォーマッターの設定**
   - ESLintの設定
   - Prettierの設定
   - Git Hooksの設定（必要に応じて）

2. **デバッグ設定の最適化**
   - `launch.json`の更新
   - ブレークポイントの設定
   - デバッグセッションの効率化

**成果物**:
- 完全な開発環境
- コード品質管理ツール
- 最適化されたデバッグ環境

## 3. コアコンポーネント実装

このフェーズでは、VSCode LM API Host拡張機能の中核となる機能を実装します。各ステップでテストを作成し、テスト駆動開発を実施します。

### 3.1 VSCode LM APIラッパーの実装

#### タスク詳細

1. **VSCode LM API接続の実装とテスト**
   - GitHub Copilot経由でのAPI接続
   - 利用可能なモデルの取得
   - APIレスポンスの処理
   - エラーハンドリング

2. **モデル管理機能の実装**
   - モデル情報の取得と管理
   - モデル選択機能
   - モデル情報のキャッシュ
   - モデル情報のテスト

3. **チャット補完機能の実装**
   - チャットリクエストの処理
   - レスポンスのフォーマット変換
   - ストリーミングサポート
   - テストの実装

**成果物**:
- VSCode LM APIへのアクセスラッパー
- モデル管理機能
- チャット補完機能
- 単体テスト

### 3.2 モジュール設計の原則

- 機能ごとに独立したモジュールを作成
- 各モジュールは単一責任の原則に従う
- 循環依存を避けるために、依存関係を明確に定義
- インターフェースを適切に使用して実装の詳細を隠蔽

### 3.3 テスト構造の方針

- 各モジュールには対応する `__tests__` ディレクトリを作成
- ユニットテストは実装と並行して作成（TDDアプローチ）
- テストファイル名は `[テスト対象ファイル名].test.ts` の形式
- モック、スタブ、スパイを適切に使用してテスト分離

## 4. Fastifyを使用したAPIサーバー実装

このフェーズでは、LM API機能を外部に公開するためのHTTP APIサーバーを実装します。

### 4.1 Fastifyサーバーの基本設定

#### タスク詳細

1. **Fastifyのインストールと基本設定**
   - Fastifyとプラグインのインストール
   - サーバー初期化コードの実装
   - ポート/ホスト設定の管理
   - サーバーライフサイクル管理

2. **APIルーターの実装**
   - エンドポイント定義
   - リクエストバリデーション
   - レスポンスシリアライゼーション
   - エラーハンドリング

3. **CORSとセキュリティ設定**
   - CORSミドルウェアの実装
   - リクエスト制限の設定
   - セキュリティヘッダーの設定

**成果物**:
- Fastifyベースの基本APIサーバー
- APIルーター
- セキュリティ設定
- ユニットテスト

### 4.2 APIエンドポイント実装

#### タスク詳細

1. **モデル一覧エンドポイントの実装**
   - `/v1/models` エンドポイント
   - OpenAI互換のレスポンス形式
   - データ変換ロジック

2. **チャット補完エンドポイントの実装**
   - `/v1/chat/completions` エンドポイント
   - リクエストスキーマバリデーション
   - LM APIへのプロキシロジック
   - レスポンス変換

3. **ストリーミングレスポンスのサポート**
   - Server-Sent Events (SSE) 実装
   - ストリーミングレスポンスのフォーマット
   - 接続処理とエラーハンドリング

**成果物**:
- 完全なAPIエンドポイント実装
- OpenAI互換のレスポンス形式
- ストリーミングサポート
- ユニットテスト

## 5. 高度な機能実装

このフェーズでは、基本機能に加えて高度な機能を実装します。こちらはプロジェクトの進捗や優先度に応じてオプショナルとして扱うことができます。

### 5.1 WebView UIの実装

#### タスク詳細

1. **WebView基本構造の実装**
   - WebViewパネルの作成
   - HTML/CSS/JSの基本構造
   - メッセージ通信の実装

2. **サーバー管理UI**
   - サーバーステータス表示
   - サーバー設定UI
   - 起動/停止コントロール

3. **APIテストインターフェース**
   - モデル選択UI
   - テストリクエスト送信
   - レスポンス表示

**成果物**:
- WebView UIパネル
- サーバー管理インターフェース
- APIテスト機能
- テスト

### 5.2 高度なレスポンス処理

#### タスク詳細

1. **レスポンスフォーマット変換拡張**
   - 異なるAPIフォーマット間の変換
   - カスタムレスポンス形式の実装

2. **ストリーミングレスポンスの拡張**
   - 部分的なトークン処理
   - キャンセル機能
   - 進捗表示

**成果物**:
- 拡張されたレスポンス処理
- 高度なストリーミング機能
- テスト

### 5.3 モニタリングと診断

#### タスク詳細

1. **メトリクス収集の実装**
   - リクエスト数のカウント
   - 処理時間の測定
   - エラー率の追跡

2. **ステータスエンドポイントの実装**
   - サーバーステータスAPI
   - メトリクス取得API
   - ヘルスチェックエンドポイント

3. **ログ機能の強化**
   - 詳細ログレベル
   - ファイルへのログ出力
   - ローテーション機能

**成果物**:
- メトリクス収集システム
- ステータスAPIエンドポイント
- 拡張ログ機能
- テスト

## 6. ドキュメントとリリース準備

このフェーズでは、プロジェクトのドキュメント作成とリリース準備を行います。

### 6.1 ユーザードキュメント

#### タスク詳細

1. **インストールガイドの作成**
   - 前提条件
   - インストール手順
   - 初期設定方法

2. **使用方法ドキュメント**
   - 機能の説明
   - APIリファレンス
   - 設定オプション

3. **トラブルシューティングガイド**
   - 一般的な問題の解決方法
   - エラーメッセージの説明
   - デバッグ方法

**成果物**:
- 完全なユーザードキュメント
- APIリファレンス
- トラブルシューティングガイド

### 6.2 開発者ドキュメント

#### タスク詳細

1. **アーキテクチャドキュメント**
   - コンポーネント説明
   - クラス図
   - データフロー

2. **貢献ガイドライン**
   - 開発環境設定
   - コーディング規約
   - プルリクエストプロセス

3. **APIの内部実装ドキュメント**
   - 内部APIの説明
   - 拡張ポイント
   - カスタマイズ方法

**成果物**:
- アーキテクチャドキュメント
- 貢献ガイドライン
- 内部APIドキュメント

### 6.3 リリース準備

#### タスク詳細

1. **パッケージング設定**
   - VSIXパッケージの作成
   - マーケットプレイス掲載情報
   - アイコンとスクリーンショット

2. **最終テスト**
   - エンドツーエンドテスト
   - パフォーマンステスト
   - インストールテスト

3. **リリースノート作成**
   - 機能一覧
   - 既知の問題
   - 将来の計画

**成果物**:
- 公開用VSIXパッケージ
- リリースドキュメント
- 最終テスト結果

## 将来的な拡張計画

### バージョン2.0検討事項

1. **複数LLMプロバイダー対応**
   - OpenAI APIサポート
   - Anthropic Claude APIサポート
   - ローカルモデルサポート

2. **高度な認証と権限管理**
   - APIキー認証
   - ユーザーベースの認証
   - リクエスト単位の制限

3. **グラフィカル管理インターフェース**
   - ダッシュボード機能
   - 使用状況グラフ
   - 設定管理UI

4. **プラグインシステム**
   - カスタムエンドポイント
   - レスポンス変換プラグイン
   - 外部サービス連携

5. **WSL・クロスプラットフォーム対応強化**
   - WSL環境での特別なサポート
   - 異なるOS間での一貫した動作の保証
