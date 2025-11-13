# Claude Code フック（Hooks）ガイド

## 目次
- [フックとは](#フックとは)
- [フックの種類](#フックの種類)
- [フックの設定方法](#フックの設定方法)
- [実践的な活用例](#実践的な活用例)
- [ベストプラクティス](#ベストプラクティス)
- [よくある質問](#よくある質問)

---

## フックとは

**フック（Hooks）**は、Claude Codeの特定のイベントが発生したときに、自動的にスクリプトを実行する機能です。

### 基本概念

フックを使うと、以下のような自動化が可能になります：

- **セッション開始時**: プロジェクトの環境チェック、依存関係のインストール
- **コマンド実行前**: Lintチェック、フォーマット確認
- **ツール実行前**: セキュリティチェック、バックアップ作成

**イメージ:**
Gitのフック（pre-commit、pre-pushなど）と似た仕組みです。

```
イベント発生（例: セッション開始）
  ↓
フックが自動実行
  ↓
結果をClaude Codeに通知
  ↓
Claudeが作業を続行
```

---

## フックの種類

Claude Codeには、主に以下のフックがあります。

### 1. SessionStart（セッション開始時）

**タイミング:** `claude` コマンドを実行して、セッションが開始した直後

**用途:**
- 依存関係のチェック
- 環境変数の確認
- データベース接続のテスト
- 開発サーバーの起動確認
- Gitの状態確認

**例:**
```bash
# .claude/hooks/session-start.sh
#!/bin/bash

echo "🔍 環境チェックを実行中..."

# Node.jsのバージョンチェック
node --version

# 依存関係のチェック
if [ ! -d "node_modules" ]; then
  echo "⚠️  node_modulesが見つかりません。npm installを実行してください。"
fi

# 環境変数のチェック
if [ -z "$DATABASE_URL" ]; then
  echo "⚠️  DATABASE_URL環境変数が設定されていません"
fi

echo "✅ 環境チェック完了"
```

---

### 2. PromptSubmit（プロンプト送信前）

**タイミング:** ユーザーがプロンプト（指示）を送信する直前

**用途:**
- プロンプトの内容をログに記録
- 機密情報の検出と警告
- プロンプトのフォーマット確認

**例:**
```bash
# .claude/hooks/prompt-submit.sh
#!/bin/bash

# プロンプトの内容は標準入力から受け取る
PROMPT=$(cat)

# 機密情報が含まれていないかチェック
if echo "$PROMPT" | grep -qiE "(password|api[_-]?key|secret|token)"; then
  echo "⚠️  警告: プロンプトに機密情報が含まれている可能性があります"
  echo "送信する前に確認してください"
fi
```

---

### 3. BeforeToolCall（ツール実行前）

**タイミング:** Claudeがツール（ファイル編集、コマンド実行など）を使う直前

**用途:**
- 重要なファイルの保護
- デストラクティブな操作の確認
- バックアップの作成
- 操作のログ記録

**例:**
```bash
# .claude/hooks/before-tool-call.sh
#!/bin/bash

TOOL_NAME=$1
FILE_PATH=$2

# 本番環境のファイルを保護
if echo "$FILE_PATH" | grep -q "production"; then
  echo "🛑 エラー: 本番環境のファイルは直接編集できません"
  exit 1
fi

# 重要なファイルのバックアップ
if echo "$FILE_PATH" | grep -qE "(config|\.env)"; then
  echo "💾 バックアップを作成中..."
  cp "$FILE_PATH" "$FILE_PATH.backup"
fi
```

---

### 4. AfterToolCall（ツール実行後）

**タイミング:** Claudeがツールを実行した直後

**用途:**
- 変更内容の自動フォーマット
- Lintの自動実行
- テストの自動実行
- 変更のログ記録

**例:**
```bash
# .claude/hooks/after-tool-call.sh
#!/bin/bash

TOOL_NAME=$1
FILE_PATH=$2

# ファイル編集後に自動フォーマット
if [ "$TOOL_NAME" = "Edit" ] || [ "$TOOL_NAME" = "Write" ]; then
  if echo "$FILE_PATH" | grep -qE "\.(js|ts|jsx|tsx)$"; then
    echo "🎨 コードをフォーマット中..."
    npx prettier --write "$FILE_PATH"
  fi
fi
```

---

## フックの設定方法

### ステップ1: フックディレクトリの作成

プロジェクトのルートディレクトリで、`.claude/hooks/` ディレクトリを作成します:

```bash
mkdir -p .claude/hooks
```

---

### ステップ2: フックスクリプトの作成

フックの種類に応じて、スクリプトファイルを作成します:

```bash
# SessionStartフックの作成
touch .claude/hooks/session-start.sh

# 実行権限を付与
chmod +x .claude/hooks/session-start.sh
```

**ファイル名の規則:**
- `session-start.sh` → SessionStartフック
- `prompt-submit.sh` → PromptSubmitフック
- `before-tool-call.sh` → BeforeToolCallフック
- `after-tool-call.sh` → AfterToolCallフック

---

### ステップ3: フックスクリプトの内容を記述

エディタでフックスクリプトを開き、内容を記述します:

```bash
#!/bin/bash

# ここにフックの処理を記述
echo "フックが実行されました"
```

**重要なポイント:**
1. **Shebang行を忘れずに**: `#!/bin/bash`
2. **実行権限を付与**: `chmod +x`
3. **終了コード**:
   - `exit 0`: 成功（処理を続行）
   - `exit 1`: 失敗（処理を中断）

---

### ステップ4: 動作確認

Claude Codeを起動して、フックが動作するか確認します:

```bash
claude
```

SessionStartフックが設定されている場合、起動時にスクリプトが実行されます。

---

## 実践的な活用例

### 例1: 開発環境の自動チェック

**ファイル:** `.claude/hooks/session-start.sh`

```bash
#!/bin/bash

echo "🔍 開発環境をチェック中..."

# Node.jsのバージョンチェック
REQUIRED_NODE_VERSION="18"
CURRENT_NODE_VERSION=$(node -v | cut -d'v' -f2 | cut -d'.' -f1)

if [ "$CURRENT_NODE_VERSION" -lt "$REQUIRED_NODE_VERSION" ]; then
  echo "⚠️  Node.js v${REQUIRED_NODE_VERSION}以上が必要です"
  echo "   現在のバージョン: v${CURRENT_NODE_VERSION}"
fi

# 依存関係のチェック
if [ ! -d "node_modules" ]; then
  echo "📦 依存関係をインストール中..."
  npm install
fi

# 環境変数のチェック
if [ ! -f ".env" ]; then
  echo "⚠️  .envファイルが見つかりません"
  echo "   .env.exampleをコピーして.envを作成してください"
fi

# データベース接続のチェック
if ! npx prisma db push --help > /dev/null 2>&1; then
  echo "⚠️  Prismaが正しくセットアップされていません"
fi

echo "✅ 環境チェック完了"
```

**効果:**
- セッション開始時に環境の問題を早期発見
- 自動的に依存関係をインストール
- 開発者が忘れがちな設定を通知

---

### 例2: コード変更後の自動Lint

**ファイル:** `.claude/hooks/after-tool-call.sh`

```bash
#!/bin/bash

TOOL_NAME=$1
FILE_PATH=$2

# ファイル編集後にLintを実行
if [ "$TOOL_NAME" = "Edit" ] || [ "$TOOL_NAME" = "Write" ]; then
  if echo "$FILE_PATH" | grep -qE "\.(js|ts|jsx|tsx)$"; then
    echo "🔍 Lintを実行中..."

    # ESLintを実行（エラーがあっても処理は続行）
    npx eslint "$FILE_PATH" --fix || true

    # Prettierでフォーマット
    npx prettier --write "$FILE_PATH"

    echo "✅ Lint完了"
  fi
fi
```

**効果:**
- コード変更後に自動的にフォーマット
- Lintエラーを自動修正
- コード品質を自動的に保持

---

### 例3: 本番環境ファイルの保護

**ファイル:** `.claude/hooks/before-tool-call.sh`

```bash
#!/bin/bash

TOOL_NAME=$1
FILE_PATH=$2

# 保護するファイルのリスト
PROTECTED_FILES=(
  "config/production.js"
  ".env.production"
  "src/config/prod.ts"
)

# 保護対象のファイルかチェック
for protected in "${PROTECTED_FILES[@]}"; do
  if [[ "$FILE_PATH" == *"$protected"* ]]; then
    echo "🛑 エラー: $FILE_PATH は保護されています"
    echo "   本番環境のファイルは直接編集できません"
    exit 1
  fi
done

# デストラクティブな操作の警告
if [ "$TOOL_NAME" = "Bash" ]; then
  echo "⚠️  注意: シェルコマンドを実行します"
fi
```

**効果:**
- 本番環境ファイルの誤編集を防止
- 危険な操作の前に警告
- セキュリティの向上

---

### 例4: Git自動コミット前のチェック

**ファイル:** `.claude/hooks/before-tool-call.sh`

```bash
#!/bin/bash

TOOL_NAME=$1
COMMAND=$2

# gitコミットの前にチェック
if echo "$COMMAND" | grep -q "git commit"; then
  echo "🔍 コミット前チェックを実行中..."

  # テストを実行
  echo "🧪 テストを実行中..."
  npm test
  if [ $? -ne 0 ]; then
    echo "❌ テストが失敗しました。コミットを中断します。"
    exit 1
  fi

  # Lintチェック
  echo "🔍 Lintチェック中..."
  npm run lint
  if [ $? -ne 0 ]; then
    echo "❌ Lintエラーがあります。修正してからコミットしてください。"
    exit 1
  fi

  echo "✅ すべてのチェックが通りました"
fi
```

**効果:**
- コミット前にテストとLintを自動実行
- 問題があればコミットを中断
- コードの品質を保証

---

### 例5: プロジェクト状態の定期レポート

**ファイル:** `.claude/hooks/session-start.sh`

```bash
#!/bin/bash

echo "📊 プロジェクト状態レポート"
echo "================================"

# Git情報
echo "📌 Gitブランチ: $(git branch --show-current)"
echo "📝 未コミットの変更: $(git status --short | wc -l)ファイル"

# 最近のコミット
echo ""
echo "📜 最近のコミット:"
git log -3 --oneline

# テストカバレッジ（もしあれば）
if [ -f "coverage/coverage-summary.json" ]; then
  echo ""
  echo "📈 テストカバレッジ:"
  cat coverage/coverage-summary.json | grep -oP '"total".*?"pct":\K[0-9.]+' | head -1
fi

# TODOの数
echo ""
echo "📝 TODO項目: $(grep -r "TODO" src/ | wc -l)件"

echo "================================"
```

**効果:**
- セッション開始時にプロジェクトの状態を把握
- 未コミットの変更やTODOを可視化
- チーム開発での情報共有

---

## ベストプラクティス

### 1. エラーハンドリングを適切に行う

フックスクリプトでは、エラーハンドリングが重要です。

```bash
#!/bin/bash

# エラーが発生したら即座に終了
set -e

# 処理...

# 成功
exit 0
```

**終了コードの使い分け:**
- `exit 0`: 成功（処理を続行）
- `exit 1`: エラー（処理を中断）
- 終了コードを指定しない場合、最後のコマンドの終了コードが使われる

---

### 2. ユーザーに分かりやすいメッセージを出力

フックが何をしているか、結果がどうだったかを明確に伝えます。

```bash
#!/bin/bash

echo "🔍 テストを実行中..."

npm test

if [ $? -eq 0 ]; then
  echo "✅ テスト完了: すべて成功"
else
  echo "❌ テスト失敗: 修正してください"
  exit 1
fi
```

---

### 3. フックの実行時間を短く保つ

フックは頻繁に実行されるため、実行時間を短く保つことが重要です。

**悪い例:**
```bash
# 時間がかかりすぎる
npm run build
npm run test:e2e
```

**良い例:**
```bash
# 必要最小限のチェック
npm run lint
npm run test:unit
```

---

### 4. フックをバージョン管理する

フックスクリプトはGitにコミットし、チームで共有します。

```bash
git add .claude/hooks/
git commit -m "feat: Add session-start hook"
```

**`.gitignore`に追加しない:**
フックはプロジェクトの一部として管理すべきです。

---

### 5. 環境に応じてフックを切り替える

開発環境と本番環境で異なるフックを使う場合があります。

```bash
#!/bin/bash

# 環境変数で挙動を変更
if [ "$NODE_ENV" = "production" ]; then
  echo "本番環境では軽量なチェックのみ実行"
  npm run lint:quick
else
  echo "開発環境では詳細なチェックを実行"
  npm run lint
  npm test
fi
```

---

### 6. フックを無効化できるようにする

デバッグ時や特殊な状況では、フックを一時的に無効化できると便利です。

```bash
#!/bin/bash

# SKIP_HOOKSが設定されていたらスキップ
if [ "$SKIP_HOOKS" = "true" ]; then
  echo "⏭️  フックをスキップ"
  exit 0
fi

# 通常の処理...
```

**使い方:**
```bash
SKIP_HOOKS=true claude
```

---

## よくある質問

### Q: フックが実行されない

**A:** 以下を確認してください:

1. **ファイル名が正しいか**
   - `session-start.sh` （`session_start.sh` ではない）
   - ハイフンでつなぐ

2. **実行権限があるか**
   ```bash
   chmod +x .claude/hooks/session-start.sh
   ```

3. **Shebang行があるか**
   ```bash
   #!/bin/bash
   ```

4. **ファイルの場所が正しいか**
   ```
   .claude/hooks/session-start.sh
   ```

---

### Q: フックでエラーが発生したらどうなる？

**A:** フックが `exit 1` を返すと、Claude Codeの処理が中断されます。

**例:**
```bash
#!/bin/bash

npm test
if [ $? -ne 0 ]; then
  echo "テストが失敗しました"
  exit 1  # ここでClaude Codeの処理が中断される
fi
```

---

### Q: フックはどの言語で書ける？

**A:** 実行可能なスクリプトなら何でもOKです。

**Bash:**
```bash
#!/bin/bash
echo "Bash script"
```

**Python:**
```python
#!/usr/bin/env python3
print("Python script")
```

**Node.js:**
```javascript
#!/usr/bin/env node
console.log("Node.js script");
```

**実行権限を忘れずに:**
```bash
chmod +x .claude/hooks/session-start.py
```

---

### Q: フックから引数を受け取るには？

**A:** フックの種類によって、異なる引数が渡されます。

**BeforeToolCall / AfterToolCall:**
```bash
#!/bin/bash

TOOL_NAME=$1    # ツール名（Edit, Write, Bashなど）
FILE_PATH=$2    # 対象ファイルのパス

echo "Tool: $TOOL_NAME"
echo "File: $FILE_PATH"
```

**PromptSubmit:**
```bash
#!/bin/bash

# プロンプトの内容は標準入力から受け取る
PROMPT=$(cat)

echo "Prompt: $PROMPT"
```

---

### Q: フックのログはどこに保存される？

**A:** フックの出力はClaude Codeの出力に表示されます。ログファイルに保存したい場合は、スクリプト内で記録します。

```bash
#!/bin/bash

LOG_FILE=".claude/hooks.log"

echo "$(date): SessionStart hook executed" >> "$LOG_FILE"
```

---

### Q: チーム全員にフックを適用するには?

**A:** フックスクリプトをGitにコミットします。

```bash
git add .claude/hooks/
git commit -m "Add hooks for team"
git push
```

チームメンバーがpullすれば、自動的にフックが適用されます。

---

### Q: フックをプロジェクト外に配置できる？

**A:** グローバルフックは `~/.config/claude/hooks/` に配置できます。

```bash
mkdir -p ~/.config/claude/hooks/
echo '#!/bin/bash\necho "Global hook"' > ~/.config/claude/hooks/session-start.sh
chmod +x ~/.config/claude/hooks/session-start.sh
```

**優先順位:**
1. プロジェクトのフック (`.claude/hooks/`)
2. グローバルフック (`~/.config/claude/hooks/`)

---

## フックを活用した開発フロー

### フロー例: 品質を保証する開発

**1. セッション開始時: 環境チェック**
```bash
# .claude/hooks/session-start.sh
- 依存関係の確認
- 環境変数の確認
- データベース接続の確認
```

**2. コード編集後: 自動フォーマット**
```bash
# .claude/hooks/after-tool-call.sh
- PrettierでフォーマットESLintで自動修正
```

**3. コミット前: 品質チェック**
```bash
# .claude/hooks/before-tool-call.sh
- テストの実行
- Lintチェック
- 型チェック
```

**4. プッシュ前: 最終確認**
```bash
# .claude/hooks/before-tool-call.sh
- ビルドの確認
- E2Eテストの実行
```

---

## まとめ

フックは、以下のような自動化に有用です:

✅ **環境チェック**: セッション開始時の自動確認
✅ **コード品質**: 自動フォーマット、Lint
✅ **セキュリティ**: 機密情報の検出、ファイル保護
✅ **テスト**: コミット前の自動テスト
✅ **ログ記録**: 操作の記録とレポート

**効果的な使い方:**
- エラーハンドリングを適切に行う
- 実行時間を短く保つ
- 分かりやすいメッセージを出力
- チームで共有する

---

## 次のステップ

フックを理解したら、他の高度な機能も学びましょう:

- [サブエージェントガイド](./07-subagents-guide.md): 専門エージェントの活用
- [Slash Commandsガイド](./09-slash-commands-guide.md): カスタムコマンドの作成
- [MCPガイド](./10-mcp-guide.md): 外部ツールとの統合

---

**参考:**
- [Claude Code 公式ドキュメント](https://code.claude.com/docs)
