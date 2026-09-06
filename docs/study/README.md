# 学習ドキュメント

`docs/study` は、AIエージェントと進める学習の運用資料、テーマごとのPlan・進捗、学習中に得た技術メモを管理するディレクトリです。

## 学習を始める

ユーザーは基本的に[ユーザー用プロンプト集](./user-prompts.md)だけ見れば運用できます。今の状況に合う短い依頼文を送ると、AIが必要な運用資料と進捗を確認して次の操作を判断します。

運用仕様を確認・変更するときは[学習Planの運用方法](./learning-workflow.md)を参照してください。

AIは開始時に[現在の学習ワークフロー状態](./learning-state.md)を読み、[共通の再開手順](./learning-workflow.md#8-別セッションで再開する)に従って運用範囲とファイルの存在要否に応じた照合を行います。独立学習では現在Roadmap Stepを検索せず、復帰先を別に検証します。`learning-state.md` は現在地のポインタであり、詳細な進捗の代わりではありません。

運用に使用する原本は次の3ファイルです。テンプレートへ直接記入せず、所定のファイルへ複製して使用します。

- [学習Planテンプレート](./templates/learning-plan-template.md)
- [学習進捗管理テンプレート](./templates/learning-progress-template.md)
- [学習ワークフロー状態テンプレート](./templates/learning-state-template.md)

運用仕様を変更した場合は[学習ワークフロー回帰テスト](./workflow-validation.md)で標準・変更・復旧シナリオを再確認します。

## ディレクトリ構成

```text
docs/study/
├── README.md
├── user-prompts.md
├── learning-workflow.md
├── learning-state.md          # 現在の作業対象を示す一意なポインタ
├── workflow-validation.md     # 状態遷移の回帰テスト
├── templates/
│   ├── learning-plan-template.md
│   ├── learning-progress-template.md
│   └── learning-state-template.md
├── plans/                    # テーマ専用の学習Plan
├── progress/                 # テーマ専用の進捗
└── notes/                    # 分野別の技術メモ
    ├── go/
    ├── api/
    ├── testing/
    └── infrastructure/
```

`plans/` と `progress/` は、最初のテーマ専用ファイルを作成するときに追加します。

## ファイルの役割と配置

| 種類 | 役割 | 配置 |
| --- | --- | --- |
| 学習運用 | AIとの学習手順を定義する | `docs/study/learning-workflow.md` |
| 現在状態 | 現在の運用範囲・Plan・Step・次の1アクションを一意に示す | `docs/study/learning-state.md` |
| 回帰テスト | 標準・変更・復旧シナリオの期待動作を検証する | `docs/study/workflow-validation.md` |
| ユーザー用プロンプト | 普段使う短い依頼文を場面別にまとめる | `docs/study/user-prompts.md` |
| テンプレート | Planと進捗の再利用可能な原本 | `docs/study/templates/` |
| 学習Plan | 何を、なぜ、どの順番で学ぶかを定義する | `docs/study/plans/<learning-id>-plan.md` |
| 学習進捗 | 現在地、理解の証拠、次の行動を記録する | `docs/study/progress/<learning-id>-progress.md` |
| 技術メモ | 学習中に確認した知識や手順を分野別に残す | `docs/study/notes/<分野>/` |
| 設計成果物 | 学習結果として確定したシステム設計を残す | `docs/architecture/<領域>/` |

## 技術メモ
- 現状はなし

## 新しいドキュメントの追加ルール

- 学習IDとファイル名には、小文字のkebab-caseを使用する。
- 1つの成果物に対して、同じ学習IDのPlanと進捗ファイルを作成する。
- 技術メモは `notes/laravel`、`notes/api`、`notes/testing`、`notes/infrastructure` など該当分野へ追加する。
- 学習途中の状態は進捗ファイルに記録し、完成した設計書には混在させない。
- `learning-state.md` には現在地のポインタだけを記録し、詳細な学習履歴はProgressへ記録する。
- 新しい技術メモを追加したら、このREADMEの索引も更新する。

## 他リポジトリへ移植する

汎用運用を移植するときは、`learning-workflow.md`、`user-prompts.md`、3つのテンプレート、`workflow-validation.md` を共通部として利用します。この共通仕様はRoadmapのあるリポジトリを対象とします。導入先では次をプロジェクト固有情報へ置き換えます。

1. Roadmapのファイル名、Step ID、目的、完了条件。
2. `AGENTS.md` のプロジェクト目的、技術方針、成果物配置。
3. `user-prompts.md` のプロジェクト名。
4. 状態テンプレートから作成する `learning-state.md` の初期Roadmap Step IDと次の1アクション。

状態値、遷移条件、承認境界、復旧規則は共通仕様として維持します。導入先には実際の学習目的・完了条件を持つRoadmapを用意し、運用範囲 `roadmap`、最初のStepの `準備`、Plan・Plan内Step・復帰先は `なし` で初期化します。

Roadmapを持たずに常時 `independent` とする運用は、この仕様では未対応です。第12節の独立学習はRoadmapからの一時的な切替であり、復帰先が必要です。Roadmapなしで利用する場合は、初回Planの開始・完了後の待機・次Planの開始・再開／復旧を別途設計・検証してから導入してください。運用範囲だけを変更して移植できるという以前の案内は訂正します。
