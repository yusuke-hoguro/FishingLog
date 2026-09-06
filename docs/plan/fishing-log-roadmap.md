# Fishing Log Development Roadmap

## 1. このRoadmapの目的

このドキュメントは、Fishing Logを学習用ポートフォリオとして開発し、最終的にAWS上で稼働させて実際に利用できる状態まで進めるための全体Roadmapです。

各Stepの詳細な学習・設計・実装は、このRoadmapへ直接書き込まず、`docs/study/learning-workflow.md` に従ってテーマごとの学習Plan・進捗ファイル・成果物へ分割して進めます。

このRoadmapは「何を、どの順番で完成させるか」を示す親計画であり、各Roadmap Stepの現在Statusと、そのStepに紐づくLearning Planを記録するプロジェクト全体の進捗台帳でもあります。

現在実際に扱っているRoadmap Step、Learning Plan、Plan内Stepは `docs/study/learning-state.md` を入口として確認します。このRoadmapはStepの詳細と状態の正本であり、状態ファイルは現在地を指すポインタです。

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

各Roadmap Stepには永続的な `Roadmap Step ID` を持たせます。Statusは `未着手 / 進行中 / 完了 / 保留` のいずれかです。

Roadmapに紐づくLearning Plan・Progressは親Roadmap Step IDを記録し、Plan確定時にRoadmap側へ関連付けます。独立学習は親Roadmap Step ID・親Roadmap Stepをともに `なし` とし、Roadmapへ登録しません。

関連Learning Planは `学習ID — Planパス` の形式で記録します。作成中・レビュー待ちのPlanは登録せず、確定時にだけ追加します。Planの状態は各Planを正とし、Roadmapへ重複記録しません。

