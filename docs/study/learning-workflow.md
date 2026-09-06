# 学習Planの運用方法

このドキュメントは、Fishing Log全体のRoadmapと個別Learning Planを区別し、AIエージェントと一緒に設計書などの成果物をPlan内の1 Stepずつ完成させるための運用手順です。

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

Roadmapの1 Stepは、理解度や成果物の粒度に応じて複数のLearning Planへ分割できます。

通常の利用では、ユーザーがRoadmap運用 / 個別Learning Plan運用やA/B番号を意識する必要はありません。ユーザーが「現在地から進めて」「続きをやりたい」と依頼した場合、AIがリポジトリ内の永続情報から現在地と次の操作を判断します。

## この運用の目的

- 学習を始める前に、ゴールと学習順序を明確にする。
- AIに完成品を作ってもらうだけでなく、自分で説明・判断できる状態を目指す。
- セッションが変わっても、現在地と次の作業を正確に引き継ぐ。
- Roadmap Stepと複数のLearning Planの関係を永続的に追跡できるようにする。
- 学習結果を、設計書などの再利用できる成果物として残す。

## 運用情報の正本

各ファイルの正本としての役割を次のように分けます。

| 情報 | 正本 |
| --- | --- |
| 現在扱っている運用範囲・Roadmap Step・Plan・Plan内Step・次の1アクション | `docs/study/learning-state.md` |
| Roadmap Stepの順序・状態・関連Plan・完了条件 | Roadmap（状態・関連Planの更新先はStatus一覧のみ） |
| Learning Planの学習経路・完了条件・Roadmapへの寄与 | 対象Plan |
| Plan内Stepの実績・理解の証拠・未解決事項・再開地点 | 対象Progress |
| AIの共通行動規則 | `AGENTS.md` とこの文書 |
| ユーザーが送る依頼文 | `docs/study/user-prompts.md` |

`learning-state.md` は詳細情報を複製せず、正本をたどるためのポインタだけを保持します。ポインタと詳細ファイルが矛盾する場合、AIは推測でどちらかを上書きせず、後述の復旧手順へ移ります。

## 状態モデル

### Roadmap Step

`未着手 / 進行中 / 保留 / 完了` を使用します。通常、`進行中` にできるRoadmap Stepは1つだけです。独立学習を行っている間も、Roadmap Stepの状態は変更せず、`learning-state.md` の運用範囲だけを `independent` に切り替えます。

### Roadmap Stepの保留と再開

