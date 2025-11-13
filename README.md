# Claude Code 導入・活用ガイド

非エンジニアのプログラミング初学者向けの、Claude Code完全ガイドです。

---

## このガイドについて

このリポジトリは、**Claude Code**の導入から活用までを、プログラミング初学者向けにわかりやすくまとめた学習資料です。

Claude Codeは、Anthropic社が開発したAI駆動のターミナルベースの開発支援ツールです。コードの生成、デバッグ、リファクタリング、コードベースの理解など、開発作業を強力にサポートします。

---

## 対象読者

- プログラミングを学び始めたばかりの方
- Claude Codeを初めて使う方
- 個人開発でClaude Codeを活用したい方
- AI開発ツールに興味がある方

---

## 📚 ドキュメント構成

### 1. [導入ガイド](./docs/01-installation.md)

Claude Codeのインストール方法を、Mac・Windows両方について詳しく解説します。

**内容:**
- Claude Codeとは
- システム要件
- Mac・Windowsそれぞれのインストール手順
- 初期設定
- 動作確認

👉 **まずはここから!** インストールがまだの方は、このガイドから始めてください。

---

### 2. [基本的な使い方ガイド](./docs/02-getting-started.md)

Claude Codeの基本操作と、実践的なワークフローを学びます。

**内容:**
- 対話モードの使い方
- よく使うコマンド
- コードベースの理解
- バグ修正の方法
- 新機能の追加
- リファクタリング
- ファイル操作

👉 **インストール完了後はこちら!** Claude Codeの基本的な使い方を身につけましょう。

---

### 3. [CLAUDE.mdガイド](./docs/03-claude-md-guide.md)

プロジェクト設定ファイル「CLAUDE.md」の作成方法と活用方法を解説します。

**内容:**
- CLAUDE.mdとは
- メモリの階層構造
- CLAUDE.mdの作成方法
- 含めるべき内容
- 実践的な例
- ベストプラクティス

👉 **プロジェクトごとに設定したい方へ!** プロジェクト固有の設定やルールを記録する方法を学びましょう。

---

### 4. [Skillsガイド](./docs/04-skills-guide.md)

Claude Codeに新しい能力を追加する「Skills」の作成と活用方法を学びます。

**内容:**
- Skillsとは
- Skillsの仕組み
- Skillsの作成方法
- 実践的なSkills例
- ベストプラクティス

👉 **作業を自動化したい方へ!** 定型作業をスキルとして登録し、効率的に開発しましょう。

---

### 5. [Tips集](./docs/05-tips-and-tricks.md)

個人開発で役立つテクニックやコツを紹介します。

**内容:**
- 効率的な開発フロー
- プロジェクト初期設定
- コード品質向上
- デバッグテクニック
- 学習・理解を深める方法
- 時間節約のコツ
- コスト最適化
- よくあるシーン別Tips

👉 **もっと効率的に使いたい方へ!** 実践的なテクニックで開発スピードを向上させましょう。

---

### 6. [トラブルシューティング](./docs/06-troubleshooting.md)

よくある問題とその解決方法をまとめています。

**内容:**
- インストール時の問題
- ログイン・認証の問題
- 実行時の問題
- パフォーマンスの問題
- 課金・コストの問題

👉 **困ったときはこちら!** 問題が発生した際の対処法を確認できます。

---

## 📁 サンプルファイル

実際にプロジェクトで使えるサンプルファイルを用意しています。

### [CLAUDE.md サンプル](./examples/CLAUDE.md.example)

実践的なCLAUDE.mdのサンプルです。コピーして自分のプロジェクトに合わせてカスタマイズできます。

**含まれる内容:**
- プロジェクト概要
- 技術スタック
- よく使うコマンド
- ディレクトリ構造
- コーディング規約
- 設計パターン
- 注意事項

### [Skills サンプル](./examples/skills/)

すぐに使える実用的なスキルのサンプルです。

- **[create-react-component.md](./examples/skills/create-react-component.md)**: 新しいReactコンポーネントを作成するスキル
- **[run-tests-and-fix.md](./examples/skills/run-tests-and-fix.md)**: テストを実行して失敗を自動修正するスキル
- **[lint-and-format.md](./examples/skills/lint-and-format.md)**: Lintエラーを修正してコードをフォーマットするスキル
- **[create-api-endpoint.md](./examples/skills/create-api-endpoint.md)**: 新しいAPIエンドポイントを作成するスキル

---

## 🚀 クイックスタート

### ステップ1: インストール

**Mac:**
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell（管理者として実行）:**
```powershell
irm https://claude.ai/install.ps1 | iex
```

