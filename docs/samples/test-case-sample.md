# テストケース記載例

## 例1：画面機能単体テスト

```yaml
test_case_id: FUT-UI001-01-001
title: 申請画面からSharePointへ正常登録できる
test_level: functional_unit
target_feature_ids:
  - UI001-01
requirement_ids:
  - REQ001
use_case_ids:
  - UC001
data_flow_ids:
  - DF001
objective: 申請入力画面が、入力検証からSharePoint登録までの責務を完結できることを確認する
design_technique:
  - equivalence_partitioning
preconditions:
  environment: test
  user_role: applicant
  sharepoint_schema_check: passed
test_data:
  test_run_id: runtime-generated
  application_no: runtime-unique
  title: テスト申請
steps:
  - scr_request_entryを開く
  - 必須項目へ値を入力する
  - btn_submit_request_entryを押す
expected_results:
  ui:
    - 完了メッセージが表示される
  sharepoint:
    - sp_requestsに1件登録される
    - application_noが入力値と一致する
    - titleが入力値と一致する
postconditions:
  - test_run_idを条件に作成データを削除する
automation: automated
execution_profiles:
  - smoke
  - impact
  - full
coverage_targets:
  - btn_submit_request_entry.OnSelectの正常分岐
  - sp_requests登録処理
evidence:
  - screenshot
  - playwright_trace
  - sharepoint_record
priority: critical
owner: application-team
status: approved
n_a_reasons: []
```

## 例2：機能間結合テスト

```yaml
test_case_id: FIT-DF002-001
title: 入力画面で登録した申請が一覧画面へ表示される
test_level: integration
target_feature_ids:
  - UI001-01
  - UI001-02
requirement_ids:
  - REQ001
  - REQ002
use_case_ids:
  - UC001
  - UC002
data_flow_ids:
  - DF002
objective: 入力画面、正式なSharePointリスト、一覧画面が同じスキーマと業務キーで連携することを確認する
design_technique:
  - data_flow_testing
preconditions:
  environment: test
  user_role: applicant
  sharepoint_schema_check: passed
test_data:
  test_run_id: runtime-generated
  application_no: runtime-unique
steps:
  - 入力画面で申請を登録する
  - 一覧画面を開く
  - application_noで対象データを検索する
expected_results:
  sharepoint:
    - sp_requestsに対象レコードが存在する
  ui:
    - 一覧画面にapplication_noが表示される
    - 入力したタイトルと一覧表示値が一致する
postconditions:
  - test_run_idを条件に作成データを削除する
automation: automated
execution_profiles:
  - impact
  - full
coverage_targets:
  - DF002の全経路
evidence:
  - entry_screenshot
  - history_screenshot
  - sharepoint_record
  - playwright_trace
priority: critical
owner: application-team
status: approved
n_a_reasons: []
```
