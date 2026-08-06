# Power Apps テストケース定義標準

作成日：2026-08-06

## 0. 目的

本標準は、人間とAIが同じ意味でテストケースを作成、実行、変更、評価できるように、テストケースの必須項目と値を定める。

---

## 1. テストレベル

| 値 | 意味 |
|---|---|
| `functional_unit` | 機能単体テスト |
| `integration` | 機能間結合テスト |
| `system` | システムテスト |

テストレベルは実施工程ではなく、テストが保証する対象と目的を示す属性である。

---

## 2. テストケースID

推奨形式：

```text
<LEVEL>-<TARGET>-<SEQUENCE>
```

| 種別 | 接頭語 | 例 |
|---|---|---|
| 機能単体 | `FUT` | `FUT-SCR-REQUEST-001` |
| 機能間結合 | `FIT` | `FIT-REQUEST-ENTRY-HISTORY-001` |
| システム | `ST` | `ST-REQUEST-SUBMIT-001` |

IDは一度発行した後に再利用しない。

---

## 3. 必須項目

| 項目 | 必須 | 内容 |
|---|---:|---|
| `test_case_id` | ○ | 一意なテストケースID |
| `title` | ○ | 検証内容を表す名称 |
| `test_level` | ○ | `functional_unit` / `integration` / `system` |
| `target_feature_ids` | ○ | 対象機能ID |
| `requirement_ids` | ○ | 対応する要件ID |
| `use_case_ids` | 条件付 | システムテストまたは関連時 |
| `data_flow_ids` | 条件付 | 機能間結合テストまたは関連時 |
| `objective` | ○ | 何を保証するか |
| `design_technique` | ○ | 同値分割、境界値、状態遷移等 |
| `preconditions` | ○ | 環境、ユーザー、権限、データ |
| `test_data` | ○ | 入力値、識別子、作成方法 |
| `steps` | ○ | 人間またはAIが実行可能な操作 |
| `expected_results` | ○ | UI、SharePoint、IF、状態等の期待結果 |
| `postconditions` | ○ | 後処理、削除、復元 |
| `automation` | ○ | `automated` / `manual` / `hybrid` |
| `execution_profiles` | ○ | smoke、impact、full等 |
| `coverage_targets` | ○ | 対象分岐、処理経路、仕様項目 |
| `evidence` | ○ | 保存する証跡 |
| `priority` | ○ | critical / high / medium / low |
| `owner` | ○ | 責任者またはチーム |
| `status` | ○ | draft / approved / active / suspended / retired |
| `n_a_reasons` | 条件付 | 適用しない観点と理由 |

---

## 4. テスト設計技法

対象仕様に応じて以下を使用する。

- 同値分割
- 境界値分析
- デシジョンテーブル
- 状態遷移テスト
- 組合せテスト
- エラー推測
- 処理経路テスト
- データフローテスト
- ユースケーステスト

AIは、使用した設計技法を記録する。

---

## 5. 期待結果の記載

期待結果は、画面文言だけでなく、必要に応じて以下を分けて記載する。

```yaml
expected_results:
  ui:
    - 完了メッセージが表示される
  sharepoint:
    - sp_requestsに1件登録される
    - status列がSubmittedである
  integration:
    - flow_submit_requestが成功する
  state:
    - 二重登録が発生しない
```

期待結果の根拠は [`testing-standard.md`](./testing-standard.md) の情報源優先順位に従う。

---

## 6. 実行プロファイル

| 値 | 意味 |
|---|---|
| `smoke` | PRまたはデプロイごとに行う短時間の重要テスト |
| `impact` | 変更影響に基づき選択するテスト |
| `full` | 夜間、定期実行、リリース前の全体テスト |
| `manual_release` | リリース前に人間が実施するテスト |

1つのテストケースに複数の実行プロファイルを設定してよい。

---

## 7. 自動化区分

| 値 | 意味 |
|---|---|
| `automated` | 全手順と判定を自動化する |
| `manual` | 人間による実施・判定が必要 |
| `hybrid` | 自動実行と人間の判定を組み合わせる |

自動化しない場合は、理由、実施タイミング、実施責任者を記録する。

---

## 8. テンプレート

```yaml
test_case_id: FUT-SCR-REQUEST-001
title: 必須項目を入力して申請を登録できる
test_level: functional_unit
target_feature_ids:
  - SCR-REQUEST-ENTRY
requirement_ids:
  - REQ-001
use_case_ids:
  - UC-REQUEST-SUBMIT
data_flow_ids:
  - DF-REQUEST-ENTRY-TO-LIST
objective: 申請入力画面がSharePointへの登録責務を完結できることを確認する
design_technique:
  - equivalence_partitioning
preconditions:
  environment: test
  user_role: applicant
  data: 登録対象と重複しない申請番号を用意する
test_data:
  test_run_id: runtime-generated
  title: テスト申請
steps:
  - 申請入力画面を開く
  - 必須項目を入力する
  - 登録ボタンを押す
expected_results:
  ui:
    - 完了メッセージが表示される
  sharepoint:
    - sp_requestsに1件登録される
    - 入力値が対応列へ正しく保存される
postconditions:
  - test_run_idを条件に作成データを削除する
automation: automated
execution_profiles:
  - smoke
  - impact
  - full
coverage_targets:
  - btn_submit_request.OnSelectの正常分岐
  - SharePoint登録処理
evidence:
  - screenshot
  - playwright_trace
  - sharepoint_record
priority: critical
owner: application-team
status: approved
n_a_reasons: []
```
