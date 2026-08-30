# Fishing Log Development Roadmap

## 1. このRoadmapの目的

このドキュメントは、Fishing Logを学習用ポートフォリオとして開発し、最終的にAWS上で稼働させて実際に利用できる状態まで進めるための全体Roadmapです。

各Stepの詳細な学習・設計・実装は、このRoadmapへ直接書き込まず、`docs/study/learning-workflow.md` に従ってテーマごとの学習Plan・進捗ファイル・成果物へ分割して進めます。

このRoadmapは「何を、どの順番で完成させるか」を示す親計画として扱います。

---

## 2. プロジェクトのゴール

### 学習面のゴール

- PHPの基本を理解し、自分の言葉で説明できる。
- Laravelの基本構造と主要機能を理解し、小規模なWeb APIを自力で実装できる。
- PostgreSQLを利用したデータ設計・永続化を行える。
- Dockerを利用して再現可能な開発環境を構築できる。
- 自動テストとCIを利用して品質を確認できる。
- AWSの主要サービスを組み合わせてLaravelアプリケーションを稼働させられる。
- アプリケーション開発から本番運用までの流れを一通り経験する。

### プロダクト面のゴール

- 自分の釣果を記録できるFishing Logを作る。
- 小さな機能構成でv1を完成させる。
- AWS上でインターネットから利用できる状態にする。
- 実際に利用し、必要性が生まれた機能のみ段階的に追加する。

---

## 3. v1の基本方針

v1ではLaravelやAWSの学習を優先し、機能を増やしすぎません。

想定する中心データは次のとおりです。

```text
User
  └── Catch
       ├── fish_name
       ├── caught_at
       ├── location
       ├── length_cm
       ├── quantity
       └── memo
```

v1で想定する中心機能は次のとおりです。

- ユーザー登録
- ログイン
- 釣果登録
- 釣果一覧
- 釣果詳細
- 釣果更新
- 釣果削除

写真、天気、潮汐、地図、統計、検索高度化などは原則としてv1の必須要件に含めません。

---

## 4. 開発・学習の基本ルール

各Stepは `docs/study/learning-workflow.md` に従って進めます。

原則として次の流れを守ります。

```text
RoadmapのStepを選ぶ
        ↓
学習テーマと成果物を決める
        ↓
理解度診断を行う
        ↓
テーマ専用の学習Planを作る
        ↓
Planをレビューして確定する
        ↓
進捗ファイルを初期化する
        ↓
現在のStepを1つだけ進める
        ↓
理解確認後に成果物へ反映する
        ↓
進捗を更新する
        ↓
次のStepへ進む
```

AIやAIエージェントは、学習者が考える前に完成回答や完成成果物を提示しないことを原則とします。

また、Planが確定していない状態で実装へ進んだり、学習者の確認なしに次のStepへ進んだりしません。

---

## 5. Roadmap

## Step 1: PHP基礎

### 目的

Laravelへ入る前に、PHPの基本構文とWebアプリケーション開発で頻出する言語仕様を理解します。

### 主な対象

- 変数と型
- 配列
- 条件分岐と繰り返し
- 関数
- クラスとオブジェクト
- 継承・インターフェース
- 例外
- 名前空間
- Composerの基本

### 完了の目安

- PHPの基本構文を読める。
- 小さな処理を自力で書ける。
- Laravelのコードを読み始められる状態になる。

---

## Step 2: Laravel基礎

### 目的

Laravelアプリケーションの基本構造と、API開発に必要な主要機能を理解します。

### 主な対象

- Laravelプロジェクト構成
- Routing
- Controller
- Request / Response
- Middleware
- Service Container / Dependency Injection
- Configuration / Environment Variables
- Artisan
- Migration
- Eloquent ORM
- Validation
- Exception Handling

### 完了の目安

- HTTPリクエストがLaravel内部でどのように処理されるか説明できる。
- 小規模なCRUD APIを作成できる。

---

## Step 3: Fishing Log v1 要件定義

### 目的

実装を始める前に、Fishing Log v1で作るものと作らないものを明確にします。

### 主な対象

- 利用目的
- 想定ユーザー
- v1の機能一覧
- 対象外機能
- 利用シナリオ
- 非機能要件の最低限の整理

### 完了の目安

- v1の範囲を説明できる。
- 「なぜその機能をv1へ入れるのか」を説明できる。
- 実装前に要件がドキュメントとして確定している。

