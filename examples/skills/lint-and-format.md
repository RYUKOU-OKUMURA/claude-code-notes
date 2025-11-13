# Lint and Format Skill

コードのLintエラーを修正し、フォーマットを整えます。

---

## 実行手順

1. **Lintを実行**
   - `npm run lint`を実行
   - Lintエラーとwarningを確認

2. **自動修正を試みる**
   - `npm run lint:fix`を実行
   - 自動修正可能なエラーを修正

3. **手動修正が必要なエラーを修正**
   - 残っているLintエラーを一つずつ確認
   - エラーの種類に応じて適切に修正:
     - **unused variable**: 使用されていない変数を削除または使用
     - **missing dependency**: useEffectなどの依存配列に追加
     - **any type**: 適切な型定義に変更
     - **console.log**: 本番コードから削除

4. **フォーマットを実行**
   - `npm run format`を実行
   - コードを統一されたスタイルにフォーマット

5. **再度Lintを実行**
   - `npm run lint`を実行
   - すべてのエラーが解消されているか確認

6. **型チェック**
   - `npm run type-check`を実行（TypeScriptプロジェクトの場合）
   - 型エラーがあれば修正

---

## 修正の優先順位

1. **エラー（error）**: 必ず修正
2. **警告（warning）**: できる限り修正
3. **情報（info）**: 必要に応じて修正

---

## 注意事項

- **コードの動作を変更しない**: Lintやフォーマットの修正でロジックを変えないこと
- **既存のコードスタイルに従う**: プロジェクトの既存のパターンを尊重
- **段階的に修正**: 大量のエラーがある場合は、ファイルごとに段階的に修正
- **テストを実行**: 修正後、テストを実行して動作確認

---

## Lintルールの理解

主要なLintルールの意味:

- **no-unused-vars**: 使用されていない変数は削除
- **no-console**: `console.log`は開発中のみ使用
- **@typescript-eslint/no-explicit-any**: `any`型の使用を避ける
- **react-hooks/exhaustive-deps**: useEffectの依存配列に必要な変数を含める
- **import/order**: インポート文を整理

---

## 使用例

このスキルを実行するには:

```
/skill lint-and-format
```

または:

```
Lintエラーを修正してコードをフォーマットして
```
