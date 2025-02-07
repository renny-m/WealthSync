# データベース設計書

## 1. 概要

本データベース設計書は、資産管理アプリケーションのデータベース設計を記述したものです。アプリケーションでは、ユーザーの目標設定、資産情報、ポートフォリオデータなどを管理するためのデータベースを設計します。

## 2. 使用するデータベース

- **Firestore**（NoSQL）
  - ユーザー情報、目標設定、ポートフォリオ情報など、非構造化データを管理
- **Supabase（PostgreSQL）**（補完的に使用）
  - 複雑なクエリやトランザクションが必要なデータを管理（補助的な用途）

## 3. テーブル設計

### 3.1. ユーザーテーブル (`users`)

| 項目         | 型          | 説明                                                     |
| ------------ | ----------- | -------------------------------------------------------- |
| `user_id`    | `STRING`    | ユーザーの一意識別子（Firebase Authentication に基づく） |
| `email`      | `STRING`    | ユーザーのメールアドレス                                 |
| `name`       | `STRING`    | ユーザーの名前                                           |
| `created_at` | `TIMESTAMP` | ユーザー登録日                                           |
| `updated_at` | `TIMESTAMP` | ユーザー情報の最終更新日                                 |

### 3.2. 目標設定テーブル (`goals`)

| 項目              | 型          | 説明                                                      |
| ----------------- | ----------- | --------------------------------------------------------- |
| `goal_id`         | `STRING`    | 目標設定の一意識別子                                      |
| `user_id`         | `STRING`    | 目標設定に紐づくユーザー ID（`users`テーブルの`user_id`） |
| `goal_amount`     | `FLOAT`     | 必要金額                                                  |
| `target_date`     | `TIMESTAMP` | 目標達成予定日                                            |
| `interest_rate`   | `FLOAT`     | 年利（目標金額を達成するための年利）                      |
| `monthly_savings` | `FLOAT`     | 毎月の積立額（自動算出）                                  |
| `created_at`      | `TIMESTAMP` | 目標設定作成日                                            |
| `updated_at`      | `TIMESTAMP` | 目標設定の最終更新日                                      |

### 3.3. ポートフォリオ設定テーブル (`portfolios`)

| 項目                | 型          | 説明                                                            |
| ------------------- | ----------- | --------------------------------------------------------------- |
| `portfolio_id`      | `STRING`    | ポートフォリオ設定の一意識別子                                  |
| `user_id`           | `STRING`    | ポートフォリオに紐づくユーザー ID（`users`テーブルの`user_id`） |
| `asset_type`        | `STRING`    | 資産の種類（例：株式、債券、不動産など）                        |
| `target_percentage` | `FLOAT`     | 各資産の目標割合（%）                                           |
| `target_amount`     | `FLOAT`     | 各資産の目標金額（自動算出）                                    |
| `actual_percentage` | `FLOAT`     | 実績割合（自動算出）                                            |
| `actual_amount`     | `FLOAT`     | 実績金額（手入力）                                              |
| `created_at`        | `TIMESTAMP` | ポートフォリオ作成日                                            |
| `updated_at`        | `TIMESTAMP` | ポートフォリオの最終更新日                                      |

### 3.4. 資産履歴テーブル (`asset_history`)

| 項目         | 型          | 説明                                                      |
| ------------ | ----------- | --------------------------------------------------------- |
| `history_id` | `STRING`    | 資産履歴の一意識別子                                      |
| `user_id`    | `STRING`    | 資産履歴に紐づくユーザー ID（`users`テーブルの`user_id`） |
| `asset_type` | `STRING`    | 資産の種類（例：株式、債券、不動産など）                  |
| `amount`     | `FLOAT`     | 実績金額（手入力）                                        |
| `date`       | `TIMESTAMP` | 実績の入力日                                              |
| `created_at` | `TIMESTAMP` | 履歴データ作成日                                          |
| `updated_at` | `TIMESTAMP` | 履歴データの最終更新日                                    |

### 3.5. 支出テーブル (`expenses`)

| 項目         | 型          | 説明                                                  |
| ------------ | ----------- | ----------------------------------------------------- |
| `expense_id` | `STRING`    | 支出の一意識別子                                      |
| `user_id`    | `STRING`    | 支出に紐づくユーザー ID（`users`テーブルの`user_id`） |
| `amount`     | `FLOAT`     | 支出金額                                              |
| `category`   | `STRING`    | 支出カテゴリ（例：食費、教育費、住居費など）          |
| `date`       | `TIMESTAMP` | 支出日                                                |
| `created_at` | `TIMESTAMP` | 支出データ作成日                                      |
| `updated_at` | `TIMESTAMP` | 支出データの最終更新日                                |

## 4. データの整合性と制約

- 各テーブルの`user_id`は`users`テーブルの`user_id`とリレーションを持ち、ユーザーごとにデータを管理します。
- `goal_amount`（目標金額）、`monthly_savings`（毎月の積立額）など、数値型データに関してはバリデーションを行い、不正な値（負の値や極端な値など）は入力できないようにします。
- `TIMESTAMP`フィールドは、データの作成日や更新日をトラッキングするために使用され、データの履歴管理に活用します。
