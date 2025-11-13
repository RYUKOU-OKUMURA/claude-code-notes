# Claude Code Skills ガイド

## 目次
- [Skillsとは](#skillsとは)
- [Skillsの仕組み](#skillsの仕組み)
- [Skillsの作成方法](#skillsの作成方法)
- [Skillsの使い方](#skillsの使い方)
- [実践的なSkills例](#実践的なskills例)
- [ベストプラクティス](#ベストプラクティス)
- [よくある質問](#よくある質問)

---

## Skillsとは

**Skills（スキル）**は、Claude Codeに新しい能力を追加するための拡張機能です。

### 基本概念

Skillsを使うと、以下のようなことができます:

- **定型作業を自動化**: 頻繁に行うタスクをスキルとして登録
- **専門知識をパッケージ化**: 特定の技術やツールに関する知識を再利用可能な形で保存
- **チームで共有**: 便利なスキルをチームメンバーと共有

**イメージ:**
Skillsは、Claudeに教える「マニュアル」や「レシピ」のようなものです。一度作成すれば、何度でも呼び出して使えます。

---

## Skillsの仕組み

### Skillsの基本構造

Skillsは、`.claude/skills/`ディレクトリに配置されたMarkdownファイルです。

```
your-project/
├── .claude/
│   └── skills/
│       ├── deploy.md          # デプロイスキル
│       ├── create-component.md # コンポーネント作成スキル
│       └── setup-test.md      # テストセットアップスキル
└── CLAUDE.md
```

### Skillの内容

各スキルファイルには、Claudeに実行してほしい手順やルールを記述します。

**例: `deploy.md`**

```markdown
# Deploy Skill

このスキルは、アプリケーションを本番環境にデプロイします。

## 手順

1. テストをすべて実行し、合格を確認
2. ビルドを実行
3. ビルド成果物を検証
4. デプロイコマンドを実行: `npm run deploy:prod`
5. デプロイが成功したか確認
6. 本番環境のヘルスチェックを実行

## 注意事項

- 必ず`main`ブランチからデプロイすること
- デプロイ前にチームに通知すること
- ロールバック手順を確認しておくこと
```

---

## Skillsの作成方法

### 方法1: 手動で作成

#### ステップ1: ディレクトリを作成

プロジェクトのルートディレクトリで、`.claude/skills/`ディレクトリを作成します:

```bash
mkdir -p .claude/skills
```

#### ステップ2: スキルファイルを作成

`.claude/skills/`ディレクトリ内に、Markdownファイルを作成します:

```bash
touch .claude/skills/my-skill.md
```

#### ステップ3: スキルの内容を記述

エディタでファイルを開き、スキルの内容を記述します:

```markdown
# My Skill

このスキルの説明をここに書きます。

## 実行手順

1. 手順1
2. 手順2
3. 手順3

## 注意事項

- 注意点1
- 注意点2
```

---

### 方法2: Claudeに作成してもらう

Claude Codeの対話モード中に、Claudeにスキルを作成してもらうこともできます:

```bash
claude
```

対話モードで:

```
新しいReactコンポーネントを作成するスキルを作って。
以下の手順を含めて:
1. コンポーネントファイルを作成
2. スタイルファイルを作成
3. テストファイルを作成
4. index.tsにエクスポートを追加

スキル名は "create-react-component" にして。
```

Claudeが`.claude/skills/create-react-component.md`を作成してくれます。

---

## Skillsの使い方

### スキルを呼び出す

対話モード中に、スキル名を指定してスキルを実行します:

```bash
claude
```

対話モードで:

```
/skill deploy
```

または、自然言語で:

```
deployスキルを実行して
```

Claudeがスキルの内容を読み取り、手順に従って作業を進めます。

---

### スキルの一覧を確認

利用可能なスキルの一覧を確認するには:

```
/skills
```

または:

```
利用可能なスキルを教えて
```

---

## 実践的なSkills例

### 例1: コンポーネント作成スキル

**ファイル:** `.claude/skills/create-component.md`

```markdown
# Create React Component Skill

新しいReactコンポーネントを作成します。

## 実行手順

1. コンポーネント名をユーザーに確認
2. `src/components/[ComponentName]/`ディレクトリを作成
3. 以下のファイルを作成:
   - `[ComponentName].tsx` - コンポーネント本体
   - `[ComponentName].module.css` - スタイル
   - `[ComponentName].test.tsx` - テスト
   - `index.ts` - エクスポート
4. コンポーネントのボイラープレートコードを生成
5. `src/components/index.ts`に新しいコンポーネントをエクスポート

## コンポーネントのテンプレート

```tsx
import React from 'react';
import styles from './[ComponentName].module.css';

interface [ComponentName]Props {
  // Props definition
}

export const [ComponentName]: React.FC<[ComponentName]Props> = (props) => {
  return (
    <div className={styles.container}>
      {/* Component content */}
    </div>
  );
};
```

## 注意事項

- コンポーネント名はパスカルケースで
- PropTypesではなくTypeScriptの型定義を使用
- アクセシビリティを考慮したマークアップ
```

**使い方:**

```
/skill create-component
```

Claudeが対話的にコンポーネント名を聞き、必要なファイルを自動生成してくれます。

---

### 例2: テスト実行スキル

**ファイル:** `.claude/skills/run-tests.md`

```markdown
# Run Tests Skill

テストを実行し、失敗したテストを修正します。

## 実行手順

1. `npm run test`を実行
2. テスト結果を分析
3. 失敗したテストがある場合:
   - 失敗の原因を特定
   - コードを修正
   - 再度テストを実行
   - すべてのテストが通るまで繰り返す
4. すべてのテストが通ったら、カバレッジレポートを生成
5. カバレッジが80%未満の場合、警告を表示

## 注意事項

- テスト修正時は、既存の動作を変更しないこと
- カバレッジが低い場合は、新しいテストの追加を提案
```

---

### 例3: Lintとフォーマット

**ファイル:** `.claude/skills/lint-and-format.md`

```markdown
# Lint and Format Skill

コードのLintエラーを修正し、フォーマットを整えます。

## 実行手順

1. `npm run lint`を実行
2. Lintエラーを確認
3. 自動修正可能なエラーは`npm run lint:fix`で修正
4. 手動修正が必要なエラーを一つずつ修正
5. `npm run format`を実行してコードをフォーマット
6. 再度Lintを実行して、エラーがないことを確認

## 注意事項

- コードの動作を変更しないこと
- 既存のコードスタイルに従うこと
```

---

### 例4: APIエンドポイント作成

**ファイル:** `.claude/skills/create-api-endpoint.md`

```markdown
# Create API Endpoint Skill

新しいAPIエンドポイントを作成します。

## 実行手順

1. エンドポイントのパスとHTTPメソッドをユーザーに確認
2. `src/routes/`に新しいルートファイルを作成
3. `src/controllers/`に対応するコントローラーを作成
4. `src/services/`にビジネスロジックを作成
5. バリデーションスキーマを定義
6. エラーハンドリングを実装
7. ユニットテストを作成
8. 統合テストを作成
9. APIドキュメント（Swagger）を更新

## エンドポイントのテンプレート

```typescript
// routes/example.ts
import { Router } from 'express';
import { exampleController } from '../controllers/exampleController';
import { validate } from '../middlewares/validate';
import { exampleSchema } from '../schemas/exampleSchema';

const router = Router();

router.post('/example', validate(exampleSchema), exampleController.create);

export default router;
```

## 注意事項

- 認証ミドルウェアを適用すること
- レート制限を考慮すること
- 適切なHTTPステータスコードを返すこと
```

---

### 例5: データベースマイグレーション

**ファイル:** `.claude/skills/create-migration.md`

```markdown
# Create Database Migration Skill

データベースマイグレーションファイルを作成します。

## 実行手順

1. マイグレーションの内容をユーザーに確認
2. `npm run migration:create [name]`を実行
3. 生成されたマイグレーションファイルを開く
4. `up`メソッドに変更内容を記述
5. `down`メソッドにロールバック処理を記述
6. マイグレーションをテスト環境で実行: `npm run migration:run`
7. ロールバックをテスト: `npm run migration:revert`
8. 問題がなければコミット

## 注意事項

- `down`メソッドは必ず実装すること
- 本番環境では慎重にマイグレーションを実行
- データの損失がないか確認
```

---

## ベストプラクティス

### 1. 明確な名前をつける

スキル名は、何をするスキルなのかが一目でわかる名前にします。

**良い例:**
- `create-react-component.md`
- `deploy-to-production.md`
- `run-all-tests.md`

**悪い例:**
- `skill1.md`
- `helper.md`
- `misc.md`

---

### 2. 手順を具体的に書く

曖昧な指示ではなく、具体的で明確な手順を記述します。

**悪い例:**
```markdown
## 手順

1. テストを実行
2. 問題があれば修正
```

**良い例:**
```markdown
## 手順

1. `npm run test`を実行
2. テスト結果を分析し、失敗したテストをリストアップ
3. 失敗したテストごとに:
   - エラーメッセージを確認
   - 該当するコードを特定
   - 原因を分析
   - コードを修正
   - 再度テストを実行
4. すべてのテストが通るまで繰り返す
```

---

### 3. 注意事項を明記

スキル実行時の注意点や制約を明記します。

```markdown
## 注意事項

- 本番環境では実行しないこと
- 必ずバックアップを取ること
- チームに事前通知すること
```

---

### 4. テンプレートを含める

コード生成を伴うスキルでは、テンプレートを含めると便利です。

```markdown
## コードテンプレート

```typescript
// Template code here
```
```

---

### 5. スキルを小さく保つ

1つのスキルは1つの責務に集中させます。複雑な作業は、複数のスキルに分割します。

**悪い例:**
- `setup-entire-project.md` （すべてを含む巨大なスキル）

**良い例:**
- `setup-dependencies.md`
- `setup-database.md`
- `setup-env-variables.md`
- `setup-git-hooks.md`

---

### 6. スキルをバージョン管理

スキルファイルはGitにコミットし、チームで共有します。

```bash
git add .claude/skills/
git commit -m "feat: Add component creation skill"
```

---

## よくある質問

### Q: SkillsとCLAUDE.mdの違いは?

**A:**

| | CLAUDE.md | Skills |
|---|---|---|
| **用途** | プロジェクト全体の設定・ルール | 特定のタスクの手順 |
| **内容** | コーディング規約、技術スタック、ディレクトリ構造など | デプロイ、テスト実行、コンポーネント作成などの手順 |
| **実行** | 自動的に読み込まれる | 明示的に呼び出す |

**簡単に言うと:**
- CLAUDE.md = 「プロジェクトの説明書」
- Skills = 「作業手順書」

---

### Q: Skillsはどこに保存すべき?

**A:**

- **プロジェクト固有のスキル**: `.claude/skills/` （Gitにコミット）
- **個人的なスキル**: `~/.config/claude/skills/` （個人利用のみ）

チームで共有したい場合は、プロジェクトの`.claude/skills/`に配置します。

---

### Q: Skillsを作るべきタイミングは?

**A:**

以下のような場合、Skillsの作成を検討しましょう:

- 同じ作業を何度も繰り返している
- 複数のステップからなる定型作業がある
- チームメンバーに手順を共有したい
- 新しいメンバーのオンボーディングを効率化したい

---

### Q: Skillsを実行しても動かない

**A:**

以下を確認してください:

1. **ファイルの場所**: `.claude/skills/`ディレクトリに配置されているか
2. **ファイル名**: `.md`拡張子がついているか
3. **内容**: 手順が明確に記述されているか
4. **Claude Codeの再起動**: 新しいスキルを追加した場合、Claudeを再起動してみる

---

### Q: スキルをチームで共有するには?

**A:**

1. `.claude/skills/`に スキルファイルを作成
2. Gitにコミット:
   ```bash
   git add .claude/skills/
   git commit -m "Add new skill"
   git push
   ```
3. チームメンバーがpullすれば、すぐに使えます

---

### Q: 既存のスキルを探すには?

**A:**

公式のスキルリポジトリや、コミュニティが共有しているスキルを探すことができます（将来的に）。

現時点では、以下の方法でスキルを見つけられます:
- チーム内で共有されているスキル
- 公式ドキュメントのサンプル
- GitHubなどで公開されているサンプルプロジェクト

---

## 次のステップ

Skillsの使い方を理解したら、次のガイドに進みましょう:

- [Tips集](./05-tips-and-tricks.md): 個人開発で役立つテクニック
- [サンプルファイル](../examples/): 実際に使えるスキルの例

---

**公式ドキュメント:**
- [Skills Documentation](https://code.claude.com/docs/en/skills.md)