Roadmap Stepの保留はPlanの保留と区別します。運用範囲が `roadmap` のとき、ユーザーが現在のStepの保留を依頼したら、途中経過を存在するProgressへ保存し、Roadmapの[保留・再開履歴表](../plan/fishing-log-roadmap.md#roadmap保留再開履歴)へ規定の履歴ID・Step ID・変更前後Status・理由を記録してStatus一覧の該当行を `保留` にします。状態ファイルの対象・フェーズ・確認待ち・次の1アクションとPlanの状態は保持し、状態遷移履歴へ保留操作を記録します。これにより準備・レビュー・承認待ちを失いません。完了Stepを保留に変えたり、独立学習中に復帰先のStepを保留に変えたりしません。

状態ファイルが指す `保留` Stepは不整合ではありません。通常の照合後、ユーザーから当該Stepの再開依頼があるまでは、保存した次の行動やPlan確定・学習・完了処理へ進みません。「現在地から再開して」は現在の保留Roadmap Stepの再開依頼として扱えます。再開時は同表の未再開の保留行を1件だけ選び、その変更前Statusへ戻します。対応する保留履歴IDを持つ再開行を追記し、Status一覧と状態遷移履歴を同期します。該当行が0件・複数件なら復旧対象とします。`進行中` へ戻す際に別のStepが進行中なら復旧対象とし、履歴が欠ける場合も推測でStatusを決めず復旧手順に従います。

再開後は保持したフェーズ・確認待ちから進め、Plan確定や完了承認は代行しません。Plan自体も保留なら、その解除は下記のPlan保留手順で別に扱います。独立学習から保留Stepへ復帰しただけではRoadmap Stepの保留を解除しません。

### Learning Plan

`作成中 / レビュー待ち / 確定 / 保留 / 完了 / 廃止` を使用します。

- `作成中`: 理解度診断またはPlan編集中。
- `レビュー待ち`: 学習者の確定を待っている。
- `確定`: 実行可能で、対応するProgressが存在する。
- `保留`: 再開可能な状態を保持して一時停止している。
- `完了`: Plan全体の完了条件と学習者確認を満たした。
- `廃止`: 置換・統合などで今後実行しない。理由と後継Planを変更履歴へ残す。

### 確定履歴と保留からの再開

Planの `確定履歴` は `未確定 / 確定済み` を使用します。新規作成時は未確定、ユーザー確定時にPlan・Progress・状態ファイルの同期と同時に確定済みへ変更し、その後は保留・完了・廃止でも維持します。未確定PlanはProgressなしが正常で、確定済みPlanは保留中もProgressが必須です。既存Planで確定履歴が欠けている場合は確定承認・変更履歴から一意に補い、ファイルの有無だけで判断しません。不明なら復旧待ちとします。

ユーザーの依頼でPlanを保留する際は、Planの保留時の再開情報へ変更前の全体ステータス・フェーズ・Step・確認待ち・次の1アクションを保存してから全体ステータスを保留にします。確定履歴は維持し、確定済みの場合だけProgressにも途中経過を保存します。独立Planではこの保存後に第12節でRoadmapへ復帰します。新規Planとして作り直したり、未確定PlanにダミーProgressを作ったりしません。

保留Planの再開はユーザーが対象を指定したときだけ行います。保存情報と確定履歴を検証し、Planの全体ステータスを保留前の値へ戻して状態ファイルを同期します。未確定なら作成・レビューへ、確定済みなら必要なProgressを照合して保存した停止地点へ戻します。独立PlanをRoadmapから再開する場合は、第12節の切替条件を満たすことを確認し、現在のRoadmap停止地点を新たな復帰先へ保存してから、既存Planと保存したフェーズ・Step・確認待ちを現在地に設定します。新規提案用のPlanレビュー待ちへリセットしません。独立学習からの直接切替は禁止を維持します。

### ProgressとPlan内Step

`未着手 / 進行中 / 保留 / 完了` を使用します。1つのProgress内で `進行中` にできるPlan内Stepは1つだけです。

### 現在のフェーズ

`learning-state.md` では次を使用します。

`準備 / Planレビュー待ち / 学習待ち / 学習中 / Step完了承認待ち / Plan完了承認待ち / Roadmap完了承認待ち / 保留 / 復旧待ち / なし`

フェーズは「次に許可される操作」を示します。確認待ちのフェーズでは、ユーザーが承認するまで次の学習や状態遷移へ進みません。

## 状態遷移と同期更新

主要な遷移は次のとおりです。

| 操作 | 遷移前 | 遷移後 | 同じ作業で同期するもの | ユーザー確認 |
| --- | --- | --- | --- | --- |
| Plan提案 | `準備` | `Planレビュー待ち` | Plan、`learning-state.md` | 確定前に必要 |
| Plan確定 | `Planレビュー待ち` | `学習待ち` | Plan、Progress作成、`learning-state.md`。Roadmapに紐づくPlanの場合だけRoadmapの関連PlanとStatusも更新 | 必須 |
| Plan内Step開始 | `学習待ち` | `学習中` | PlanのStep状態、Progress、`learning-state.md` | 明示依頼が必要 |
| Step中断（通常のセッション終了） | `学習中` | `学習中` | Progress、`learning-state.md`。Stepは進行中を維持 | 不要 |
| Plan保留（Step中断から保留する場合も含む） | 作成中・レビュー待ち・確定Planの停止地点 | Roadmapに紐づくPlanは `保留`、独立Planは保存後に第12節で復帰 | 「確定履歴と保留からの再開」に従いPlanの保留前情報を保存、存在要否に応じたProgress、`learning-state.md`を同期 | ユーザーの保留依頼が必要 |
| Step完了候補 | `学習中` | `Step完了承認待ち` | Progressの根拠、`learning-state.md` | 完了に必須 |
| Step完了 | `Step完了承認待ち` | `学習待ち` または `Plan完了承認待ち` | Plan、Progress、`learning-state.md` | 必須 |
| Plan完了 | `Plan完了承認待ち` | Roadmapに紐づくPlanは次の対象に応じたフェーズ、独立Planは検証済み復帰先のフェーズ（検証失敗時は復旧待ち） | Plan、Progress、`learning-state.md`。独立Planは第12節の復帰手順を適用 | 必須 |
| Roadmap Step保留・再開 | `未着手 / 進行中` ↔ `保留` | 保留前Statusを履歴に保存・復元。現在のフェーズは維持 | 上記「Roadmap Stepの保留と再開」に従いStatus一覧・履歴・必要なProgressを同期 | 当該Stepの保留・再開依頼が必要 |
| Roadmap Step完了 | `Roadmap完了承認待ち` | 次Stepの `準備` または `なし` | Roadmap、`learning-state.md` | 必須 |
| 独立学習開始 | 運用範囲=`roadmap`、復帰先がすべて `なし` の安全な停止地点 | 独立Planの `Planレビュー待ち` | 現在の再開情報を状態ファイルの復帰先へ保存、元のProgressが存在する場合だけ併記、独立Plan、`learning-state.md` | Plan確定前に必要 |
| 独立学習中に別の独立学習を依頼 | 運用範囲=`independent`、復帰先は正常なRoadmapの停止地点 | 現状維持 | 更新なし。直接切替・新しいPlan作成をせず、Roadmapへ戻ってから提案する順序を案内 | 終了・保留・復帰を無断で行わない |
| 矛盾検出 | 任意 | `復旧待ち` | `learning-state.md` に差異と次の確認を記録 | 修復内容により必要 |

通常の起動・再開と状態変更の前後には、運用範囲によらず[Roadmap保留・再開履歴の整合性規則](../plan/fishing-log-roadmap.md#roadmap保留再開履歴)も適用します。全Stepについて、保留なら未再開行が1件、それ以外なら0件であることと、履歴の参照関係を確認します。独立Planの親照合とは別の検証であり、不整合なら学習や新たな保留へ進まず復旧します。

同期対象の一部だけ更新して処理が中断した場合、その状態を正常とはみなしません。次回開始時に整合性チェックを行い、`復旧待ち` にしてから不足更新を補います。履歴から一意に補えない場合だけユーザーへ確認します。

## 一意な現在地を決める規則

1. `learning-state.md` の現在の運用範囲とIDを入口にする。
2. 下記の存在要否に従ってRoadmap、Plan、Progressを照合し、IDと状態が一致するか確認する。
3. 一致すれば `現在のフェーズ` と `次に行う1アクション` から再開する。
4. `learning-state.md` が初期状態または対象なしなら、Roadmapの最初の `未着手` Stepを準備対象にする。
5. `learning-state.md` が欠損している場合に限り、Roadmap、Plan、Progressから候補を復元する。候補が1つなら状態ファイルを再作成し、複数なら `復旧待ち` としてユーザーへ候補を示す。

RoadmapやProgressの状態だけから複数候補の優先順位を推測してはいけません。複数Planを並行して保持する場合も、実際に再開するPlanは `learning-state.md` で1つだけ指定します。

### 再開時のファイルの存在要否

以下は通常の再開・整合性チェック・独立学習からの復帰に共通の規則です。

- Planなしの `準備` ではPlan / Progress照合は対象外とする。
- 確定履歴が `未確定` のPlan（保留中も含む）はProgressなしが正常とする。保留中は上記の再開情報に従う。Planが `作成中 / レビュー待ち` なら、状態ファイルが指すPlanを読む。Roadmap未登録・Progress未作成が正常であり、登録やProgress照合を要求しない。状態ファイルの次の1アクションに従って作成・レビューを再開し、確定前に学習へ進まない。
- 確定済みPlanではProgressが必要であり、欠損は復旧対象とする。独立Planは親Roadmap Stepが `なし` のため、Roadmapへの登録・親Step照合は対象外とする。
- 以降のPlan / Progress照合は、この存在要否に従う。必須ではないファイルを照合のためだけに作成しない。

## 現在地の判断

現在地は会話履歴ではなく、リポジトリ内の記録を正として判断します。最初に `docs/study/learning-state.md` を確認し、以下のRoadmap / Plan / Progress照合を行います。

### Roadmap上の現在地

この節は現在の運用範囲が `roadmap` の場合だけ適用し、`docs/plan/fishing-log-roadmap.md` のStatus一覧を確認します。`independent` では現在のRoadmap Stepを検索せず、下記の別セッション再開手順に従って現在Planと保存済み復帰先を別々に照合します。

1. `learning-state.md` が指定するRoadmap Stepを確認する。
2. 指定されたStepがRoadmapの `進行中`、次に準備する `未着手`、または保存された停止地点の `保留` Stepと一致することを確認する。保留は上記「Roadmap Stepの保留と再開」に従い、再開依頼まで処理を止める。
3. 状態ファイルに指定がなく、`進行中` のRoadmap Stepが1つだけなら復元候補とする。
4. `進行中` のRoadmap Stepが複数ある場合は作業を止め、復旧対象とする。
5. そのStepの `関連Learning Plan` と状態ファイルが指定するPlanを確認する。
6. 現在のPlanの作成・レビューや承認待ちがなく、すべての関連PlanとProgressが `完了` でRoadmap Stepが `進行中` の場合は、Roadmap Step自身の完了条件を確認する。

### 個別Learning Plan内の現在地

上記の存在要否を確認し、確定済みの対象Planと対応するProgressを照合します。

- PlanとProgressの `学習ID` が一致していること。
- PlanとProgressの `親Roadmap Step ID` が一致していること。
- Roadmapに紐づく確定済みPlanは、Status一覧の関連Learning Planに登録されていること。
- Progressの `現在のStep`、`まだ曖昧なこと`、`次回最初に行うこと` を再開地点とすること。
- `learning-state.md` が同じPlanとPlan内Stepを指していること。

Roadmap、Plan、Progressの関連情報が矛盾する場合は、作業を進めず差異を説明します。

## 矛盾・欠損時の復旧

次のいずれかを検出したら、学習・実装・完了処理を進めません。

- `learning-state.md` が指すファイルまたはIDが存在しない。
- 復帰先の運用範囲が `independent`、または現在の運用範囲が `roadmap` なのに復帰先のいずれかの項目が `なし` ではない。切替・復帰を止め、残存情報を消さずに以下の復旧手順へ進む。
- Roadmapの関連PlanとPlanの親Roadmap Stepが一致しない。
- PlanとProgressの学習ID、親Roadmap Step、現在Stepが一致しない。
- `確定` のPlanにProgressがない、またはProgressに対応するPlanがない。
- 同時に複数のRoadmap Stepまたは同一Plan内の複数Stepが `進行中` である。
- PlanとProgressの完了状態が矛盾する。
- ユーザー確認待ちなのに、確認内容または完了根拠がない。

復旧は次の順番で行います。

1. 差異を、ファイル・項目・現在値・期待値の形で示す。
2. Git差分、状態遷移履歴、完了根拠、最終更新日を確認する。
3. 根拠から正しい状態が一意なら、変更予定と根拠を説明して整合を回復する。
4. 一意でなければ `learning-state.md` を `復旧待ち` にし、選択が必要な点だけをユーザーへ確認する。
5. 修復後にRoadmap、Plan、Progress、状態ファイルを再照合する。
6. 整合性チェックがすべて `一致` または `対象外` になってから通常運用へ戻る。

完了済みの根拠や履歴を削除して帳尻を合わせてはいけません。誤記を訂正する場合も、変更理由を履歴へ残します。

## 使用するファイル

### Roadmap

- `docs/plan/fishing-log-roadmap.md`

Statusと関連Learning Planの唯一の正本・更新先はRoadmapのStatus一覧です。Plan確定・置換・分割・削除やStepの状態遷移では該当行を更新します。詳細節には可変状態を複製しません。

Roadmapはプロジェクト全体の親計画であり、各Roadmap Stepについて以下を保持します。

- Roadmap Step ID
- Status
- 関連Learning Plan
- 目的
- 主な対象
- 完了条件

### テンプレート

テンプレートは原本として保管し、直接進捗を書き込みません。

- Plan用: `docs/study/templates/learning-plan-template.md`
- 進捗管理用: `docs/study/templates/learning-progress-template.md`

### 学習テーマごとに作成するファイル

```text
docs/study/plans/<learning-id>-plan.md
docs/study/progress/<learning-id>-progress.md
docs/architecture/<領域>/<成果物名>.md
```

Roadmapに紐づくPlan / Progressは、Roadmapと同じ親Roadmap Step IDを必ず記録します。

Roadmap外の独立学習では、親Roadmap Stepを `なし` とします。

## ファイルごとの役割

| ファイル | 役割 | 主な更新タイミング |
| --- | --- | --- |
| Roadmap | プロジェクト全体の順序、Roadmap StepのStatus、関連Planを管理する | Roadmap Step開始・Plan追加・Roadmap Step完了時 |
| 学習Plan | 何を、なぜ、どの順番で学ぶかを定義する | Plan作成時、学習経路を変更するとき |
| 学習進捗 | 現在地、理解の証拠、未解決事項、次の行動を記録する | Plan内Step完了時、セッション終了時 |
| 成果物 | 学習結果として確定した設計や判断を残す | 各Stepで内容を理解したあと |

成果物には、学習途中の一時的なメモや進行状態を混在させません。

## 全体の流れ

```text
Roadmapの現在地を確認
        ↓
対象Roadmap Stepを特定
        ↓
必要なLearning Planを検討
        ↓
理解度診断
        ↓
Learning Plan作成
        ↓
親Roadmap Step IDをPlanへ記録
        ↓
学習者がPlanをレビューして確定
        ↓
Progress初期化・状態ファイル更新（Roadmapに紐づくPlanのみStatus一覧へ関連付け）を同時に行う
        ↓
Plan内Stepを1つだけ学習
        ↓
理解確認後、成果物とProgressを更新
        ↓
Learning Plan完了
        ↓
Roadmap Stepの完了条件を確認
        ↓
学習者確認後にRoadmap Stepを完了
        ↓
次のRoadmap Stepへ
```

## 1. Roadmap Stepを開始する

次に取り組むRoadmap Stepを決めるときは、まずRoadmapのStatus一覧を確認します。

新しいStepを開始する場合は、そのStepをいきなり実装せず、次を行います。

1. Roadmap Stepの目的・主な対象・完了条件を確認する。
2. 既存の成果物・コード・Learning Planを確認する。
3. そのStepを達成するために必要なLearning Planを整理する。
4. 最初に作るLearning Planの理解度診断を行う。

Learning Planを確定した時点で、Roadmapの対象Stepを `進行中` にし、関連Learning Planへ学習IDとPlanパスを追加します。

## 2. Learning Planを作成する

最初からPlanを確定させず、AIに現在の知識や経験を確認してもらいます。

Planには必ず次を記録します。

- 学習ID
- 学習テーマ
- 親Roadmap Step ID
- 親Roadmap Step
- 対象成果物
- 対応するProgress

Roadmap外の独立学習なら、親Roadmap Stepを `なし` とします。

AIは診断結果を基に、既に理解している内容を短くし、未経験の内容を細かく分割します。

Planが確定したら、そのPlanがRoadmapに紐づく場合は、Roadmapの `関連Learning Plan` と必ず同期します。

作成中・レビュー待ちのPlanはRoadmapへ登録せず、Progressも作りません。ユーザーがPlanを確定したときに限り、Planを `確定` にし、Progress初期化と `learning-state.md` の対象Plan・Plan内Step・フェーズの更新を同じ作業で行います。Roadmapに紐づくPlanの場合だけ、同じ作業でRoadmapへの関連付けと対象Stepの `進行中` への更新も行います。

独立Planの確定ではPlan・Progressの親Roadmap情報を `なし` とし、状態ファイルの運用範囲は `independent`、現在Roadmap Step IDは `なし` を維持します。RoadmapのStatus・関連Learning Planと保存済みの復帰先は変更しません。復帰先のStepを独立Planの親として更新してはいけません。Progressと状態ファイルの初期値は次の「Progressを初期化する」に従い、学習開始は別の明示依頼を待ちます。

## 3. 学習Planをレビューする

AIが提案したPlanについて、次を確認します。

- 学びたい内容が含まれているか。
- Stepの大きさが、1回ずつ取り組める粒度になっているか。
- 既に理解している内容に時間を使いすぎていないか。
- 前提知識から応用へ進む順番になっているか。
- 成果物の完成だけでなく、理解確認が含まれているか。
- 設計理由、代替案、条件変更の影響を説明する機会があるか。
- 最後に復習・応用Stepがあるか。
- 対象と対象外が明確か。
- 親Roadmap Stepの完了条件に対して、このPlanが何を担うか説明できるか。

Planをレビューして確定するまでは、Plan内Stepの実行や成果物の作成を開始しません。

## 4. Progressを初期化する

Planが確定したら、Progressテンプレートを基にテーマ専用Progressを作成します。

ProgressにはPlanと同じ次の値を転記します。

- 学習ID
- 親Roadmap Step ID
- 親Roadmap Step
- 対応するPlan
- 対象成果物

初期状態の目安は次のとおりです。

- 全体ステータス: `未着手`
- 現在のStep: `Step 1`
- すべてのStepの状態: `未着手`
- 次回最初に行うこと: `Step 1の目的と完了条件を確認する`

同じ作業で `learning-state.md` の現在Planを新しい学習ID、現在のPlan内Stepを `Step 1`、フェーズを `学習待ち` にします。

## 5. 現在のPlan内Stepを1つだけ進める

各Plan内Stepは、原則として次の順番で進めます。

1. AIが目的、論点、完了条件を説明する。
2. AIが質問や演習を提示する。
3. 学習者が予想、回答、判断または草案を出す。
4. AIが正しい点、不足、誤解とその理由を説明する。
5. 学習者が回答や草案を修正する。
6. 理解できた内容を成果物へ反映する。
7. 学習者が重要な内容を自分の言葉で説明する。
8. AIと学習者が完了条件を確認する。

開始時にPlan、Progress、`learning-state.md` の現在Stepを `進行中` / `学習中` へ同期します。

AIによる完成例や模範回答が必要な場合は、学習者が明示的に依頼します。

## 6. Plan内Step完了後にProgressを更新する

成果物を作成しただけではStepを完了にしません。学習者が内容や判断理由を説明し、Planに記載された完了条件を満たしていることを確認します。

条件を満たしている場合は、Progressへ次を記録します。

- 実施した作業
- 成果物への反映
- 理解できたこと
- 自分の言葉で説明できたこと
- 完了を確認した方法
- 完了根拠
- まだ曖昧なこと
- 次回最初に行うこと

完了条件を満たしていない場合は、状態を `進行中` のままにします。

ユーザーが確認するまでは次のPlan内Stepを開始しません。

完了条件を満たした候補になった時点では、`learning-state.md` を `Step完了承認待ち` にし、ユーザー確認後にだけPlanとProgressのStepを `完了` へ同期します。

## 7. Step途中でセッションを終了する

Step途中で終了しても問題ありません。

通常のセッション終了は以下の中断保存を行い、Planを保留にはしません。ユーザーが明示的に保留を依頼した場合は、上記「確定履歴と保留からの再開」のPlan保留手順へ進みます。Planの保留前情報を保存せずProgressと状態ファイルだけを保留にする経路はありません。

状態を `進行中` のままにして、Progressへ次を残します。

- 現在までに実施した内容
- 成果物への反映
- 理解できたこと
- まだ曖昧なこと
- 未解決事項
- 次回最初に行う具体的な1アクション

`learning-state.md` にも同じPlan・Stepと次の1アクションを記録します。詳細はProgressだけに残し、状態ファイルへ学習履歴を複製しません。

`次回最初に行うこと` は、別セッションのAIが即座に着手できる具体性を持たせます。

## 8. 別セッションで再開する

ユーザーは通常、[ユーザー用プロンプト集](./user-prompts.md)の短い依頼だけで再開できます。

AI側は次の順序で現在地を復元します。

1. `learning-state.md` の運用範囲、対象ID、フェーズ、次の1アクションを確認する。
2. 運用範囲が `roadmap` の場合だけ、RoadmapのStatus一覧と現在の対象Roadmap Stepを照合する。`independent` では現在Roadmap Step IDが `なし` であることを確認し、Status一覧の現在Step検索は行わない。保存済み復帰先は第12節の手順で別に検証するが、再開だけで復帰・消去はしない。
3. 状態ファイルが指定するPlanを確認し、上記の存在要否に従ってRoadmapへの登録を照合する。
4. Progressが必要な場合は、Plan / Progressの学習ID、親Roadmap Step ID、現在Stepを照合する。
5. 整合していれば、状態ファイルと、存在する場合はProgressの次の1アクションから再開する。作成中・レビュー待ちなら作成・レビューを再開し、後続の完了判定へ進まない。
6. 運用範囲が `roadmap` で、現在のPlanの作成・レビューや承認待ちがなく、関連Planがすべて完了しRoadmap Stepが `進行中` なら、Roadmap Stepの完了判定へ進む。
7. 運用範囲が `roadmap` で、Roadmap Stepが完了していて対象Planがないなら、次の未着手Roadmap Stepの準備へ進む。
8. 不一致または複数候補があれば `復旧待ち` とし、学習を進めない。

上記の照合後も、現在Roadmap Stepが保留なら「Roadmap Stepの保留と再開」を先に適用します。ユーザー確認が必要な完了・Plan確定・次Step開始を勝手に飛ばしてはいけません。

## 9. Planを途中で変更する

学習中に前提知識の不足や新しい論点が判明した場合は、Planを変更できます。

変更時は次を行います。

1. AIが変更理由と後続Stepへの影響を説明する。
2. 親Roadmap Stepへの影響を確認する。
3. 学習者が変更内容を確認する。
4. Planを更新する。
5. ProgressのPlan変更履歴を更新する。
6. 関連Planの追加・削除・分割がある場合はRoadmapも同期する。
7. 現在地または次のアクションが変わる場合は `learning-state.md` も同期する。

完了済みの記録は削除しません。

## 10. Learning Plan全体を完了する

すべての通常Stepが完了したあと、Planの最終Stepとして復習・応用を実施します。

- 資料を見ずに主要な設計判断を説明する。
- 採用案と代替案を比較する。
- 前提条件が変わった場合の設計変更を考える。
- 代表的な障害の原因と切り分け方を説明する。
- AIの確認問題へ回答する。
- 残った理解不足を今後の学習候補へ記録する。

成果物の完成と理解確認の両方を満たし、学習者が確認したら、PlanとProgressの全体ステータスを `完了` にします。Roadmapに紐づくPlanでは、`learning-state.md` を同じRoadmap Stepの次の確定Plan、追加Plan検討、またはRoadmap完了承認待ちのいずれかへ更新します。

独立Planでは第12節の復帰手順を適用し、保存済みのRoadmap停止地点を検証してから、運用範囲・対象ID・Plan内Step・フェーズ・次の1アクションと確認待ちを復元します。復元が成功した場合だけ復帰先欄を `なし` にします。検証できなければ `復旧待ち` とし、復帰先と独立Planの完了記録を保持します。RoadmapのStatusを更新したり、復帰先を推測で選んだり、復帰後の学習を自動開始したりしません。完了後に復帰処理だけが中断した場合も、再開時には完了承認の記録を確認して第12節から復帰を再試行します。

Learning Planを完了しても、親Roadmap Stepは自動完了しません。

## 11. Roadmap Stepを完了する

Roadmap Stepの完了判定では、次を確認します。

1. Roadmapに登録された関連Learning Planをすべて確認する。
2. 各Plan / Progressが完了していることを確認する。
3. Roadmap Step自身の完了条件を確認する。
4. 成果物・コード・テストなど必要な根拠を確認する。
5. 不足があれば、既存Planを継続するか、追加Learning Planが必要か判断する。
6. 完了条件を満たした場合はユーザーへ完了根拠を提示する。
7. ユーザーが承認した場合のみRoadmap StepのStatusを `完了` に更新する。
8. `learning-state.md` を次の未着手Roadmap Stepの `準備`、またはRoadmap完了時の `なし` へ更新する。

Roadmap Step完了後も、ユーザー確認なしに次のRoadmap Stepの学習・実装を開始しません。

## 12. Roadmap外の独立学習

Fishing Log Roadmapとは直接関係しないテーマも、同じLearning Plan / Progress運用を利用できます。

その場合はPlan / Progressの以下を `なし` とします。

- 親Roadmap Step ID
- 親Roadmap Step

独立学習の完了はRoadmapのStatusへ影響しません。

独立学習への切替は、現在の運用範囲が `roadmap`、復帰先の全項目が `なし`、安全な停止地点にある場合だけ許可します。復帰先はRoadmapの停止地点1件だけを保持し、上書きしません。開始前に整合性を確認し、復帰先が `independent`、またはRoadmap上に復帰先が残っている場合は、残存情報を保持して復旧手順へ進みます。履歴から正しい状態が一意に決まる場合だけ訂正し、理由を記録します。

`independent` 中に別の独立学習を依頼された場合は、入れ子の切替を行いません。現在のPlan・Progress・状態ファイル（復帰先を含む）を維持し、新しいPlanも作成しません。現在の独立学習を終了または保留してRoadmapへ戻り、その後に別の独立学習を提案する順序を案内します。終了・保留・復帰は無断で行わず、終了には既存の完了条件とユーザー確認を適用します。この制約は作成中・レビュー待ち・学習途中のいずれにも適用します。

独立学習を開始するときは、Roadmap側の現在状態を変更しません。現在の運用範囲・Roadmap Step・Plan・Plan内Step・フェーズ・ユーザー確認待ちの内容・次の1アクションを、必ず `learning-state.md` の復帰先へ保存します。元のProgressが存在する場合だけ、そこにも再開情報を記録します。初期準備など元のPlan / Progressがない場合は、該当IDを `なし` として状態ファイルだけに保存し、ダミーのProgressを作りません。

保存後、状態ファイルの運用範囲を `independent`、Roadmap Step IDとPlan内Stepを `なし`、Plan IDを独立Planの学習ID、フェーズを `Planレビュー待ち` にします。ユーザー確認待ちと次の1アクションも独立Planのレビュー内容に合わせます。独立Planの確定前はProgressを作成しません。

独立学習を終了または保留したら、復帰先フェーズとPlanの状態に応じて「再開時のファイルの存在要否」を適用し、Roadmapと必要なPlan / Progressを照合します。初期準備への復帰ではPlan / Progress照合を対象外とし、確定済みPlanのProgress欠損は復旧対象とします。整合が確認できた場合だけ保存した対象・フェーズ・次の1アクションへ戻し、復帰先欄を `なし` にします。確認待ちの内容も復帰先からそのまま復元します。復帰先の確認待ち欄が欠損している旧記録は、Roadmapの承認記録・Plan・Progress・状態遷移履歴から一意に復元できる場合だけ理由を残して補い、不明なら復旧待ちとします。確認待ちを推測で「なし」にしません。復帰によって承認や次Step開始を代行しません。

途中でFishing Logの前提学習として必要になった場合は、ユーザー確認の上で親Roadmap Stepへ紐づけ直し、Roadmap・Plan・Progressを同期します。

## 運用上の重要ルール

- ユーザーにA/B番号や詳細プロンプトの選択を要求しない。
- テンプレートは原本として保ち、テーマ専用ファイルへ複製して使用する。
- Roadmap / Plan / Progressの関連を同期する。
- `learning-state.md` を唯一の現在地ポインタとして同期する。
- Roadmapに紐づくPlanは親Roadmap Step IDを必須とする。
- Plan確定前にPlan内Stepを開始しない。
- 作成中・レビュー待ちのPlanをRoadmapへ登録せず、Progressも作らない。
- 一度に扱うのは現在の1 Stepだけとする。
- 学習者が考える前に、AIが完成回答を提示しない。
- 成果物を作ったことと、内容を理解したことを分けて確認する。
- Step完了には、具体的な理解の証拠と学習者の確認を必要とする。
- Step完了時またはセッション終了時にProgressを更新する。
- Learning Plan完了とRoadmap Step完了を区別する。
- 次回の開始地点は、すぐ着手できる具体的な1アクションとして記録する。
- 実装から確認できる事実、一般的な推奨、AIの推測を区別する。
- パスワード、トークン、秘密鍵などの秘密情報を学習ファイルへ記録しない。

## ユーザーが使う依頼文

コピペ用の依頼文は[ユーザー用プロンプト集](./user-prompts.md)へ集約します。この文書は運用仕様を正とし、依頼文の重複管理は行いません。

## 状態遷移の文書検証

標準シナリオは、`未着手` のRoadmap StepからPlanを提案し、ユーザーの確定後にだけPlanの `確定`、Progress作成、Roadmapへの関連付けと `進行中` 更新、`learning-state.md` の更新を同時に行うことで開始できます。Plan内Stepを途中終了した場合はProgressを `進行中` のまま保存し、状態ファイルに同じPlan・Stepと次の1アクションを残します。別セッションでは状態ファイルを入口にRoadmap、Plan、Progressを照合して再開します。StepとPlanの完了承認後、Plan / Progressを `完了` にし、Roadmap Step自身の完了条件を別に判定します。ユーザーがRoadmap Stepの完了を承認した後だけRoadmapを `完了` にし、次Stepは準備までに留めます。

1つのRoadmap Stepに複数Planがある場合も、Roadmapの関連Learning Plan一覧を母集団とし、各Planの `確定 / 完了` と各Progressの `未着手 / 進行中 / 完了 / 保留` を照合します。1つでも未完了または不足を補う追加Planが必要ならRoadmap Stepを完了せず、すべてが完了してもRoadmap Step自身の条件とユーザー承認を省略しません。
