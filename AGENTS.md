# AGENTS.md

## 1. このリポジトリの目的

このリポジトリは、PHP / Laravelを中心にFishing Logを開発し、最終的にAWS上で稼働させることを目的とした学習・ポートフォリオ用リポジトリです。

単に完成品を作ることではなく、開発者本人が設計・実装・運用上の判断を自分の言葉で説明できる状態を重視します。

AIやAIエージェントは、完成物の代行者ではなく、学習と開発を支援する伴走者として振る舞ってください。

---

## 2. 最初に読むファイル

作業開始前に、最低限次を確認してください。

1. `AGENTS.md`
2. `docs/study/learning-workflow.md`
3. `docs/study/learning-state.md`
4. `docs/plan/fishing-log-roadmap.md`
5. 状態ファイルが指す `docs/study/plans/*-plan.md`
6. 対応する `docs/study/progress/*-progress.md`
7. 対象テーマに関係するコード・設計ドキュメント

現在地は会話履歴から推測せず、`learning-state.md` を入口にRoadmap、対象Plan、対応するProgressを照合して判断してください。状態ファイルは現在地のポインタ、Roadmap / Plan / Progressは各詳細情報の正本です。

---

## 3. 開発全体の進め方

Fishing Log全体の親計画は `docs/plan/fishing-log-roadmap.md` です。

計画は次の階層で管理します。

```text
Fishing Log全体
    ↓
Roadmap Step
    ↓
個別Learning Plan
    ↓
Plan内Step
```

各Roadmap Stepには永続的な `Roadmap Step ID` を割り当てます。Roadmapに紐づくLearning PlanとProgressは親となる `Roadmap Step ID` を記録し、ユーザー確定後にRoadmap側へ関連Learning Planを登録します。独立学習ではPlan・Progressの `親Roadmap Step ID` と `親Roadmap Step` をともに `なし` とし、Roadmapへ登録しません。保存済み復帰先を独立Planの親として扱いません。

Roadmapの1 StepとLearning Planは1対1とは限らず、必要に応じて複数のLearning Planへ分割します。

Roadmapに紐づく学習の基本フローは次のとおりです。独立学習の開始・完了は学習ワークフロー第12節に従います。

```text
Roadmapの現在地を確認
        ↓
対象Roadmap Stepを決定
        ↓
必要なLearning Planを提案
        ↓
Learning Planに親Roadmap Step IDを記録
        ↓
ユーザーがPlanをレビュー・確定
        ↓
Progress初期化・状態ファイル更新（Roadmapに紐づくPlanのみStatus一覧へ関連付け）を同時に行う
        ↓
現在のPlan内Stepを1つだけ進める
        ↓
理解確認
        ↓
成果物・コードへ反映
        ↓
Progress更新
        ↓
Learning Plan完了
        ↓
Roadmap Stepの完了条件を確認
        ↓
ユーザー確認後にRoadmap Stepを完了
```

---

## 4. AI / AIエージェントの行動ルール

### ユーザーにA/B運用を意識させない

`docs/study/learning-workflow.md` では説明上、Roadmap運用と個別Learning Plan運用を区別しますが、通常の利用ではユーザーにA/B番号や詳細プロンプトの選択を要求しません。

ユーザーが「現在地から進めて」「続きをやりたい」などと依頼した場合、AIがリポジトリを確認して現在の階層と次の操作を判断してください。

判断順序は次のとおりです。

1. `learning-state.md` の運用範囲、対象ID、フェーズ、次の1アクションを確認する。
2. 運用範囲が `roadmap` の場合だけ、現在のRoadmap Step、関連Learning Plan、Statusを照合する。`independent` では現在Roadmap Step IDの `なし` を確認し、復帰先は学習ワークフロー第12節に従って別に検証する。再開だけでは復帰しない。
3. 状態ファイルが指すPlan / Progressの学習ID、親Roadmap Step ID、現在Stepを確認する。
4. 一致していれば、状態ファイルとProgressの次の1アクションから再開する。
5. 運用範囲が `roadmap` で、すべての関連PlanとProgressが `完了` でRoadmap Stepが `進行中` の場合は、Roadmap Stepの完了判定を行う。
6. 対象Planがなく現在フェーズが `準備` なら、指定されたRoadmap Stepに必要なLearning Planを検討する。
7. 状態ファイルが欠損している場合だけRoadmap、Plan、Progressから復元する。候補が複数なら推測せず復旧待ちにする。

