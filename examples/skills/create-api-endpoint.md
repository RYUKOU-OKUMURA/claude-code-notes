# Create API Endpoint Skill

新しいAPIエンドポイントを作成します。RESTful API設計に従い、必要なファイル（ルート、コントローラー、サービス、テスト）を一括で生成します。

---

## 実行手順

1. **エンドポイント情報をユーザーに確認**
   - エンドポイントのパス（例: `/api/v1/users`）
   - HTTPメソッド（GET, POST, PUT, DELETE）
   - リクエスト/レスポンスの形式

2. **ルートファイルを作成**
   - `src/routes/[resource].ts`を作成
   - ルーティングを定義

3. **コントローラーを作成**
   - `src/controllers/[resource]Controller.ts`を作成
   - リクエストハンドリングロジックを実装

4. **サービスを作成**
   - `src/services/[resource]Service.ts`を作成
   - ビジネスロジックとデータベースアクセスを実装

5. **バリデーションスキーマを定義**
   - `src/schemas/[resource]Schema.ts`を作成
   - リクエストボディのバリデーションルールを定義

6. **エラーハンドリングを実装**
   - 適切なHTTPステータスコードを返す
   - エラーレスポンスの形式を統一

7. **テストを作成**
   - ユニットテスト: `src/controllers/[resource]Controller.test.ts`
   - 統合テスト: `src/routes/[resource].test.ts`

8. **APIドキュメントを更新**
   - Swagger/OpenAPI定義を更新

9. **メインのルーターに追加**
   - `src/routes/index.ts`に新しいルートを追加

---

## エンドポイントのテンプレート

### ルート（routes/users.ts）

```typescript
import { Router } from 'express';
import { userController } from '../controllers/userController';
import { validate } from '../middlewares/validate';
import { authenticateToken } from '../middlewares/auth';
import { userSchema } from '../schemas/userSchema';

const router = Router();

// GET /api/v1/users - ユーザー一覧取得
router.get('/', authenticateToken, userController.getAll);

// GET /api/v1/users/:id - ユーザー詳細取得
router.get('/:id', authenticateToken, userController.getById);

// POST /api/v1/users - ユーザー作成
router.post('/', validate(userSchema.create), userController.create);

// PUT /api/v1/users/:id - ユーザー更新
router.put('/:id', authenticateToken, validate(userSchema.update), userController.update);

// DELETE /api/v1/users/:id - ユーザー削除
router.delete('/:id', authenticateToken, userController.delete);

export default router;
```

### コントローラー（controllers/userController.ts）

```typescript
import { Request, Response, NextFunction } from 'express';
import { userService } from '../services/userService';
import { handleApiError } from '../utils/errorHandler';

export const userController = {
  // ユーザー一覧取得
  async getAll(req: Request, res: Response, next: NextFunction) {
    try {
      const users = await userService.getAllUsers();
      res.json({ data: users });
    } catch (error) {
      next(handleApiError(error));
    }
  },

  // ユーザー詳細取得
  async getById(req: Request, res: Response, next: NextFunction) {
    try {
      const { id } = req.params;
      const user = await userService.getUserById(id);

      if (!user) {
        return res.status(404).json({
          error: {
            code: 'USER_NOT_FOUND',
            message: 'ユーザーが見つかりません'
          }
        });
      }

      res.json({ data: user });
    } catch (error) {
      next(handleApiError(error));
    }
  },

  // ユーザー作成
  async create(req: Request, res: Response, next: NextFunction) {
    try {
      const userData = req.body;
      const newUser = await userService.createUser(userData);
      res.status(201).json({ data: newUser });
    } catch (error) {
      next(handleApiError(error));
    }
  },

  // ユーザー更新
  async update(req: Request, res: Response, next: NextFunction) {
    try {
      const { id } = req.params;
      const userData = req.body;
      const updatedUser = await userService.updateUser(id, userData);

      if (!updatedUser) {
        return res.status(404).json({
          error: {
            code: 'USER_NOT_FOUND',
            message: 'ユーザーが見つかりません'
          }
        });
      }

      res.json({ data: updatedUser });
    } catch (error) {
      next(handleApiError(error));
    }
  },

  // ユーザー削除
  async delete(req: Request, res: Response, next: NextFunction) {
    try {
      const { id } = req.params;
      await userService.deleteUser(id);
      res.status(204).send();
    } catch (error) {
      next(handleApiError(error));
    }
  }
};
```

### サービス（services/userService.ts）

```typescript
import { prisma } from '../lib/db';
import { User, CreateUserInput, UpdateUserInput } from '../types/user';

export const userService = {
  async getAllUsers(): Promise<User[]> {
    return await prisma.user.findMany();
  },

  async getUserById(id: string): Promise<User | null> {
    return await prisma.user.findUnique({ where: { id } });
  },

  async createUser(data: CreateUserInput): Promise<User> {
    return await prisma.user.create({ data });
  },

  async updateUser(id: string, data: UpdateUserInput): Promise<User | null> {
    return await prisma.user.update({ where: { id }, data });
  },

  async deleteUser(id: string): Promise<void> {
    await prisma.user.delete({ where: { id } });
  }
};
```

### バリデーションスキーマ（schemas/userSchema.ts）

```typescript
import { z } from 'zod';

export const userSchema = {
  create: z.object({
    body: z.object({
      name: z.string().min(1, 'Name is required'),
      email: z.string().email('Invalid email'),
      password: z.string().min(8, 'Password must be at least 8 characters')
    })
  }),

  update: z.object({
    body: z.object({
      name: z.string().min(1).optional(),
      email: z.string().email().optional(),
      password: z.string().min(8).optional()
    })
  })
};
```

---

## 注意事項

- **認証ミドルウェア**: 適切なエンドポイントに認証ミドルウェアを適用
- **バリデーション**: すべてのリクエストボディをバリデーション
- **エラーハンドリング**: 一貫したエラーレスポンス形式を使用
- **HTTPステータスコード**: 適切なステータスコードを返す
  - 200: 成功（GET, PUT）
  - 201: 作成成功（POST）
  - 204: 削除成功（DELETE）
  - 400: バリデーションエラー
  - 401: 認証エラー
  - 403: 権限エラー
  - 404: リソースが見つからない
  - 500: サーバーエラー

---

## 使用例

このスキルを実行するには:

```
/skill create-api-endpoint
```

または:

```
新しいAPIエンドポイントを作成して。
パス: /api/v1/tasks
メソッド: GET, POST, PUT, DELETE
```
