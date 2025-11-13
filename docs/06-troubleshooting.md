# Claude Code トラブルシューティングガイド

## 目次
- [インストール時の問題](#インストール時の問題)
- [ログイン・認証の問題](#ログイン認証の問題)
- [実行時の問題](#実行時の問題)
- [パフォーマンスの問題](#パフォーマンスの問題)
- [課金・コストの問題](#課金コストの問題)
- [その他の問題](#その他の問題)

---

## インストール時の問題

### 問題1: インストールスクリプトが失敗する

**症状:**
```
curl: (7) Failed to connect to claude.ai
```

**原因:**
- インターネット接続の問題
- ファイアウォールやプロキシの設定

**解決方法:**

1. **インターネット接続を確認:**
   ```bash
   ping claude.ai
   ```

2. **プロキシ設定を確認（企業ネットワークなど）:**
   ```bash
   echo $http_proxy
   echo $https_proxy
   ```

3. **手動でインストーラーをダウンロード:**
   ```bash
   curl -O https://claude.ai/install.sh
   chmod +x install.sh
   ./install.sh
   ```

---

### 問題2: 「Permission denied」エラー

**症状:**
```
Permission denied
```

**原因:**
- 管理者権限が必要な場所にインストールしようとしている

**解決方法:**

**Mac/Linux:**
```bash
sudo curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell:**
PowerShellを**管理者として実行**してから、インストールコマンドを実行してください。

---

### 問題3: コマンドが見つからない

**症状:**
```
claude: command not found
```

**原因:**
- PATHの設定が正しくない
- インストールが完了していない

**解決方法:**

1. **ターミナルを再起動:**
   新しいターミナルウィンドウを開いてください。

2. **PATHを確認:**
   ```bash
   echo $PATH
   ```

3. **手動でPATHを追加（Mac/Linux）:**
   ```bash
   export PATH="$HOME/.claude/bin:$PATH"
   ```

   恒久的に設定する場合は、`.bashrc`または`.zshrc`に追加:
   ```bash
   echo 'export PATH="$HOME/.claude/bin:$PATH"' >> ~/.bashrc
   source ~/.bashrc
   ```

4. **インストールを確認:**
   ```bash
   which claude
   ```

---

### 問題4: Mac で「開発元が未確認」エラー

**症状:**
「開発元が未確認のため開けません」というエラーが表示される

**解決方法:**

1. **システム環境設定を開く**
2. **「セキュリティとプライバシー」**を選択
3. **「一般」タブで「このまま許可」**をクリック

または、ターミナルで:
```bash
xattr -d com.apple.quarantine /path/to/claude
```

---

## ログイン・認証の問題

### 問題5: ログインできない

**症状:**
```
Authentication failed
```

**原因:**
- Claudeアカウントが作成されていない
- 課金設定が完了していない
- ブラウザのポップアップがブロックされている

**解決方法:**

1. **Claudeアカウントを作成:**
   [https://claude.ai](https://claude.ai) または [https://console.anthropic.com](https://console.anthropic.com)でアカウントを作成してください。

2. **課金設定を確認:**
   Claude Consoleで課金設定を完了させてください。

3. **ブラウザのポップアップブロックを解除:**
   ブラウザの設定で、claude.aiのポップアップを許可してください。

4. **手動でログイン:**
   ```bash
   claude login
   ```

---

### 問題6: 「API key not found」エラー

**症状:**
```
Error: API key not found
```

**原因:**
- ログインが完了していない
- APIキーの設定が削除された

**解決方法:**

1. **再度ログイン:**
   ```bash
   claude login
   ```

2. **設定ファイルを確認:**
   ```bash
   cat ~/.config/claude/config.json
   ```

   APIキーが含まれているか確認してください。

---

### 問題7: 「Unauthorized」エラー

**症状:**
```
Error: 401 Unauthorized
```

**原因:**
- APIキーが無効
- 課金設定が無効

**解決方法:**

1. **Claude Consoleで課金設定を確認:**
   [https://console.anthropic.com](https://console.anthropic.com)にアクセスし、課金設定が有効か確認してください。

2. **再度ログイン:**
   ```bash
   claude logout
   claude login
   ```

---

## 実行時の問題

### 問題8: Claude Codeが応答しない

**症状:**
コマンドを入力しても反応がない、または非常に遅い

**原因:**
- ネットワークの問題
- Claude APIの障害
- 大量のファイルを読み込んでいる

**解決方法:**

1. **インターネット接続を確認:**
   ```bash
   ping claude.ai
   ```

2. **Claude APIのステータスを確認:**
   [https://status.anthropic.com](https://status.anthropic.com)でAPIの状態を確認してください。

3. **セッションを再起動:**
   `Ctrl+C`で現在のセッションを終了し、再度起動してください。

4. **ファイルの数を減らす:**
   不要なファイルを`.gitignore`や`.claudeignore`に追加してください。

---

### 問題9: 「Too many requests」エラー

**症状:**
```
Error: 429 Too Many Requests
```

**原因:**
- レート制限に達した

**解決方法:**

1. **数分待ってから再試行:**
   レート制限は通常、数分後に解除されます。

2. **リクエストの頻度を減らす:**
   短時間に大量のリクエストを送らないようにしてください。

---

### 問題10: ファイルが編集されない

**症状:**
ファイルを編集するように指示したが、変更されていない

**原因:**
- ファイルパスが間違っている
- ファイルの権限がない
- 指示が曖昧

**解決方法:**

1. **ファイルパスを確認:**
   ```
   @src/components/Header.tsx を編集して
   ```

   正確なパスを指定してください。

2. **ファイルの権限を確認:**
   ```bash
   ls -l src/components/Header.tsx
   ```

   書き込み権限があるか確認してください。

3. **具体的に指示:**
   ```
   @src/components/Header.tsx の15行目の「Hello」を「こんにちは」に変更して
   ```

---

### 問題11: 生成されたコードが動かない

**症状:**
Claudeが生成したコードを実行すると、エラーが発生する

**原因:**
- プロジェクトの環境を正しく理解していない
- 依存関係が不足している
- 指示が不十分

**解決方法:**

1. **CLAUDE.mdにプロジェクト情報を記載:**
   技術スタック、コーディング規約、ディレクトリ構造などを明記してください。

2. **エラーメッセージを共有:**
   ```
   このコードを実行したら以下のエラーが発生しました:
   [エラーメッセージ]
   修正してください。
   ```

3. **具体的に指示:**
   ```
   このプロジェクトはTypeScript + React 18を使っています。
   最新のHooksパターンでコンポーネントを作成してください。
   ```

---

## パフォーマンスの問題

### 問題12: 動作が遅い

**症状:**
Claude Codeの応答が非常に遅い

**原因:**
- 大量のファイルを読み込んでいる
- ネットワークが遅い
- 複雑な処理を実行している

**解決方法:**

1. **`.claudeignore`ファイルを作成:**
   不要なファイルを除外してください。

   ```bash
   # .claudeignore
   node_modules/
   dist/
   build/
   .git/
   *.log
   ```

2. **必要なファイルだけを指定:**
   ```
   @src/components/Header.tsx だけを見て、問題を確認して
   ```

3. **会話履歴をクリア:**
   ```
   /clear
   ```

---

### 問題13: メモリ不足エラー

**症状:**
```
Error: Out of memory
```

**原因:**
- 大量のファイルを一度に処理しようとしている

**解決方法:**

1. **タスクを小分けにする:**
   大きなタスクは小さく分割してください。

2. **不要なファイルを除外:**
   `.claudeignore`で不要なファイルを除外してください。

3. **セッションを再起動:**
   定期的にセッションを終了して、再起動してください。

---

## 課金・コストの問題

### 問題14: 予想以上にコストがかかる

**症状:**
想定していたよりも課金額が高い

**原因:**
- 長時間セッションを開きっぱなしにしている
- 大量のファイルを読み込んでいる
- 頻繁にリクエストを送っている

**解決方法:**

1. **セッションを適切に終了:**
   作業が終わったら必ず`exit`してください。

2. **会話履歴をクリア:**
   長い会話は文脈保持のためにコストがかかるので、定期的に`/clear`してください。

3. **必要最小限の情報だけを共有:**
   ファイル全体ではなく、必要な部分だけを指定してください。

4. **単発モードを活用:**
   簡単な質問には単発モードを使ってください:
   ```bash
   claude "package.jsonの依存関係を確認して"
   ```

---

### 問題15: 課金がうまく設定できない

**症状:**
Claude Consoleで課金設定ができない

**解決方法:**

1. **Claude Consoleにアクセス:**
   [https://console.anthropic.com](https://console.anthropic.com)

2. **「Billing」セクションで支払い方法を追加:**
   クレジットカード情報を登録してください。

3. **サポートに問い合わせ:**
   問題が解決しない場合は、Anthropicのサポートに問い合わせてください。

---

## その他の問題

### 問題16: Skillsが動作しない

**症状:**
Skillsを呼び出しても動作しない

**原因:**
- ファイルの場所が間違っている
- ファイル名が間違っている
- 内容が正しくない

**解決方法:**

1. **ファイルの場所を確認:**
   `.claude/skills/`ディレクトリに配置されているか確認してください。

2. **ファイル名を確認:**
   `.md`拡張子がついているか確認してください。

3. **内容を確認:**
   Markdownファイルの内容が正しいか確認してください。

4. **Claudeを再起動:**
   新しいスキルを追加した場合、Claudeを再起動してください。

---

### 問題17: CLAUDE.mdが読み込まれない

**症状:**
CLAUDE.mdに記載した内容がClaude Codeに反映されない

**原因:**
- ファイルの場所が間違っている
- ファイル名が間違っている

**解決方法:**

1. **ファイルの場所を確認:**
   プロジェクトのルートディレクトリに`CLAUDE.md`があるか確認してください。

2. **ファイル名を確認:**
   `CLAUDE.md`（大文字）が正しいファイル名です。`claude.md`（小文字）では認識されません。

3. **Claudeを再起動:**
   CLAUDE.mdを編集した場合、Claudeを再起動してください。

---

### 問題18: Gitの操作でエラーが発生

**症状:**
Claudeにコミットやプッシュを依頼すると、エラーが発生する

**原因:**
- Git設定が不足
- 認証情報が不足

**解決方法:**

1. **Git設定を確認:**
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "your.email@example.com"
   ```

2. **認証情報を設定:**
   GitHubなどを使用している場合、SSH鍵やアクセストークンを設定してください。

3. **手動で操作:**
   エラーが解決しない場合は、手動でGit操作を行ってください。

---

### 問題19: Windows で動作しない

**症状:**
WindowsでClaude Codeが正しく動作しない

**解決方法:**

1. **WSLを使用:**
   Windows Subsystem for Linux (WSL)を使用することを推奨します。

   ```bash
   wsl --install
   ```

2. **PowerShellを管理者として実行:**
   PowerShellを管理者として実行してください。

3. **実行ポリシーを変更:**
   ```powershell
   Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
   ```

---

### 問題20: 日本語が正しく表示されない

**症状:**
日本語が文字化けする

**解決方法:**

1. **ターミナルのエンコーディングを確認:**
   UTF-8に設定されているか確認してください。

   **Mac/Linux:**
   ```bash
   echo $LANG
   ```

   `ja_JP.UTF-8`や`en_US.UTF-8`になっているか確認してください。

2. **ターミナルの設定を変更:**
   ターミナルの設定で、文字エンコーディングをUTF-8に設定してください。

---

## まだ解決しない場合

### 公式サポート

問題が解決しない場合は、以下のリソースを参照してください:

1. **公式ドキュメント:**
   [https://code.claude.com/docs](https://code.claude.com/docs)

2. **GitHub Issues:**
   [https://github.com/anthropics/claude-code/issues](https://github.com/anthropics/claude-code/issues)

3. **コミュニティフォーラム:**
   公式フォーラムやDiscordサーバーで質問してください。

4. **Anthropicサポート:**
   [https://support.anthropic.com](https://support.anthropic.com)

---

### デバッグ情報の収集

サポートに問い合わせる際は、以下の情報を含めると、より迅速に解決できます:

1. **Claude Codeのバージョン:**
   ```bash
   claude --version
   ```

2. **OS情報:**
   ```bash
   # Mac/Linux
   uname -a

   # Windows
   systeminfo
   ```

3. **エラーメッセージ:**
   エラーメッセージの全文をコピーしてください。

4. **再現手順:**
   問題を再現する手順を詳しく記載してください。

---

## 次のステップ

問題が解決したら、以下のガイドに戻って開発を続けましょう:

- [基本的な使い方ガイド](./02-getting-started.md)
- [Tips集](./05-tips-and-tricks.md)

---

**公式ドキュメント:**
- [Claude Code Docs](https://code.claude.com/docs)
- [Anthropic Support](https://support.anthropic.com)