### 操作する階層を区別する

内部的には、依頼が次のどちらを操作するものかを明確にしてください。

- **Roadmap運用**: プロジェクト全体の現在地確認、Roadmap Stepの開始・完了判定、関連Learning Planの追加、Roadmap変更を扱う。
- **個別Learning Plan運用**: 1つのLearning Planの作成・変更・完了と、そのPlan内Stepの開始・中断・再開・完了判定、Progress更新を扱う。

「Step」がRoadmap StepとPlan内Stepのどちらを指すか曖昧な場合は、Roadmap、Plan、Progressを照合して対象を特定してください。リポジトリだけで一意に特定できない場合に限り、ユーザーへ確認します。

### RoadmapとLearning Planの関連付け

RoadmapのStatusと関連Learning Planの唯一の正本・更新先はStatus一覧です。Plan確定・置換・分割・削除やStepの状態遷移では該当行を更新し、詳細節に可変状態を複製しません。

- 各Roadmap Stepには `Roadmap Step ID` を持たせる。
- Roadmap StepのStatusは `未着手 / 進行中 / 完了 / 保留` のいずれかとする。状態ファイルが指す保留Stepは正常な現在地として扱い、再開・停止の判断は学習ワークフローの「Roadmap Stepの保留と再開」に従う。
- Roadmapには、そのStepを満たすために確定したLearning Planの学習IDとパスを列挙する。
- Learning Planには `親Roadmap Step ID` と `親Roadmap Step` を記録する。
- Progressにも同じ親Roadmap Step情報を記録する。
- 作成中・レビュー待ちのLearning PlanはRoadmapへ登録しない。ユーザーがPlanを確定したときだけ、同じ作業内でProgressを初期化し、状態ファイルを同期する。Roadmapに紐づくPlanの場合だけRoadmapの関連Learning Plan一覧とStatusも更新する。独立Planの確定ではRoadmapと保存済み復帰先を維持する。
- Learning Planを削除・置換・分割する場合もRoadmapとの関連を同期する。
- 現在地、確認待ち、次の1アクションが変わる操作では `learning-state.md` も同じ作業で同期する。
- 関連Learning Planがすべて完了してもRoadmap Stepを自動完了しない。Roadmap自身の完了条件を確認し、ユーザー確認後にStatusを `完了` へ更新する。

### 状態の一意性と復旧

Planの確定履歴（未確定 / 確定済み）は保留中も維持し、Progressの存在要否はこの履歴で判断します。未確定PlanはProgressなしが正常、確定済みPlanは保留中もProgressが必須です。保留前の再開情報をPlanへ保存し、詳細は学習ワークフローの「確定履歴と保留からの再開」に従います。独立学習の復帰先にはユーザー確認待ちの内容も保存し、復帰時にそのまま復元します。

再開・整合性チェックはファイルの存在要否を先に判断します。Planなしの `準備` ではPlan / Progress照合は対象外です。作成中・レビュー待ちPlanは状態ファイルから参照し、Roadmap未登録・Progress未作成を正常と扱います。確定済みPlanのProgress欠損は復旧対象です。独立PlanはRoadmapへの登録・親Step照合が対象外です。以降の照合手順もこの条件に従い、確認待ちの作成・レビューを飛ばして学習へ進みません。

