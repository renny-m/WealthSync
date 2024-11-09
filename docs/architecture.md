# システムアーキテクチャ設計書

## 1. システム構成

### インフラ：クラウドサービス

- **Firebase**

  - **ホスティング**: 静的 Web サイトのデプロイ
  - **Firestore**: データベース（ユーザー情報、資産情報、目標設定など）
  - **Firebase Authentication**: ユーザー認証
  - **Firebase Functions**: サーバーレスバックエンド

- **Supabase**（補完的に使用）
  - **PostgreSQL**: 補助的なデータベース

### バックエンド

- **Firebase Functions**: サーバーレスでバックエンドロジックを処理
- **Supabase API**: 必要に応じてデータベースと簡単に連携

### データベース

- **Firestore**: 資産情報、目標設定データなどの保存
- **Supabase（PostgreSQL）**: 複雑なデータやクエリの管理

### 認証

- **Firebase Authentication**: Google、Facebook、メール認証などを対応

### フロントエンド

- **Next.js**または**React**: アプリケーションの UI 構築
- **Firebase Hosting** または **Vercel**: 静的サイトのホスティング

### 監視/ログ

- **Sentry**: バグやエラー管理
- **Google Analytics**: ユーザー行動分析
