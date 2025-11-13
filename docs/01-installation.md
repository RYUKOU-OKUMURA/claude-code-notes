# Claude Code 導入ガイド

## 目次
- [Claude Codeとは](#claude-codeとは)
- [システム要件](#システム要件)
- [インストール方法](#インストール方法)
  - [Mac（macOS）](#mac-macos)
  - [Windows](#windows)
- [初期設定](#初期設定)
- [動作確認](#動作確認)

---

## Claude Codeとは

Claude Codeは、Anthropic社が開発したAI駆動のターミナルベースの開発支援ツールです。

### 主な特徴

- **ターミナルで動作**: コマンドラインから直接利用でき、既存の開発環境にスムーズに統合できます
- **コード生成とデバッグ**: 自然言語での説明からコードを生成したり、バグの原因を特定して修正案を提案したりできます
- **コードベース理解**: プロジェクト全体の構造を理解し、質問に対して詳細な回答を提供します
- **自動化タスク**: lint問題の修正、マージコンフリクト解決、リリースノート作成など、定型業務を自動化できます

---

## システム要件

Claude Codeを使用するには、以下の要件を満たす必要があります。

### オペレーティングシステム

- **macOS**: 10.15以降
- **Windows**: Windows 10以降
  - ネイティブインストール（PowerShell、CMD）
  - WSL 1またはWSL 2
  - Git for Windows
- **Linux**: Ubuntu 20.04以降 / Debian 10以降

### ハードウェア

- **メモリ**: 最低4GB RAM（推奨: 8GB以上）
- **ストレージ**: 十分な空き容量
- **インターネット接続**: 必須（Claude APIと通信するため）

### その他の要件

- **Claudeアカウント**: [Claude.ai](https://claude.ai)または[Claude Console](https://console.anthropic.com)のアカウント
- **課金設定**: Claude Consoleで課金設定が必要（使用量に応じて課金されます）

---

## インストール方法

### Mac (macOS)

macOSには2つのインストール方法があります。

#### 方法1: 公式インストールスクリプト（推奨）

ターミナルを開いて、以下のコマンドを実行します:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**手順の詳細:**
1. **ターミナルを開く**: Finderから「アプリケーション」→「ユーティリティ」→「ターミナル」を開きます
2. **コマンドを入力**: 上記のコマンドをコピー＆ペーストします
3. **インストール完了を待つ**: 数秒から数分でインストールが完了します

#### 方法2: Homebrew経由（Homebrewユーザー向け）

Homebrewがインストール済みの場合、以下のコマンドでインストールできます:

```bash
brew install anthropic/claude/claude
```

**Homebrewとは?**
macOS用のパッケージマネージャーです。まだインストールしていない場合は、[Homebrewの公式サイト](https://brew.sh/ja/)を参照してください。

---

### Windows

Windowsには複数のインストール方法があります。

#### 方法1: PowerShell経由（推奨）

PowerShellを**管理者として実行**し、以下のコマンドを実行します:

```powershell
irm https://claude.ai/install.ps1 | iex
```

**手順の詳細:**
1. **PowerShellを管理者として開く**:
   - スタートメニューで「PowerShell」を検索
   - 「Windows PowerShell」を右クリック
   - 「管理者として実行」を選択
2. **コマンドを入力**: 上記のコマンドをコピー＆ペーストします
3. **インストール完了を待つ**: 数秒から数分でインストールが完了します

#### 方法2: コマンドプロンプト（CMD）経由

コマンドプロンプトを**管理者として実行**し、以下のコマンドを実行します:

```cmd
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

**手順の詳細:**
1. **コマンドプロンプトを管理者として開く**:
   - スタートメニューで「cmd」を検索
   - 「コマンドプロンプト」を右クリック
   - 「管理者として実行」を選択
2. **コマンドを入力**: 上記のコマンドをコピー＆ペーストします
3. **インストール完了を待つ**: インストールが完了します

#### 方法3: WSL経由

Windows Subsystem for Linux (WSL)を使用している場合、WSL内でLinux向けのインストール方法を使用できます:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**WSLとは?**
Windows上でLinux環境を実行できる機能です。開発者向けの機能で、Windowsの設定から有効化できます。

---

## 初期設定

インストールが完了したら、以下の手順で初期設定を行います。

### 1. インストールの確認

ターミナル（またはPowerShell）で以下のコマンドを実行し、Claude Codeが正しくインストールされているか確認します:

```bash
claude --version
```

正しくインストールされていれば、バージョン番号が表示されます（例: `claude version 1.2.3`）。

### 2. Claudeにログイン

初めて`claude`コマンドを実行すると、ログイン画面が表示されます:

```bash
claude
```

以下の流れでログインします:

1. **ブラウザが自動的に開く**: OAuth認証画面が表示されます
2. **Claudeアカウントでログイン**: Claude.aiまたはClaude Consoleのアカウントでログインします
3. **認証を許可**: 「Authorize」（許可）ボタンをクリックします
4. **ターミナルに戻る**: 認証が完了すると、ターミナルで「Logged in successfully!」と表示されます

**注意点:**
- Claude Consoleでの課金設定が必要です
- 初回ログイン時にAPIキーが自動的に設定されます

### 3. 課金設定の確認

Claude Codeを使用するには、Claude Consoleで課金設定が必要です:

1. [Claude Console](https://console.anthropic.com)にアクセス
2. 「Billing」（課金）セクションで支払い方法を登録
3. 使用量に応じて課金されます（従量課金制）

---

## 動作確認

インストールと初期設定が完了したら、動作を確認してみましょう。

### 基本的なコマンド

1. **対話モードを起動**:
   ```bash
   claude
   ```
   プロンプトが表示され、Claudeとの対話が始まります。

2. **簡単な質問をする**:
   ```
   こんにちは。自己紹介してください。
   ```

3. **終了する**:
   ```
   exit
   ```
   または `Ctrl+D`（Mac/Linux）、`Ctrl+Z`（Windows）で終了できます。

### 単発のタスクを実行

対話モードに入らずに、1つのタスクを実行することもできます:

```bash
claude "現在のディレクトリにあるファイルを一覧表示して"
```

### ヘルプの確認

利用可能なコマンドやオプションを確認するには:

```bash
claude --help
```

または対話モード内で:

```
/help
```

---

## トラブルシューティング

### インストールがうまくいかない場合

**Macの場合:**
- Xcodeのコマンドラインツールがインストールされているか確認:
  ```bash
  xcode-select --install
  ```

**Windowsの場合:**
- PowerShellの実行ポリシーを確認:
  ```powershell
  Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
  ```

### ログインできない場合

- インターネット接続を確認してください
- ブラウザのポップアップブロックが有効になっていないか確認してください
- Claude Consoleで課金設定が完了しているか確認してください

### コマンドが見つからない場合

- ターミナルを再起動してみてください
- PATHの設定を確認してください:
  ```bash
  echo $PATH
  ```

詳しいトラブルシューティングは、[06-troubleshooting.md](./06-troubleshooting.md)を参照してください。

---

## 次のステップ

インストールが完了したら、次のガイドに進みましょう:

- [基本的な使い方ガイド](./02-getting-started.md): Claude Codeの基本操作を学ぶ
- [CLAUDE.mdガイド](./03-claude-md-guide.md): プロジェクト設定ファイルの作成方法
- [Skillsガイド](./04-skills-guide.md): カスタムスキルの作成と活用
- [Tips集](./05-tips-and-tricks.md): 個人開発で役立つテクニック

---

**公式ドキュメント:**
- [Claude Code公式ドキュメント](https://code.claude.com/docs)
- [Claude API Documentation](https://docs.anthropic.com)