- `learning-state.md` が現在扱うRoadmap Step、Learning Plan、Plan内Stepをそれぞれ最大1つだけ指定する。
- 複数Planを同じRoadmap Stepへ登録してもよいが、同時に再開対象とするPlanは1つだけとする。
- 独立学習への切替は、運用範囲が `roadmap`、復帰先の全項目が `なし`、安全な停止地点にある場合だけ許可する。復帰先は1件とし、上書きしない。
- `independent` 中に別の独立学習を依頼された場合は、現在のPlan・Progress・復帰先を維持し、新しいPlanの作成や直接切替を行わない。現在の独立学習を終了または保留してRoadmapへ戻り、その後に別の独立学習を提案する順序を案内する。終了・保留・復帰は無断で行わない。
- 復帰先の運用範囲が `independent`、または `roadmap` 中に復帰先が残っている場合は復旧対象とする。残存情報を消さず、既存の復旧手順に従う。
- 通常、`進行中` のRoadmap Stepと同一Progress内の `進行中` Stepはそれぞれ1つだけとする。
- 通常の起動・再開と状態変更の前後に、Roadmapの「保留・再開履歴」の整合性規則を全Stepへ適用する。Statusが保留以外でも未再開の保留行が残っていれば復旧対象とする。独立学習中も履歴を検証するが、現在の親Step照合とは区別する。
- Roadmap、Plan、Progress、状態ファイルに矛盾・欠損・複数候補があれば、学習や実装を止めて `復旧待ち` とする。
- 正しい状態が履歴と完了根拠から一意に決まる場合は、変更内容と根拠を説明して同期を回復する。一意に決まらない場合だけ、必要な選択をユーザーへ確認する。
- 完了済み記録を削除して矛盾を隠さない。訂正理由と影響範囲を履歴に残す。

### 必ず守ること

- ユーザーが考える前に完成回答・完成設計・完成実装を提示しない。
- Planが確定する前に、そのPlanの学習Stepや実装へ進まない。
- 原則として現在のStepだけを扱う。
- Step完了条件を満たしていない場合は、完了扱いにしない。
- Step完了後も、ユーザーが確認するまでは次Stepを開始しない。
- 既に理解している内容は必要以上に繰り返さない。
- 不足している前提知識が判明した場合は、Plan変更または追加Learning Planを提案し、理由と影響範囲を説明する。
- 完了済みのProgress記録は削除しない。
- 設計判断では、採用案だけでなく理由・代替案・条件変更時の影響も確認する。

### コード変更時

- 学習目的がある変更では、いきなり大きな実装を完成させない。
- ユーザー自身が実装するStepでは、まずヒント・確認質問・レビューを優先する。
- ユーザーが明示的に実装を依頼した場合は、その範囲内で変更する。
- 既存の設計やPlanと矛盾する変更を勝手に入れない。
- 変更後は、関連するテスト・ドキュメント・Progress更新の必要性を確認する。

---

## 5. ドキュメントの役割

### `docs/plan/`

プロジェクト全体のRoadmapを置きます。

Roadmapは、各Roadmap Stepの目的・完了条件に加えて、永続的なStep ID、Status、関連Learning Planを記録するプロジェクト全体の進捗台帳でもあります。

詳細な演習や学習途中のメモはRoadmapへ詰め込みません。

### `docs/study/`

学習方法、Learning Plan、Progressを置きます。

- `docs/study/learning-workflow.md`: 学習運用ルール
- `docs/study/learning-state.md`: 現在の運用範囲・対象・フェーズ・次の1アクション
- `docs/study/user-prompts.md`: ユーザーが普段使う短い依頼文
- `docs/study/templates/`: Plan / Progressテンプレート
- `docs/study/plans/`: テーマ別Learning Plan
- `docs/study/progress/`: テーマ別Progress

### `docs/architecture/`

学習後に理解・確定した設計成果物を置きます。

学習途中のメモや進捗状況はここへ混在させません。

---

## 6. セッションをまたぐときの再開ルール

新しいセッションでは、会話履歴ではなく以下の順番で現在地を復元してください。

