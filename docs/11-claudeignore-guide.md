# Claude Code .claudeignore ガイド

## 目次
- [.claudeignoreとは](#claudeignoreとは)
- [.claudeignoreの作成方法](#claudeignoreの作成方法)
- [記述方法とパターン](#記述方法とパターン)
- [よく使うパターン集](#よく使うパターン集)
- [実践的な設定例](#実践的な設定例)
- [ベストプラクティス](#ベストプラクティス)
- [よくある質問](#よくある質問)

---

## .claudeignoreとは

**`.claudeignore`**は、Claude Codeに読み込ませたくないファイルやディレクトリを指定するための設定ファイルです。

### 基本概念

プロジェクトには、Claude Codeが読み込む必要のないファイルがたくさんあります：

- **依存関係**: `node_modules/`、`vendor/`
- **ビルド成果物**: `dist/`、`build/`
- **一時ファイル**: ログファイル、キャッシュ
- **大きなファイル**: 画像、動画、データベースダンプ
- **機密情報**: `.env`、秘密鍵

これらを`.claudeignore`で除外することで：

✅ **パフォーマンス向上**: Claude Codeの読み込みが高速化
✅ **コスト削減**: 不要なファイルを読み込まないため、トークン消費量が減少
✅ **セキュリティ向上**: 機密情報の誤送信を防止

**イメージ:**
`.gitignore`と同じような仕組みです。

```
プロジェクト全体
  ├── src/          ✅ Claude Codeが読み込む
  ├── node_modules/ ❌ .claudeignoreで除外
  ├── dist/         ❌ .claudeignoreで除外
  └── .env          ❌ .claudeignoreで除外
```

---

## .claudeignoreの作成方法

### ステップ1: ファイルを作成

プロジェクトのルートディレクトリに `.claudeignore` ファイルを作成します：

```bash
touch .claudeignore
```

---

### ステップ2: 除外するパターンを記述

エディタで `.claudeignore` を開き、除外したいファイルやディレクトリを記述します：

```bash
# 依存関係
node_modules/
vendor/

# ビルド成果物
dist/
build/
out/

# ログファイル
*.log
logs/

# 環境変数
.env
.env.local
```

---

### ステップ3: 動作確認

Claude Codeを起動して、除外が正しく動作しているか確認します：

```bash
claude
```

対話モードで:

```
プロジェクト内のファイルを一覧表示して
```

`.claudeignore`で除外したファイルが表示されないことを確認します。

---

## 記述方法とパターン

`.claudeignore` は `.gitignore` と同じ記法を使います。

### 基本的なパターン

#### 1. ファイル名で除外

特定のファイル名を除外：

```
# ファイル名を直接指定
secret.txt
config.json
```

---

#### 2. ディレクトリを除外

ディレクトリ全体を除外：

```
# ディレクトリを指定（末尾に/をつける）
node_modules/
build/
dist/
```

---

#### 3. ワイルドカード（*）

`*` は任意の文字列にマッチ：

```
# 全てのログファイルを除外
*.log

# 全てのバックアップファイルを除外
*.bak
*.backup

# 全てのDSStoreファイルを除外
.DS_Store
```

---

#### 4. 再帰的なマッチ（**）

`**` は任意のディレクトリ階層にマッチ：

```
# 全ての__pycache__ディレクトリを除外
**/__pycache__/

# 全てのtestディレクトリのビルド成果物を除外
**/test/dist/
```

---

#### 5. 否定パターン（!）

`!` で除外を解除：

```
# 全てのlogファイルを除外
*.log

# ただしimportant.logは除外しない
!important.log
```

---

#### 6. コメント

`#` でコメントを記述：

```
# これはコメントです
node_modules/

# 以下はビルド成果物
dist/
build/
```

---

### パターンの例

```
# 特定のファイル
README.md

# 特定のディレクトリ
node_modules/

# パターンマッチ
*.log
*.tmp

# 再帰的マッチ
**/__pycache__/
**/node_modules/

# 否定パターン
*.log
!important.log

# 複数の拡張子
*.{jpg,png,gif}
```

---

## よく使うパターン集

### Node.js / JavaScript プロジェクト

```
# 依存関係
node_modules/
jspm_packages/

# ビルド成果物
dist/
build/
out/
.next/
.nuxt/

# キャッシュ
.cache/
.parcel-cache/
.npm/
.yarn/

# ログ
*.log
logs/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# 環境変数
.env
.env.local
.env.*.local

# OS
.DS_Store
Thumbs.db

# エディタ
.vscode/
.idea/
*.swp
*.swo
*~
```

---

### Python プロジェクト

```
# 仮想環境
venv/
env/
ENV/
.venv/

# Pythonキャッシュ
__pycache__/
*.py[cod]
*$py.class
*.so

# 配布/パッケージング
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# テストカバレッジ
htmlcov/
.tox/
.coverage
.coverage.*
.cache
nosetests.xml
coverage.xml
*.cover

# 環境変数
.env
.venv

# Jupyter Notebook
.ipynb_checkpoints

# pyenv
.python-version
```

---

### Ruby プロジェクト

```
# 依存関係
vendor/bundle/

# ログ
*.log

# 一時ファイル
tmp/

# データベース
*.sqlite3

# 環境変数
.env
.env.local

# Ruby バージョン管理
.ruby-version
.ruby-gemset

# Railsキャッシュ
public/assets/
public/packs/
public/packs-test/

# Coverage
coverage/
```

---

### Java / Kotlin プロジェクト

```
# ビルド成果物
target/
build/
out/
*.class

# Gradle
.gradle/
gradle-app.setting
!gradle-wrapper.jar

# Maven
.mvn/

# IntelliJ IDEA
.idea/
*.iml
*.iws
*.ipr

# Eclipse
.classpath
.project
.settings/

# ログ
*.log
```

---

### Go プロジェクト

```
# バイナリ
*.exe
*.exe~
*.dll
*.so
*.dylib

# テストバイナリ
*.test

# ビルド成果物
vendor/
bin/

# Go workspace
go.work
```

---

### 汎用パターン（全プロジェクト共通）

```
# OS生成ファイル
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# エディタ
.vscode/
.idea/
*.swp
*.swo
*~
.project
.classpath

# ログ
*.log
logs/

# 環境変数
.env
.env.local
.env.*.local

# 一時ファイル
*.tmp
*.temp
*.bak
*.backup

# 大きなファイル
*.zip
*.tar.gz
*.rar
*.7z

# データベースダンプ
*.sql
*.db
*.sqlite
*.sqlite3

# キャッシュ
.cache/
cache/
tmp/
temp/
```

---

## 実践的な設定例

### 例1: フルスタックWebアプリ（React + Node.js）

**`.claudeignore`:**

```
# === フロントエンド ===
# 依存関係
node_modules/
jspm_packages/

# ビルド成果物
build/
dist/
out/
.next/
public/build/

# キャッシュ
.cache/
.parcel-cache/

# === バックエンド ===
# ログ
*.log
logs/

# 環境変数
.env
.env.local
.env.production

# === データベース ===
# データベースファイル
*.sqlite
*.sqlite3
*.db

# データベースダンプ
*.sql

# === その他 ===
# OS
.DS_Store
Thumbs.db

# エディタ
.vscode/
.idea/

# テストカバレッジ
coverage/
.nyc_output/

# 画像・動画（大きなファイル）
*.png
*.jpg
*.jpeg
*.gif
*.mp4
*.mov

# ただし、小さなアイコンは除外しない
!public/icons/*.png
!public/favicon.ico
```

---

### 例2: データサイエンスプロジェクト（Python）

**`.claudeignore`:**

```
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python

# 仮想環境
venv/
env/
ENV/
.venv/

# Jupyter Notebook
.ipynb_checkpoints
*.ipynb  # ノートブックは大きくなりがち

# データファイル（大きなファイル）
*.csv
*.xlsx
*.json
*.parquet
*.h5
*.hdf5

# ただし、小さなサンプルデータは除外しない
!data/samples/*.csv

# モデルファイル
*.pkl
*.pickle
*.joblib
*.h5
*.pb

# データベース
*.sqlite
*.db

# ログ
*.log
logs/

# 画像
*.png
*.jpg
*.jpeg

# テンソルフローログ
tensorboard/

# MLflow
mlruns/

# 環境変数
.env
```

---

### 例3: モバイルアプリ（React Native）

**`.claudeignore`:**

```
# Node modules
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# OS
.DS_Store
Thumbs.db

# Xcode
ios/Pods/
ios/build/
ios/DerivedData/
*.pbxuser
!default.pbxuser
*.mode1v3
!default.mode1v3
*.mode2v3
!default.mode2v3
*.perspectivev3
!default.perspectivev3
xcuserdata/
*.xccheckout
*.moved-aside
*.xcuserstate

# Android
android/app/build/
android/build/
android/.gradle/
android/captures/
android/local.properties
*.apk
*.aab

# Metro
.metro-health-check*

# ビルド成果物
*.jsbundle

# 環境変数
.env
.env.local

# アセット（大きなファイル）
assets/videos/
*.mp4
*.mov
```

---

## ベストプラクティス

### 1. 早めに設定する

プロジェクト開始時に `.claudeignore` を作成しておくと、無駄なトークン消費を防げます。

---

### 2. .gitignoreをベースにする

`.gitignore` の内容を `.claudeignore` にコピーして、そこから調整するのが効率的です。

```bash
# .gitignoreをコピー
cp .gitignore .claudeignore

# 必要に応じて編集
vim .claudeignore
```

---

### 3. 大きなファイルは必ず除外

画像、動画、データベースダンプなど、大きなファイルは除外します。

```
# 画像
*.png
*.jpg
*.gif

# 動画
*.mp4
*.mov

# データベースダンプ
*.sql
*.dump
```

---

### 4. 機密情報を除外

環境変数ファイルや秘密鍵は必ず除外します。

```
# 環境変数
.env
.env.local
.env.production

# 秘密鍵
*.pem
*.key
id_rsa
id_rsa.pub
```

---

### 5. コメントで整理

除外パターンをカテゴリごとに整理すると、管理しやすくなります。

```
# === 依存関係 ===
node_modules/
vendor/

# === ビルド成果物 ===
dist/
build/

# === 環境変数 ===
.env
.env.local

# === ログ ===
*.log
logs/
```

---

### 6. チームで共有

`.claudeignore` をGitにコミットして、チーム全体で共有します。

```bash
git add .claudeignore
git commit -m "Add .claudeignore"
git push
```

---

### 7. 定期的に見直す

プロジェクトの成長に合わせて、`.claudeignore` を定期的に見直します。

---

## よくある質問

### Q: .gitignoreと.claudeignoreの違いは?

**A:**

| | .gitignore | .claudeignore |
|---|---|---|
| **用途** | Gitで管理しないファイル | Claude Codeが読み込まないファイル |
| **対象** | バージョン管理 | AI補助ツール |
| **優先度** | 高い（セキュリティ） | 中（パフォーマンス） |

**どちらも設定すべき:**
- `.gitignore`: Gitにコミットしないファイル
- `.claudeignore`: Claude Codeが読み込まないファイル

多くの場合、内容は似通っています。

---

### Q: .claudeignoreが効かない

**A:** 以下を確認してください:

1. **ファイル名**
   - 正しいファイル名: `.claudeignore`
   - 間違い: `claudeignore`（ドットが必要）

2. **ファイルの場所**
   - プロジェクトのルートディレクトリに配置

3. **パターンの記述**
   - ディレクトリには末尾に `/` をつける
   - パスの区切りは `/` を使う（Windowsでも）

4. **Claude Codeの再起動**
   ```bash
   exit
   claude
   ```

---

### Q: 特定のファイルだけ除外を解除したい

**A:** `!` で除外を解除できます。

```
# 全てのlogファイルを除外
*.log

# ただしimportant.logは除外しない
!important.log
```

**注意:** 親ディレクトリが除外されている場合、ファイル単体の除外解除は効きません。

```
# これは効かない
logs/
!logs/important.log

# こうする
# logs/内の全ファイルを除外
logs/*

# ただしimportant.logは除外しない
!logs/important.log
```

---

### Q: .claudeignoreをプロジェクトごとに変える必要がある?

**A:** はい、プロジェクトの種類によって異なります。

**推奨アプローチ:**
1. プロジェクトの種類（Node.js、Python、Rubyなど）に応じたテンプレートを用意
2. プロジェクト固有の除外パターンを追加

---

### Q: グローバルな.claudeignoreを設定できる?

**A:** はい、ユーザー全体に適用される設定が可能です。

```bash
# グローバル設定ファイルを作成
mkdir -p ~/.config/claude
touch ~/.config/claude/.claudeignore
```

**グローバル.claudeignore:**
```
# OS生成ファイル
.DS_Store
Thumbs.db

# エディタ
.vscode/
.idea/
*.swp
*~

# 一般的な一時ファイル
*.log
*.tmp
```

**優先順位:**
1. プロジェクトの `.claudeignore`
2. グローバルの `~/.config/claude/.claudeignore`

---

### Q: .claudeignoreの効果を確認するには?

**A:** Claude Codeに直接確認できます。

```bash
claude
```

対話モードで:

```
プロジェクト内のファイルをリストアップして
```

除外したファイルが表示されないことを確認します。

または:

```
node_modules/ディレクトリの内容を教えて
```

除外されている場合、Claudeは「アクセスできない」と答えます。

---

### Q: パフォーマンスにどのくらい影響する?

**A:** 大きなプロジェクトほど効果が大きいです。

**例: 大規模なNode.jsプロジェクト**
- `node_modules/` を含む場合: 数十万ファイル
- `node_modules/` を除外: 数千ファイル

**効果:**
- ✅ 起動時間の短縮
- ✅ トークン消費量の削減
- ✅ レスポンスの高速化

---

## .claudeignoreのテンプレート

### テンプレート: フルスタックWebアプリ

```
# === 依存関係 ===
node_modules/
vendor/
jspm_packages/

# === ビルド成果物 ===
dist/
build/
out/
.next/
.nuxt/

# === キャッシュ ===
.cache/
.parcel-cache/
.npm/
.yarn/

# === ログ ===
*.log
logs/

# === 環境変数 ===
.env
.env.local
.env.*.local

# === データベース ===
*.sqlite
*.sqlite3
*.db

# === OS ===
.DS_Store
Thumbs.db

# === エディタ ===
.vscode/
.idea/
*.swp
*~

# === テスト ===
coverage/
.nyc_output/

# === アセット（大きなファイル）===
*.png
*.jpg
*.gif
*.mp4
!public/icons/*.png
```

---

## まとめ

`.claudeignore` は、以下の効果があります:

✅ **パフォーマンス向上**: 読み込みの高速化
✅ **コスト削減**: トークン消費量の削減
✅ **セキュリティ向上**: 機密情報の保護
✅ **作業効率化**: 不要なファイルを除外

**効果的な使い方:**
- プロジェクト開始時に設定
- .gitignoreをベースにする
- 大きなファイルや機密情報を除外
- チームで共有
- 定期的に見直す

---

## 次のステップ

.claudeignoreを理解したら、他の機能も学びましょう:

- [サブエージェントガイド](./07-subagents-guide.md): 専門エージェントの活用
- [HOOKSガイド](./08-hooks-guide.md): イベント駆動の自動化
- [Slash Commandsガイド](./09-slash-commands-guide.md): カスタムコマンドの作成
- [MCPガイド](./10-mcp-guide.md): 外部ツールとの統合

---

**参考:**
- [Claude Code 公式ドキュメント](https://code.claude.com/docs)
- [.gitignore のパターン](https://git-scm.com/docs/gitignore)
