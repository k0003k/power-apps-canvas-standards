# テストケース記載例

## 1. 画面機能単体テスト

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

specification_refs:
  - requirements/REQ001.md
  - designs/UI001-01.md
  - schemas/SCHEMA-SP-REQUESTS.md

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
  - UI001-01を開く
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
assumptions: []
n_a_reasons: []
```

---

## 2. 機能間結合テスト

```yaml
test_case_id: FIT-DF001-001
title: 入力画面で登録した申請が履歴画面へ表示される
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
  - DF001

specification_refs:
  - requirements/REQ001.md
  - requirements/REQ002.md
  - designs/DF001.md
  - schemas/SCHEMA-SP-REQUESTS.md

objective: 入力画面、正式なSharePointリスト、履歴画面が同じスキーマと業務キーで連携することを確認する

design_technique:
  - data_flow_testing

preconditions:
  environment: test
  user_role: applicant
  sharepoint_schema_check: passed

test_data:
  test_run_id: runtime-generated
  application_no: runtime-unique
  title: テスト申請

steps:
  - UI001-01で申請を登録する
  - UI001-02を開く
  - application_noで対象データを検索する

expected_results:
  sharepoint:
    - sp_requestsに対象レコードが存在する
    - application_noが入力値と一致する
    - titleが入力値と一致する
  ui:
    - UI001-02にapplication_noが表示される
    - 入力したtitleと一覧表示値が一致する

postconditions:
  - test_run_idを条件に作成データを削除する

automation: automated

execution_profiles:
  - impact
  - full

coverage_targets:
  - DF001の全経路

evidence:
  - entry_screenshot
  - history_screenshot
  - sharepoint_record
  - playwright_trace

priority: critical
owner: application-team
status: approved
assumptions: []
n_a_reasons: []
```

---

## 3. システムテスト

```yaml
test_case_id: ST-UC001-001
title: 申請者が新規申請を完了できる
test_level: system

target_feature_ids:
  - UI001-01
  - CM001
  - BT001
  - IF001

requirement_ids:
  - REQ001

use_case_ids:
  - UC001

data_flow_ids:
  - DF003
  - DF004

specification_refs:
  - requirements/REQ001.md
  - use-cases/UC001.md
  - designs/DF003.md
  - designs/DF004.md

objective: 申請者がユースケースに沿って申請登録を完了できることを確認する

design_technique:
  - use_case_testing

preconditions:
  environment: test
  user_role: applicant
  sharepoint_schema_check: passed
  external_interface_mode: test

test_data:
  test_run_id: runtime-generated
  application_no: runtime-unique
  title: テスト申請

steps:
  - アプリへサインインする
  - 申請情報を入力する
  - 申請を登録する
  - 完了状態を確認する

expected_results:
  ui:
    - 申請完了が表示される
  sharepoint:
    - 申請レコードがSubmitted状態で登録される
  integration:
    - IF001へ正しい申請メッセージが送信される
    - 外部受付結果とcorrelation_idが記録される
  state:
    - 同じapplication_noの重複登録がない

postconditions:
  - 外部テストデータを削除する
  - test_run_idを条件にSharePointデータを削除する

automation: automated

execution_profiles:
  - smoke
  - impact
  - full

coverage_targets:
  - UC001の主要正常シナリオ
  - DF003の申請登録経路
  - DF004の外部送信経路

evidence:
  - screenshot
  - playwright_trace
  - sharepoint_record
  - interface_log

priority: critical
owner: application-team
status: approved
assumptions: []
n_a_reasons: []
```