---

## Step 4: API・データベース設計

### 目的

Fishing Log v1のAPIとデータ構造を実装前に設計します。

### 主な対象

- User / Catchの責務
- テーブル設計
- 主キー・外部キー
- 制約
- Indexの必要性
- APIエンドポイント
- HTTP Method
- Request / Response
- Status Code
- Error Response

### 完了の目安

- ER構造を説明できる。
- API仕様を説明できる。
- Laravel実装へ進めるだけの設計が成果物として残っている。

---

## Step 5: 認証・認可

### 目的

ユーザーごとに安全に釣果を管理するための認証・認可を実装します。

### 主な対象

- ユーザー登録
- ログイン
- Laravelで利用する認証方式の選定
- 認証済みユーザーの取得
- 他ユーザーの釣果を操作できない認可
- 認証・認可のテスト

### 完了の目安

- 認証と認可の違いを説明できる。
- ログインユーザー自身のデータのみ操作できる。

---

## Step 6: Catch CRUD API実装

### 目的

Fishing Log v1の中心機能である釣果CRUDをLaravelで実装します。

### 主な対象

- 釣果登録
- 釣果一覧
- 釣果詳細
- 釣果更新
- 釣果削除
- Eloquentによるデータ操作
- Validation
- Error Handling
- API Resource等によるResponse整形の検討

### 完了の目安

- v1の主要APIが一通り利用できる。
- 正常系と代表的な異常系を説明・確認できる。

---

## Step 7: 自動テスト

### 目的

主要機能を自動テストし、安全に変更できる状態を作ります。

### 主な対象

- Unit Testの役割
- Feature / Integration Testの役割
- 認証APIのテスト
- Catch CRUDのテスト
- Validationのテスト
- Authorizationのテスト
- テストデータ作成

### 完了の目安

- 主要なAPIが自動テストされている。
- 何をUnit Testで確認し、何をFeature Testで確認するか説明できる。

---

## Step 8: Docker開発環境

### 目的

LaravelとPostgreSQLを再現可能なローカル環境で起動できるようにします。

### 主な対象

- Dockerfile
- Docker Compose
- Laravelコンテナ
- PostgreSQLコンテナ
- 環境変数
- Volume
- Network
- 起動・停止方法

### 完了の目安

- 新しい環境でも手順に沿って開発環境を起動できる。
- コンテナ間の通信を説明できる。

---

## Step 9: CI

### 目的

GitHub上で継続的に品質を確認できる仕組みを作ります。

### 主な対象

- GitHub Actions
- Test
- Lint / Formatter
- Static Analysis
- CI実行タイミング
- 失敗時の確認方法

### 完了の目安

- Pull RequestまたはPushでCIが実行される。
- CIが何を保証しているか説明できる。

---

## Step 10: AWSアーキテクチャ設計

### 目的

Laravel Fishing LogをAWS上で安全に稼働させるための構成を設計します。

### 初期候補

```text
Internet
   ↓
ALB
   ↓
ECS / Fargate
   ↓
Laravel Container
   ↓
RDS PostgreSQL
```

必要に応じて次も検討します。

- ECR
- VPC
- Public / Private Subnet
- Security Group
- IAM
- CloudWatch
- Secrets / Parameter管理
- Route 53 / HTTPS

### 完了の目安

- 採用するAWS構成を図と文章で説明できる。
- 各AWSサービスの責務を説明できる。
- 代替案との比較理由が成果物として残っている。

---

## Step 11: AWSデプロイ

### 目的

設計したAWS環境へFishing Logをデプロイし、実際に稼働させます。

### 主な対象

- Docker Image Build
- ECR Push
- ECS / Fargate
- RDS PostgreSQL
- Migration
- Environment Variables / Secrets
- ALB
- CloudWatch Logs
- 疎通確認

### 完了の目安

- Fishing Log APIへインターネットからアクセスできる。
- RDSへデータを保存できる。
- CloudWatch等でアプリケーションログを確認できる。

---

## Step 12: CD

### 目的

アプリケーション変更を安全にAWSへ反映できるデプロイフローを構築します。

### 主な対象

- GitHub Actions
- Docker Image Build
- ECR Push
- ECS Deploy
- デプロイ失敗時の確認方法
- 必要に応じてRollbackの検討

### 完了の目安