保留Stepを指す状態ファイルも正常な現在地です。保留・再開は[共通の保留手順](../study/learning-workflow.md#roadmap-stepの保留と再開)に従い、再開依頼までは学習・Plan確定・完了処理へ進みません。

通常、`進行中` にするRoadmap Stepは1つだけです。独立学習へ切り替える場合もRoadmapのStatusは変更せず、`learning-state.md` の運用範囲を切り替えます。

原則として次の流れを守ります。

```text
Roadmapの現在地を確認
        ↓
対象Roadmap Stepを決定
        ↓
必要なLearning Planを提案
        ↓
Learning Planに親Roadmap Step IDを記録
        ↓
Planをレビューして確定
        ↓
Progress初期化・状態ファイル更新（Roadmapに紐づくPlanのみStatus一覧へ関連付け）を同時に行う
        ↓
Plan内Stepを1つずつ進める
        ↓
Learning Plan完了
        ↓
Roadmap Step自身の完了条件を確認
        ↓
ユーザー確認後にRoadmap Stepを完了
```

関連Learning Planがすべて完了しても、Roadmap Stepは自動完了しません。必ずそのRoadmap Step自身の完了条件を確認します。

---

## 5. Roadmap Status一覧

各Stepの `Status` と `関連Learning Plan` は、この一覧表を唯一の正本・更新先とします。Plan確定・置換・分割・削除、Step開始・完了時は該当行を更新し、詳細節へ状態を複製しません。完了根拠とユーザー承認は下の完了記録へ残します。

| Roadmap Step ID | Step | Status | 関連Learning Plan |
| --- | --- | --- | --- |
| `roadmap-step-01` | Step 1: PHP基礎 | `未着手` | なし |
| `roadmap-step-02` | Step 2: Laravel基礎 | `未着手` | なし |
| `roadmap-step-03` | Step 3: Fishing Log v1 要件定義 | `未着手` | なし |
| `roadmap-step-04` | Step 4: API・データベース設計 | `未着手` | なし |
| `roadmap-step-05` | Step 5: 認証・認可 | `未着手` | なし |
| `roadmap-step-06` | Step 6: Catch CRUD API実装 | `未着手` | なし |
| `roadmap-step-07` | Step 7: 自動テスト | `未着手` | なし |
| `roadmap-step-08` | Step 8: Docker開発環境 | `未着手` | なし |
| `roadmap-step-09` | Step 9: CI | `未着手` | なし |
| `roadmap-step-10` | Step 10: AWSアーキテクチャ設計 | `未着手` | なし |
| `roadmap-step-11` | Step 11: AWSデプロイ | `未着手` | なし |
| `roadmap-step-12` | Step 12: CD | `未着手` | なし |
| `roadmap-step-13` | Step 13: 本番運用の最低限 | `未着手` | なし |
| `roadmap-step-14` | Step 14: 実利用・改善 | `未着手` | なし |

> Learning Planを確定したら、対象Stepの `関連Learning Plan` に学習IDとファイルパスを追加し、Statusを必要に応じて `進行中` へ更新します。

### Roadmap Step完了記録

Roadmap Stepを完了したときは、次の表へ根拠とユーザー承認を記録します。関連Planがすべて完了していても、この記録なしにStatusを `完了` へ変更しません。

| Roadmap Step ID | 完了日 | 関連Plan確認 | Roadmap完了条件の根拠 | ユーザー承認 |
| --- | --- | --- | --- | --- |
| なし | - | - | - | - |

---

## 6. Roadmap詳細

## Step 1: PHP基礎

- Roadmap Step ID: `roadmap-step-01`

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

### 完了条件

- PHPの基本構文を読める。
- 小さな処理を自力で書ける。
- Laravelのコードを読み始められる状態になる。

---

## Step 2: Laravel基礎

- Roadmap Step ID: `roadmap-step-02`

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

### 完了条件

- HTTPリクエストがLaravel内部でどのように処理されるか説明できる。
- 小規模なCRUD APIを作成できる。

---

## Step 3: Fishing Log v1 要件定義

- Roadmap Step ID: `roadmap-step-03`

### 目的

実装を始める前に、Fishing Log v1で作るものと作らないものを明確にします。

### 主な対象

- 利用目的
- 想定ユーザー
- v1の機能一覧
- 対象外機能
- 利用シナリオ
- 非機能要件の最低限の整理

### 完了条件

- v1の範囲を説明できる。
- 「なぜその機能をv1へ入れるのか」を説明できる。
- 実装前に要件がドキュメントとして確定している。

---

## Step 4: API・データベース設計

- Roadmap Step ID: `roadmap-step-04`

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

### 完了条件

- ER構造を説明できる。
- API仕様を説明できる。
- Laravel実装へ進めるだけの設計が成果物として残っている。

---

## Step 5: 認証・認可

- Roadmap Step ID: `roadmap-step-05`

### 目的

ユーザーごとに安全に釣果を管理するための認証・認可を実装します。

### 主な対象

- ユーザー登録
- ログイン
- Laravelで利用する認証方式の選定
- 認証済みユーザーの取得
- 他ユーザーの釣果を操作できない認可
- 認証・認可のテスト

### 完了条件

- 認証と認可の違いを説明できる。
- ログインユーザー自身のデータのみ操作できる。

---

## Step 6: Catch CRUD API実装

- Roadmap Step ID: `roadmap-step-06`

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

### 完了条件

- v1の主要APIが一通り利用できる。
- 正常系と代表的な異常系を説明・確認できる。

---

## Step 7: 自動テスト

- Roadmap Step ID: `roadmap-step-07`

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

### 完了条件

- 主要なAPIが自動テストされている。
- 何をUnit Testで確認し、何をFeature Testで確認するか説明できる。

---

## Step 8: Docker開発環境

- Roadmap Step ID: `roadmap-step-08`

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

### 完了条件

- 新しい環境でも手順に沿って開発環境を起動できる。
- コンテナ間の通信を説明できる。

---

## Step 9: CI

- Roadmap Step ID: `roadmap-step-09`

### 目的

GitHub上で継続的に品質を確認できる仕組みを作ります。

### 主な対象

- GitHub Actions
- Test
- Lint / Formatter
- Static Analysis
- CI実行タイミング
- 失敗時の確認方法

### 完了条件

- Pull RequestまたはPushでCIが実行される。
- CIが何を保証しているか説明できる。

---

## Step 10: AWSアーキテクチャ設計

- Roadmap Step ID: `roadmap-step-10`

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

### 完了条件

- 採用するAWS構成を図と文章で説明できる。
- 各AWSサービスの責務を説明できる。
- 代替案との比較理由が成果物として残っている。

---

## Step 11: AWSデプロイ

- Roadmap Step ID: `roadmap-step-11`

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

### 完了条件

- Fishing Log APIへインターネットからアクセスできる。
- RDSへデータを保存できる。
- CloudWatch等でアプリケーションログを確認できる。

---

## Step 12: CD

- Roadmap Step ID: `roadmap-step-12`

### 目的

アプリケーション変更を安全にAWSへ反映できるデプロイフローを構築します。

### 主な対象

- GitHub Actions
- Docker Image Build
- ECR Push
- ECS Deploy
- デプロイ失敗時の確認方法
- 必要に応じてRollbackの検討

### 完了条件

- 定めたGit操作を起点にAWSへデプロイできる。
- CIとCDの役割の違いを説明できる。

---

## Step 13: 本番運用の最低限

- Roadmap Step ID: `roadmap-step-13`

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

### 完了条件

- 問題発生時に何を確認するか説明できる。
- 維持コストを把握できる。
- 自分で継続運用できる最低限の手順が残っている。

---

## Step 14: 実利用・改善

- Roadmap Step ID: `roadmap-step-14`

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

### 完了条件

このStepは継続改善フェーズです。v1完成後にStatusを `進行中` とし、改善テーマごとに必要なLearning Planを追加します。プロジェクト終了など明示的な区切りを設ける場合のみ `完了` とします。

---

## 7. RoadmapとLearning Planの関係

Roadmapの1 Stepが、そのまま1つのLearning Planになるとは限りません。

テーマが大きい場合は複数のLearning Planへ分割します。

例:

```text
Roadmap Step 10: AWSアーキテクチャ設計
Roadmap Step ID: roadmap-step-10

  ├── aws-network-plan.md
  ├── aws-ecs-plan.md
  ├── aws-rds-plan.md
  └── aws-observability-plan.md
```

これらすべてのPlanには `親Roadmap Step ID: roadmap-step-10` を記録し、Roadmap側にも4つのPlanを列挙します。

逆に、既に十分理解している内容は小さなPlanへまとめたり、診断結果に応じて短縮したりできます。

Roadmap外の寄り道学習は独立Learning Planとして作成でき、その場合は親Roadmap Stepを `なし` とします。

---

## 8. セッションをまたいで再開するとき

新しいセッションでは、`AGENTS.md` と[学習ワークフローの再開手順](../study/learning-workflow.md#8-別セッションで再開する)を正として現在地を復元します。以下はその参照順序です。

1. `docs/study/learning-state.md` の運用範囲、対象ID、フェーズ、確認待ち、次の1アクションを読む。
2. 現在の運用範囲が `roadmap` の場合だけ、このRoadmapのStatus一覧と状態ファイルが指定する現在Roadmap Stepを照合する。`independent` では現在Roadmap Step IDが `なし` であることを確認し、Status一覧で現在Stepを検索しない。独立Planの登録・親Step照合も対象外とする。保存済み復帰先は学習ワークフロー第12節に従って別に検証し、再開だけで復帰・消去はしない。
3. 状態ファイルが指定するPlanがあれば、学習ID・親Roadmap Step ID・状態を確認する。
4. 確定済みPlanでは対応するProgressを読み、学習ID・親Roadmap Step ID・現在Stepを照合する。
5. 対象に関連するコード・設計成果物を確認し、状態ファイルと、存在する場合はProgressの次の1アクションから再開する。

Planなしの `準備` ではPlan / Progress照合は対象外です。作成中・レビュー待ちPlanはRoadmap未登録・Progress未作成が正常で、状態ファイルが指すPlanの作成・レビューから再開します。確定前に学習へ進んではいけません。確定済みPlanのProgress欠損など、存在するべき記録の欠損・不一致は学習ワークフローの復旧手順で扱います。状態ファイルがある場合にRoadmapだけから別の再開対象を選び直しません。

---

## 9. Roadmap変更ルール

Roadmapは固定ではありません。

開発や学習の途中で、前提不足、新しい課題、AWS構成変更などが判明した場合は変更できます。

ただし、Roadmapを変更するときは次を明確にします。

- なぜ変更するのか
- どのStepへ影響するのか
- 完了済みStepの再確認が必要か
- Learning PlanやProgressへどのように反映するか
- 関連Learning Planのマッピングをどう変更するか

既に完了した学習記録は削除せず、変更履歴が追える状態を維持します。

---

## Roadmap保留・再開履歴

Roadmap Stepの保留前Statusの保存先はこの表です。現在のStatusと関連Planの正本は引き続きStatus一覧であり、この表は過去の操作を追記する履歴です。未実施の操作や記入例は実績行として追加しません。

| 履歴ID | 日付 | Roadmap Step ID | 操作 | 変更前Status | 変更後Status | 対応する保留履歴ID | 理由・ユーザー依頼の内容 |
| --- | --- | --- | --- | --- | --- | --- | --- |

- 履歴IDは `roadmap-history-001` から通し番号で採番し、再利用しません。日付は `YYYY-MM-DD` で記録します。
- 保留時は操作を `保留`、変更前Statusを `未着手 / 進行中` の実際の値、変更後Statusを `保留`、対応する保留履歴IDを `なし` として追記します。Status一覧と状態ファイルの状態遷移履歴も同じ作業で同期します。
- 再開時は同じStep IDのうち、再開行からまだ参照されていない保留行を1件だけ選び、その変更前Statusを復元先とします。0件・複数件・Status一覧との矛盾があれば復旧手順へ進みます。
- 再開行は操作を `再開`、変更前Statusを `保留`、変更後Statusを選んだ保留行の変更前Status、対応する保留履歴IDをその履歴IDとして追記します。元の保留行は消さず、Status一覧と状態遷移履歴も同期します。状態遷移履歴の操作欄にはこの表の履歴IDを記録します。
- 通常の起動・再開、独立学習からの復帰、保留・再開操作の前後には、現在の対象やStatusに限定せず、全StepのStatus一覧とこの表を照合します。各Stepが `保留` なら未再開の保留行はちょうど1件、それ以外なら0件であることが必要です。履歴なし・保留Stepなしの初期状態は正常です。
- 履歴IDの重複、存在しないStep ID、再開行の参照先欠損・別Stepへの参照・同じ保留行への重複参照・復元Statusの不一致も復旧対象です。Statusだけ復元され未再開行が残る場合や、再開行だけ追加されStatusが保留のままの場合は、その起動時に止めます。次の保留まで検証を延期しません。
- この照合はRoadmap内の履歴整合性確認であり、独立Planの親Step照合ではありません。`independent` 中も行いますが、現在Roadmap Step IDの `なし` を検索したり、復帰先を現在の親として扱ったりしません。
- 再保留は新しい履歴IDで記録します。途中で同期が中断した場合や過去の記録を訂正する場合は、共通の復旧手順に従い、根拠と訂正理由を残します。完了記録を保留履歴の代わりにしません。

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