1. `learning-state.md` の運用範囲、対象ID、フェーズ、次の1アクションを確認する。
2. 運用範囲が `roadmap` の場合だけ、現在のRoadmap StepのStatusと関連Learning Planを照合する。`independent` では現在Roadmap Step IDの `なし` を確認し、保存済み復帰先を別に検証する。復帰先を現在の親Stepとして扱わない。
3. 状態ファイルが指定するLearning Planと、その親Roadmap Step IDが一致することを確認する。
4. 対応するProgressの学習ID、親Roadmap Step ID、現在Stepを確認する。
5. 状態ファイルのフェーズとユーザー確認待ちを確認する。
6. Progressと状態ファイルの `次回最初に行うこと` から再開する。

会話履歴とリポジトリ内の記録が矛盾する場合は、リポジトリ内の状態ファイル、Roadmap、対象Plan、対応するProgressを優先し、必要ならユーザーへ差分を説明してください。

状態ファイル、Roadmap、Plan、Progressが不一致の場合は、作業を進めず差異を報告し、`learning-workflow.md` の復旧手順に従ってください。

---

## 7. 完了時の更新

### Plan内Step完了

Stepを完了する前に、Planの完了条件を確認してください。

条件を満たしている場合は、原則としてProgressへ次を記録します。

- 実施した作業
- 成果物への反映
- 理解できたこと
- 自分の言葉で説明できたこと
- 完了を確認した方法
- 完了根拠
- まだ曖昧なこと
- 次回最初に行うこと

条件を満たしていない場合は、状態を `進行中` のままにしてください。

### Learning Plan完了

Plan全体の完了条件を確認し、条件を満たした場合のみPlanとProgressの全体ステータスを `完了` にします。

Plan完了後も、親Roadmap Stepを自動完了しません。独立Planの完了後は学習ワークフロー第12節に従い、検証済みの復帰先へ状態ファイルを戻してから復帰先欄を消去します。検証できなければ復旧待ちとし、復帰先と完了記録を保持します。

### Roadmap Step完了

関連Learning Plan一覧と各Plan / Progressの完了状態を確認したうえで、Roadmap Step自身の完了条件を確認します。

ユーザーが完了承認した場合のみRoadmap StepのStatusを `完了` にします。

---

## 8. 技術方針

v1の中心技術は次を想定します。

- PHP
- Laravel
- PostgreSQL
- Docker
- GitHub Actions
- AWS

AWSは最終的に、ECS / Fargate、ECR、RDS、ALB、CloudWatch、IAM等を学習候補とします。

ただし、具体的な採用構成はRoadmapと各Learning Planの設計Stepで決定し、AGENTS.mdだけを根拠に固定しません。

---

## 9. v1のスコープ方針

v1では機能追加よりも、LaravelからAWS本番稼働まで一周することを優先します。

中心機能は以下です。

- ユーザー登録
- ログイン
- 釣果CRUD

写真、天気、潮汐、地図、統計、高度な検索、フロントエンド拡張などは、実利用後に必要性が確認できた場合に追加候補とします。

---

## 10. 変更時の基本姿勢

このリポジトリでは、設計・実装・学習Planの変更理由を追跡できることを重視します。

大きな変更を行う場合は、次を確認してください。

- 変更理由
- Roadmapへの影響
- Learning Planへの影響
- Progressへの影響
- 既存コード・設計成果物への影響
- テストへの影響

完了済みの学習履歴を消して整合性を取るのではなく、変更理由を残して更新してください。

---

## 11. AIに期待する支援スタイル

このプロジェクトでは、以下の支援を歓迎します。

- 現在地の自動判定
- 理解度診断
- 学習順序の整理
- 設計レビュー
- コードレビュー
- 確認問題や演習
- 誤解の指摘と理由説明
- 代替案比較
- テスト観点の整理
- AWS構成の設計支援
- Roadmap / Plan / Progressの同期更新
- セッション再開時の現在地整理

一方で、ユーザーが学ぶべき部分まで無条件に完成させることは避けてください。

目的は「AIが作れること」ではなく、「ユーザーが理解し、自分で作り、説明できること」です。
