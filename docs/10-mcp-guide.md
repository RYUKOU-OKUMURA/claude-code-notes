# Claude Code MCP（Model Context Protocol）ガイド

## 目次
- [MCPとは](#mcpとは)
- [MCPでできること](#mcpでできること)
- [MCPサーバーの設定方法](#mcpサーバーの設定方法)
- [よく使うMCPサーバー](#よく使うmcpサーバー)
- [実践的な活用例](#実践的な活用例)
- [ベストプラクティス](#ベストプラクティス)
- [よくある質問](#よくある質問)

---

## MCPとは

**MCP（Model Context Protocol）**は、Claude Codeが外部ツールやサービスと連携するための標準プロトコルです。

### 基本概念

MCPを使うと、Claude Codeに新しい能力を追加できます：

- **外部データベースへのアクセス**: PostgreSQL、MySQL、MongoDBなど
- **外部APIの呼び出し**: GitHub、Slack、Google Driveなど
- **専門ツールの統合**: Dockerコンテナ管理、Kubernetesなど
- **カスタムツールの追加**: 自作のツールやスクリプト

**イメージ:**
MCPは、Claude Codeと外部サービスをつなぐ「橋」のような役割を果たします。

```
Claude Code
  ↕ MCP (橋)
外部サービス（GitHub, Slack, データベースなど）
```

---

## MCPでできること

### 1. データベース操作

Claude Codeから直接データベースにクエリを実行できます。

**例:**
```
「usersテーブルから最新10件のユーザーを取得して」
```

→ Claude CodeがMCP経由でデータベースにアクセスし、結果を取得

---

### 2. GitHub連携

GitHubのIssueやPull Requestを直接操作できます。

**例:**
```
「このバグ修正のPRを作成して」
```

→ Claude CodeがMCP経由でGitHub APIを呼び出し、PRを作成

---

### 3. Slack連携

Slackにメッセージを送信したり、チャンネルの情報を取得できます。

**例:**
```
「デプロイ完了をSlackの#deploymentsチャンネルに通知して」
```

→ Claude CodeがMCP経由でSlack APIを呼び出し、メッセージを送信

---

### 4. ファイルシステム操作

通常のClaude Codeの範囲外のファイルシステム操作が可能になります。

**例:**
```
「外部ストレージからログファイルを取得して分析して」
```

---

### 5. カスタムツールの統合

自作のツールやスクリプトをClaude Codeから呼び出せます。

---

## MCPサーバーの設定方法

MCPサーバーを使うには、設定ファイルでサーバーを指定します。

### ステップ1: 設定ファイルの場所

MCPサーバーの設定は、以下のファイルで管理します：

```
~/.config/claude/mcp.json
```

---

### ステップ2: 設定ファイルの作成

設定ファイルを作成し、使用するMCPサーバーを指定します。

```bash
mkdir -p ~/.config/claude
touch ~/.config/claude/mcp.json
```

---

### ステップ3: MCPサーバーの設定

`mcp.json` に、使用するMCPサーバーを記述します。

**例: GitHubとSlackのMCPサーバーを設定**

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "your-github-token"
      }
    },
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "your-slack-bot-token",
        "SLACK_TEAM_ID": "your-team-id"
      }
    }
  }
}
```

---

### ステップ4: Claude Codeを再起動

設定を反映するために、Claude Codeを再起動します：

```bash
exit
claude
```

---

## よく使うMCPサーバー

### 1. GitHub MCP Server

GitHubのリポジトリ、Issue、Pull Requestを操作できます。

**インストール:**
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_your_token_here"
      }
    }
  }
}
```

**できること:**
- リポジトリの情報取得
- Issueの作成・更新・クローズ
- Pull Requestの作成・マージ
- ファイルの読み書き
- ブランチの管理

**使用例:**
```
「このバグ修正のPull Requestを作成して。タイトルは "Fix login bug" にして」
```

---

### 2. Slack MCP Server

Slackのチャンネルやメッセージを操作できます。

**インストール:**
```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-your-token",
        "SLACK_TEAM_ID": "T01234567"
      }
    }
  }
}
```

**できること:**
- メッセージの送信
- チャンネル情報の取得
- ユーザー情報の取得
- ファイルのアップロード

**使用例:**
```
「デプロイが完了したことを#deploymentsチャンネルに通知して」
```

---

### 3. PostgreSQL MCP Server

PostgreSQLデータベースにクエリを実行できます。

**インストール:**
```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "POSTGRES_CONNECTION_STRING": "postgresql://user:password@localhost:5432/mydb"
      }
    }
  }
}
```

**できること:**
- SELECTクエリの実行
- テーブル構造の確認
- データの挿入・更新・削除（設定による）

**使用例:**
```
「usersテーブルから、過去1週間に登録したユーザーを取得して」
```

---

### 4. Google Drive MCP Server

Google Driveのファイルにアクセスできます。

**インストール:**
```json
{
  "mcpServers": {
    "gdrive": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-gdrive"],
      "env": {
        "GDRIVE_CLIENT_ID": "your-client-id",
        "GDRIVE_CLIENT_SECRET": "your-client-secret",
        "GDRIVE_REFRESH_TOKEN": "your-refresh-token"
      }
    }
  }
}
```

**できること:**
- ファイルの検索
- ファイルの読み込み
- ファイルの作成・更新
- フォルダの管理

**使用例:**
```
「Google Driveの "Project Docs" フォルダから最新の仕様書を取得して」
```

---

### 5. Filesystem MCP Server

拡張されたファイルシステム操作が可能になります。

**インストール:**
```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/allowed/path"]
    }
  }
}
```

**できること:**
- 指定ディレクトリ内のファイル操作
- ファイル検索
- ファイルの監視

---

## 実践的な活用例

### 例1: GitHub IssueからPRを自動作成

**シーン:** GitHub Issueの内容を元に、修正のPRを自動作成

```bash
claude
```

対話モードで:

```
「Issue #42の内容を確認して、修正のPull Requestを作成して。
ブランチ名は "fix/issue-42" にして」
```

**Claude Codeの動作:**
1. GitHub MCP経由でIssue #42の内容を取得
2. Issueの説明から修正内容を理解
3. 新しいブランチを作成
4. コードを修正
5. コミットしてプッシュ
6. Pull Requestを作成
7. PR本文にIssueへの参照を含める

---

### 例2: データベースの分析とレポート

**シーン:** データベースのデータを分析して、Slackにレポートを送信

```
「過去1ヶ月のユーザー登録数をデータベースから取得して、
グラフ化して#analyticsチャンネルに投稿して」
```

**Claude Codeの動作:**
1. PostgreSQL MCP経由でデータを取得
2. データを集計・分析
3. グラフを生成
4. Slack MCP経由で#analyticsチャンネルに投稿

---

### 例3: 外部APIとの統合

**シーン:** 外部APIから情報を取得してコードに反映

```
「Stripeの最新のAPIドキュメントをGoogle Driveから取得して、
決済機能のコードを最新のAPIに合わせて更新して」
```

**Claude Codeの動作:**
1. Google Drive MCP経由でAPIドキュメントを取得
2. ドキュメントを解析
3. 既存のコードを確認
4. 最新のAPIに合わせてコードを更新
5. テストを実行

---

### 例4: マルチサービス連携

**シーン:** GitHub、データベース、Slackを組み合わせた作業

```
「本番環境のデータベースからエラーログを取得して、
頻度の高いエラーについてGitHub Issueを作成して、
#bugsチャンネルに通知して」
```

**Claude Codeの動作:**
1. PostgreSQL MCP経由でエラーログを取得
2. エラーを集計・分析
3. GitHub MCP経由でIssueを作成
4. Slack MCP経由で通知

---

### 例5: 定期レポートの自動化

**シーン:** 毎日の定期レポートを自動生成

```
「今日のコミット数、PR数、マージ数をGitHubから取得して、
Slackの#dailyチャンネルに日次レポートとして投稿して」
```

---

## ベストプラクティス

### 1. 環境変数で機密情報を管理

トークンやパスワードは、環境変数で管理します。

**悪い例（直接記述）:**
```json
{
  "mcpServers": {
    "github": {
      "env": {
        "GITHUB_TOKEN": "ghp_abc123def456..."
      }
    }
  }
}
```

**良い例（環境変数）:**
```json
{
  "mcpServers": {
    "github": {
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

環境変数を `.bashrc` や `.zshrc` で設定:
```bash
export GITHUB_TOKEN="ghp_your_token_here"
```

---

### 2. 最小権限の原則

MCPサーバーには、必要最小限の権限だけを付与します。

**例: GitHub Token**
- リポジトリの読み取りだけが必要な場合: `repo:read` のみ
- 書き込みも必要な場合: `repo` 全体

---

### 3. エラーハンドリング

MCP経由の操作はネットワークエラーなどが発生する可能性があるため、エラーハンドリングを意識します。

```
「GitHub IssueからPRを作成して。
もしAPIエラーが発生したら、エラー内容を教えて」
```

---

### 4. レート制限を考慮

外部APIにはレート制限があるため、大量のリクエストは避けます。

**悪い例:**
```
「全てのIssue（1000件以上）を一つずつ確認して更新して」
```

**良い例:**
```
「直近10件のオープンIssueを確認して、必要なものだけ更新して」
```

---

### 5. ログとモニタリング

MCP経由の操作は、ログに記録しておくと便利です。

```bash
# .claude/hooks/after-tool-call.sh
if [[ $TOOL_NAME == *"MCP"* ]]; then
  echo "$(date): MCP tool called - $TOOL_NAME" >> ~/.claude/mcp.log
fi
```

---

## よくある質問

### Q: MCPサーバーをインストールするには?

**A:** ほとんどのMCPサーバーは、npmパッケージとして提供されています。

**グローバルインストール（推奨）:**
```bash
npm install -g @modelcontextprotocol/server-github
```

**または、npxで実行時にインストール:**
```json
{
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-github"]
}
```

---

### Q: 自作のMCPサーバーを作成できる?

**A:** はい、Model Context Protocolの仕様に従って、自作のサーバーを作成できます。

**基本的な流れ:**
1. MCPプロトコルの仕様を確認
2. Node.js、Python、Go などでサーバーを実装
3. `mcp.json` で自作サーバーを指定

**詳細:**
- [MCP Specification](https://modelcontextprotocol.io/)

---

### Q: MCPサーバーが動かない

**A:** 以下を確認してください:

1. **設定ファイルの場所**
   ```
   ~/.config/claude/mcp.json
   ```

2. **JSONの文法エラー**
   - カンマの位置
   - 括弧の対応

3. **環境変数の設定**
   ```bash
   echo $GITHUB_TOKEN  # トークンが設定されているか確認
   ```

4. **MCPサーバーのインストール**
   ```bash
   npx @modelcontextprotocol/server-github --version
   ```

5. **Claude Codeの再起動**
   ```bash
   exit
   claude
   ```

---

### Q: MCPサーバーの一覧を確認するには?

**A:** 対話モード内で確認できます:

```
利用可能なMCPサーバーを教えて
```

または:

```
どんな外部サービスと連携できる?
```

---

### Q: MCPは無料で使える?

**A:** MCPの基本機能は無料ですが、外部サービスの利用には費用がかかる場合があります。

**費用の例:**
- **GitHub API**: 基本的に無料（レート制限あり）
- **Slack API**: 基本的に無料
- **Google Drive API**: 基本的に無料（容量制限あり）
- **Claude Code自体**: 使用量に応じた課金

---

### Q: セキュリティ面で注意すべきことは?

**A:** 以下の点に注意してください:

1. **トークンの管理**
   - `.gitignore`に設定ファイルを追加
   - 環境変数で管理
   - 定期的にトークンを更新

2. **最小権限の原則**
   - 必要最小限の権限だけを付与

3. **公開リポジトリでの注意**
   - トークンをコミットしない
   - `.env`ファイルを`.gitignore`に追加

4. **ログの管理**
   - 機密情報がログに残らないようにする

---

### Q: MCPサーバーのトラブルシューティング

**A:** 以下の手順でデバッグできます:

**1. ログを確認**
```bash
# Claude Codeのログを確認
cat ~/.claude/logs/claude-code.log | grep MCP
```

**2. MCPサーバーを直接実行**
```bash
npx @modelcontextprotocol/server-github
```

**3. 環境変数を確認**
```bash
env | grep -i token
```

**4. 設定ファイルをバリデート**
```bash
cat ~/.config/claude/mcp.json | jq .
```

---

## MCPを活用した開発フロー

### フロー例: GitHub連携開発

**1. Issueから開発開始**
```
「Issue #42の内容を確認して、作業ブランチを作成して」
```

**2. コードを実装**
```
「Issueの要件に従って機能を実装して」
```

**3. PRを作成**
```
「実装が完了したので、Pull Requestを作成して」
```

**4. レビュー依頼を通知**
```
「#developmentチャンネルにレビュー依頼を投稿して」
```

**5. マージ後の通知**
```
「PRがマージされたら、#deploymentsチャンネルに通知して」
```

---

## まとめ

MCPは、以下のような連携に有用です:

✅ **GitHub連携**: Issue、PR、リポジトリ管理
✅ **Slack連携**: 通知、レポート送信
✅ **データベース連携**: データ取得、分析
✅ **外部API統合**: 各種サービスとの連携
✅ **カスタムツール**: 自作ツールの統合

**効果的な使い方:**
- 機密情報は環境変数で管理
- 最小権限の原則を守る
- エラーハンドリングを意識
- レート制限を考慮

---

## 次のステップ

MCPを理解したら、他の機能も学びましょう:

- [サブエージェントガイド](./07-subagents-guide.md): 専門エージェントの活用
- [HOOKSガイド](./08-hooks-guide.md): イベント駆動の自動化
- [Slash Commandsガイド](./09-slash-commands-guide.md): カスタムコマンドの作成
- [.claudeignoreガイド](./11-claudeignore-guide.md): ファイル除外設定

---

**参考:**
- [Model Context Protocol公式サイト](https://modelcontextprotocol.io/)
- [Claude Code 公式ドキュメント](https://code.claude.com/docs)
- [MCP Servers一覧](https://github.com/modelcontextprotocol/servers)