詳しい手順は [導入ガイド](./docs/01-installation.md) を参照してください。

---

### ステップ2: ログイン

```bash
claude
```

初回起動時にブラウザでログイン画面が開きます。Claudeアカウントでログインしてください。

---

### ステップ3: 使ってみる

プロジェクトディレクトリで Claude Code を起動:

```bash
cd /path/to/your/project
claude
```

対話モードで質問してみましょう:

```
このプロジェクトの概要を教えて
```

---

## 💡 おすすめの学習順序

1. **[導入ガイド](./docs/01-installation.md)** でインストール
2. **[基本的な使い方ガイド](./docs/02-getting-started.md)** で基本操作を習得
3. **[CLAUDE.mdガイド](./docs/03-claude-md-guide.md)** でプロジェクト設定を学ぶ
4. **[Skillsガイド](./docs/04-skills-guide.md)** で作業の自動化を学ぶ
5. **[Tips集](./docs/05-tips-and-tricks.md)** でさらに効率化

困ったときは **[トラブルシューティング](./docs/06-troubleshooting.md)** を参照してください。

---

## 📖 学習のコツ

### 1. 小さく始める

最初から完璧を目指さず、簡単なタスクから始めましょう。

**おすすめの最初のタスク:**
- 既存のコードベースの概要を聞く
- 簡単なバグを修正してもらう
- コードの説明を求める

### 2. 具体的に指示する

曖昧な指示ではなく、具体的で明確な指示を心がけましょう。

**悪い例:**
```
コードをきれいにして
```

**良い例:**
```
src/utils/helper.js のコードを以下の点で改善して:
1. 関数名をより説明的にする
2. コメントを追加する
3. 重複したコードを削除する
```

### 3. 理解できない部分は質問する

Claudeの回答や生成したコードが理解できない場合は、遠慮なく質問してください。

```
さっきのコードで、なぜasync/awaitを使っているの?
```

### 4. 段階的に進める

複雑なタスクは、小さなステップに分割して進めます。

---

## 🎯 実践例

### 例1: 新しいプロジェクトを始める

```bash
mkdir my-app
cd my-app
claude
```

```
React + TypeScriptでタスク管理アプリを作りたい。
推奨されるプロジェクト構成を教えて。
```

```
/init
```

```
推奨してくれた構成でプロジェクトのスケルトンを作成して。
```

### 例2: バグを修正する

```bash
claude
```

```
以下のエラーが発生しました:

TypeError: Cannot read property 'name' of undefined
  at getUserName (user.js:15)

このエラーを修正する方法をいくつか提案して。
```

```
2番目の方法で修正して。テストも書いてください。
```

### 例3: 新機能を追加する

```
ユーザーがプロフィール画像をアップロードできる機能を追加したい。
```

```
まず、フロントエンドのUI（ファイル選択とプレビュー）を作成して
```

```
次に、画像をS3にアップロードするAPIエンドポイントを作成して
```

---

## ⚠️ 注意事項

### コストについて

Claude Codeは使用量に応じて課金されます。以下の点に注意してください:

- 作業が終わったらセッションを終了する（`exit`）
- 長い会話は定期的にクリアする（`/clear`）
- 必要最小限の情報だけを共有する

詳しくは [Tips集](./docs/05-tips-and-tricks.md) の「コスト最適化」セクションを参照してください。

### セキュリティについて

- **機密情報を共有しない**: APIキー、パスワード、個人情報などをClaude Codeに入力しないでください
- **コード確認**: 生成されたコードは必ず確認してから使用してください

---

## 🔗 公式リソース

- **公式ドキュメント**: [https://code.claude.com/docs](https://code.claude.com/docs)
- **Claude API Documentation**: [https://docs.anthropic.com](https://docs.anthropic.com)
- **GitHub Issues**: [https://github.com/anthropics/claude-code/issues](https://github.com/anthropics/claude-code/issues)
- **サポート**: [https://support.anthropic.com](https://support.anthropic.com)

---

## 🤝 コントリビューション

このガイドの改善案やバグ報告は、GitHubのIssueまたはPull Requestでお願いします。

---

## 📄 ライセンス

このガイドは、学習目的で自由に使用できます。

---

## 🎓 次のステップ

1. **[導入ガイド](./docs/01-installation.md)** からインストールを始めましょう
2. **[基本的な使い方ガイド](./docs/02-getting-started.md)** で基本操作を学びましょう
3. 実際のプロジェクトでClaude Codeを使ってみましょう

楽しいClaude Code開発ライフを!