- 定めたGit操作を起点にAWSへデプロイできる。
- CIとCDの役割の違いを説明できる。

---

## Step 13: 本番運用の最低限

### 目的

公開したFishing Logを継続利用するために最低限必要な運用を整えます。

### 主な対象

- ログ確認
- 障害時の確認手順
- DBバックアップ
- AWSコスト確認
- Secrets管理
- README / Runbook
- 利用方法

### 完了の目安

- 問題発生時に何を確認するか説明できる。
- 維持コストを把握できる。
- 自分で継続運用できる最低限の手順が残っている。

---

## Step 14: 実利用・改善

### 目的

実際にFishing Logを利用し、実利用から生まれた課題を基に改善します。

### 進め方

最初から追加機能を決め切らず、実際に必要になったものを候補として扱います。

将来候補の例:

- 釣果写真の保存（S3等）
- 天気情報
- 潮汐情報
- 地図・位置情報
- 魚種や場所による検索
- 月別・魚種別などの統計
- スマートフォン向けUI
- フロントエンドアプリケーション

### 完了の考え方

このStep以降は一度で完了させる対象ではなく、実利用に応じて継続的に改善します。

---

## 6. RoadmapとLearning Planの関係

Roadmapの1 Stepが、そのまま1つのLearning Planになるとは限りません。

テーマが大きい場合は複数のLearning Planへ分割します。

例:

```text
Roadmap Step 10: AWSアーキテクチャ設計

  ├── aws-network-plan.md
  ├── aws-ecs-plan.md
  ├── aws-rds-plan.md
  └── aws-observability-plan.md
```

逆に、既に十分理解している内容は小さなPlanへまとめたり、診断結果に応じて短縮したりできます。

Learning Planの粒度は、`docs/study/learning-workflow.md` に従い、理解度診断後に決定します。

---

## 7. ドキュメント配置方針

基本配置は次のとおりです。

```text
docs/
├── plan/
│   └── fishing-log-roadmap.md
├── study/
│   ├── learning-workflow.md
│   ├── templates/
│   ├── plans/
│   └── progress/
└── architecture/
```

役割は次のように分けます。

- `docs/plan/`: プロジェクト全体のRoadmap
- `docs/study/`: 学習方法、Learning Plan、進捗
- `docs/architecture/`: 学習後に確定した設計・判断・構成などの成果物

学習途中のメモや進捗を、確定した設計成果物へ混在させません。

---

## 8. セッションをまたいで再開するとき

新しいAIセッションまたはAIエージェントは、最低限次の順番で確認します。

1. `AGENTS.md`
2. `docs/plan/fishing-log-roadmap.md`
3. `docs/study/learning-workflow.md`
4. 現在対象となっている `docs/study/plans/*-plan.md`
5. 対応する `docs/study/progress/*-progress.md`
6. 対象Stepに関連するコード・設計成果物

進捗ファイルに記載された「現在のStep」「まだ曖昧なこと」「次回最初に行うこと」を再開地点の正とします。

AIは、前回の会話内容だけを根拠に現在地を推測せず、リポジトリ内のPlanとProgressを確認してから作業を開始します。

---

## 9. Roadmap変更ルール

Roadmapは固定ではありません。

開発や学習の途中で、前提不足、新しい課題、AWS構成変更などが判明した場合は変更できます。

ただし、Roadmapを変更するときは次を明確にします。

- なぜ変更するのか
- どのStepへ影響するのか
- 完了済みStepの再確認が必要か
- Learning PlanやProgressへどのように反映するか

既に完了した学習記録は削除せず、変更履歴が追える状態を維持します。

---

## 10. v1全体の完了条件

Fishing Log v1は、少なくとも次を満たした時点で完成とします。

- Laravelで認証付きの釣果CRUD APIが動作する。
- PostgreSQLへデータを保存できる。
- 主要機能に自動テストがある。
- Dockerでローカル開発環境を再現できる。
- GitHub ActionsでCIが実行される。
- AWS上でアプリケーションが稼働している。
- AWS上のPostgreSQLへデータを保存できる。
- アプリケーションログを確認できる。
- デプロイ手順またはCDが整備されている。
- READMEや必要な設計・運用ドキュメントが更新されている。
- 自分自身でFishing Logを利用開始できる。

v1完成後は、追加機能の多さではなく、実利用から得られた課題を基に継続改善します。
