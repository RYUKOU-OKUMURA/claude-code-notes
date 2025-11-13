# Create React Component Skill

新しいReactコンポーネントを作成します。Atomic Design原則に従い、必要なファイル（コンポーネント、スタイル、テスト）を一括で生成します。

---

## 実行手順

1. **コンポーネント名をユーザーに確認**
   - パスカルケースで入力してもらう（例: `UserProfile`, `TaskCard`）
   - コンポーネントの種類を確認（atom, molecule, organism, template, page）

2. **ディレクトリを作成**
   - `src/components/[type]/[ComponentName]/`ディレクトリを作成

3. **以下のファイルを作成:**
   - `[ComponentName].tsx` - コンポーネント本体
   - `[ComponentName].module.css` - スタイル
   - `[ComponentName].test.tsx` - テスト
   - `index.ts` - エクスポート

4. **コンポーネントのボイラープレートコードを生成**
   - TypeScript + React 18の最新パターン
   - Functional Componentを使用
   - Props型定義を含める

5. **src/components/index.tsに新しいコンポーネントをエクスポート**

---

## コンポーネントのテンプレート

### [ComponentName].tsx

```tsx
import React from 'react';
import styles from './[ComponentName].module.css';

interface [ComponentName]Props {
  // TODO: Props定義を追加
}

export const [ComponentName]: React.FC<[ComponentName]Props> = (props) => {
  return (
    <div className={styles.container}>
      <h2>[ComponentName]</h2>
      {/* TODO: コンポーネントの実装 */}
    </div>
  );
};
```

### [ComponentName].module.css

```css
.container {
  /* TODO: スタイルを追加 */
}
```

### [ComponentName].test.tsx

```tsx
import { render, screen } from '@testing-library/react';
import { [ComponentName] } from './[ComponentName]';

describe('[ComponentName]', () => {
  it('should render successfully', () => {
    render(<[ComponentName] />);
    expect(screen.getByText('[ComponentName]')).toBeInTheDocument();
  });

  // TODO: 追加のテストケースを記述
});
```

### index.ts

```ts
export { [ComponentName] } from './[ComponentName]';
export type { [ComponentName]Props } from './[ComponentName]';
```

---

## 注意事項

- **コンポーネント名**: 必ずパスカルケースで
- **Props型定義**: TypeScriptの型定義を必ず使用（PropTypesは使わない）
- **スタイル**: CSS Modulesを使用（グローバルCSSは避ける）
- **アクセシビリティ**: 適切なHTMLタグとARIA属性を使用
- **再利用性**: 汎用的なコンポーネントになるよう設計

---

## 使用例

このスキルを実行するには:

```
/skill create-react-component
```

または:

```
新しいReactコンポーネントを作成して
```
