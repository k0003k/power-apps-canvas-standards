# テストケース記載例

本ファイルは、[`../../docs/test-case-schema.md`](../../docs/test-case-schema.md) に従ったTest Specification / Test CaseのYAML記載例を示す。

Markdownファイル内の `yaml` コードブロックは記載例であり、Markdownファイル自体をYAMLとして扱うものではない。

実案件では、1 Test Specificationにつき1つの `.yaml` ファイルとして管理することを推奨する。

---

## 1. 機能単体テスト：MC/DC + 境界値分析

### 1.1 対象仕様

申請を受け付ける条件を以下とする。

```text
ユーザーが有効
AND 申請金額が100000円以下
AND 利用停止されていない
```

個別条件：

```text
C1: user.status == ACTIVE
C2: amount <= 100000
C3: user.blocked == false
```

候補となる `test_data × input_data` は9組合せあるが、MC/DCで各個別条件の独立影響を確認する4ケースを選択し、さらに境界値分析として上限直前の1ケースを追加する。

### 1.2 YAML

```yaml
test_spec_id: FUT-UI001-01-001
title: 申請受付可否判定
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
  - business-rules/request-acceptance-rule.md

objective: >
  申請受付可否の判定について、各個別条件が単独で判定結果へ影響すること、
  および申請金額の境界値が仕様どおりに判定されることを確認する

design_technique:
  - mcdc
  - boundary_value

preconditions:
  environment: test
  user_role: applicant
  sharepoint_schema_check: passed

decision:
  expression: >
    C1 AND C2 AND C3

  conditions:
    C1:
      description: ユーザーが有効である
      expression: user.status == ACTIVE
      sources:
        - test_data

    C2:
      description: 申請金額が100000円以下である
      expression: amount <= 100000
      sources:
        - input_data

    C3:
      description: 利用停止されていない
      expression: user.blocked == false
      sources:
        - test_data

test_data:
  TD001:
    description: 有効かつ利用停止されていないユーザー
    values:
      user_id: USER001
      user_status: ACTIVE
      blocked: false

  TD002:
    description: 無効かつ利用停止されていないユーザー
    values:
      user_id: USER002
      user_status: INACTIVE
      blocked: false

  TD003:
    description: 有効かつ利用停止されているユーザー
    values:
      user_id: USER003
      user_status: ACTIVE
      blocked: true

input_data:
  IN001:
    description: 申請金額の上限値
    values:
      application_no: runtime-unique
      title: テスト申請
      amount: 100000

  IN002:
    description: 申請金額の上限超過
    values:
      application_no: runtime-unique
      title: テスト申請
      amount: 100001

  IN003:
    description: 申請金額の上限直前
    values:
      application_no: runtime-unique
      title: テスト申請
      amount: 99999

steps:
  - 対象Caseのtest_dataをテスト開始前に準備する
  - 対象ユーザーでUI001-01を開く
  - 対象Caseのinput_dataを画面へ入力する
  - btn_submit_request_entryを押す
  - 対象Caseのexpected_resultを確認する

cases:
  - case_id: C001
    title: 全個別条件がtrueで申請を受け付ける
    test_data_refs:
      - TD001
    input_data_refs:
      - IN001
    condition_values:
      C1: true
      C2: true
      C3: true
    expected_result:
      result: accepted
      ui:
        - 申請完了が表示される
      sharepoint:
        - sp_requestsに1件登録される
        - amountが100000である
        - statusがSubmittedである
      state:
        - 同じapplication_noの重複登録がない
    coverage:
      mcdc:
        - baseline
      boundary_value:
        condition: C2
        point: at_boundary

  - case_id: C002
    title: ユーザーが無効の場合は申請を拒否する
    test_data_refs:
      - TD002
    input_data_refs:
      - IN001
    condition_values:
      C1: false
      C2: true
      C3: true
    expected_result:
      result: rejected
      ui:
        - ユーザーが無効である旨のエラーが表示される
      sharepoint:
        - sp_requestsに申請レコードが登録されない
      error:
        code: USER_INACTIVE
    coverage:
      mcdc:
        - C1

  - case_id: C003
    title: 申請金額が上限を超過した場合は申請を拒否する
    test_data_refs:
      - TD001
    input_data_refs:
      - IN002
    condition_values:
      C1: true
      C2: false
      C3: true
    expected_result:
      result: rejected
      ui:
        - 申請金額が上限を超過している旨のエラーが表示される
      sharepoint:
        - sp_requestsに申請レコードが登録されない
      error:
        code: AMOUNT_OVER_LIMIT
    coverage:
      mcdc:
        - C2
      boundary_value:
        condition: C2
        point: just_above

  - case_id: C004
    title: 利用停止ユーザーの場合は申請を拒否する
    test_data_refs:
      - TD003
    input_data_refs:
      - IN001
    condition_values:
      C1: true
      C2: true
      C3: false
    expected_result:
      result: rejected
      ui:
        - 利用停止中である旨のエラーが表示される
      sharepoint:
        - sp_requestsに申請レコードが登録されない
      error:
        code: USER_BLOCKED
    coverage:
      mcdc:
        - C3

  - case_id: C005
    title: 申請金額が上限直前の場合は申請を受け付ける
    test_data_refs:
      - TD001
    input_data_refs:
      - IN003
    condition_values:
      C1: true
      C2: true
      C3: true
    expected_result:
      result: accepted
      ui:
        - 申請完了が表示される
      sharepoint:
        - sp_requestsに1件登録される
        - amountが99999である
        - statusがSubmittedである
    coverage:
      boundary_value:
        condition: C2
        point: just_below

combination_coverage:
  strategy:
    - mcdc
    - boundary_value
  candidate_data_input_combination_count: 9
  theoretical_condition_combination_count: 8
  mcdc_case_count: 4
  additional_boundary_case_count: 1
  selected_case_count: 5
  omitted_candidate_count: 4
  exclusion_notes:
    - >
      MC/DCの独立影響確認に寄与せず、新たな仕様分岐または境界値も追加しない
      test_dataとinput_dataの組合せは省略する

mcdc_coverage:
  C1:
    status: covered
    pair:
      - C001
      - C002

  C2:
    status: covered
    pair:
      - C001
      - C003

  C3:
    status: covered
    pair:
      - C001
      - C004

postconditions:
  - test_run_idまたはapplication_noを条件にテストで作成した申請を削除する
  - テスト用ユーザーマスタを初期状態へ戻す

automation: automated

execution_profiles:
  - smoke
  - impact
  - full

coverage_targets:
  - 申請受付可否判定のC1
  - 申請受付可否判定のC2
  - 申請受付可否判定のC3
  - amount上限100000の境界値

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

### 1.3 MC/DCの読み方

MC/DCの確認ペアは以下となる。

| 個別条件 | 比較Case | 他の条件 | 判定結果 |
|---|---|---|---|
| C1 | `C001` ↔ `C002` | C2、C3は同じ | accepted ↔ rejected |
| C2 | `C001` ↔ `C003` | C1、C3は同じ | accepted ↔ rejected |
| C3 | `C001` ↔ `C004` | C1、C2は同じ | accepted ↔ rejected |

`C005`はMC/DCのためではなく、C2の境界値100000に対する「直前値」99999を追加確認するCaseである。

---

## 2. 機能間結合テスト

`DF001`：

```text
UI001-01 → sp_requests → UI001-02
```

```yaml
test_spec_id: FIT-DF001-001
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

objective: >
  入力画面、正式なSharePointリスト、履歴画面が同じスキーマと業務キーで
  申請情報を連携できることを確認する

design_technique:
  - data_flow_testing

preconditions:
  environment: test
  user_role: applicant
  sharepoint_schema_check: passed

test_data:
  TD001:
    description: 申請登録可能な有効ユーザー
    values:
      user_id: USER001
      user_status: ACTIVE

input_data:
  IN001:
    description: 正常な新規申請
    values:
      application_no: runtime-unique
      title: テスト申請
      amount: 10000

steps:
  - TD001のユーザーでUI001-01を開く
  - IN001の値を入力して申請を登録する
  - UI001-02を開く
  - application_noで対象申請を検索する

cases:
  - case_id: C001
    title: 登録した申請を同一業務キーで履歴画面から参照できる
    test_data_refs:
      - TD001
    input_data_refs:
      - IN001
    expected_result:
      result: displayed
      sharepoint:
        - sp_requestsに対象レコードが1件存在する
        - application_noが入力値と一致する
        - titleが入力値と一致する
        - amountが入力値と一致する
      ui:
        - UI001-02に同じapplication_noが表示される
        - titleとamountの表示値が保存値と一致する

postconditions:
  - application_noを条件に作成データを削除する

automation: automated

execution_profiles:
  - impact
  - full

coverage_targets:
  - DF001の入力から保存まで
  - DF001の保存から表示まで
  - 業務キーapplication_noの一貫性

evidence:
  - entry_screenshot
  - history_screenshot
  - sharepoint_record
  - playwright_trace

priority: critical
owner: application-team
status: approved
assumptions: []
n_a_reasons:
  - viewpoint: mcdc
    reason: DF001のデータフロー確認自体には複数個別条件から成る判定式がない
```

---

## 3. システムテスト

```yaml
test_spec_id: ST-UC001-001
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

objective: 申請者がユースケースに沿って新規申請を完了できることを確認する

design_technique:
  - use_case_testing

preconditions:
  environment: test
  user_role: applicant
  sharepoint_schema_check: passed
  external_interface_mode: test

test_data:
  TD001:
    description: 申請可能な有効ユーザー
    values:
      user_id: USER001
      user_status: ACTIVE
      blocked: false

input_data:
  IN001:
    description: 正常な新規申請
    values:
      application_no: runtime-unique
      title: テスト申請
      amount: 10000

steps:
  - TD001のユーザーでアプリへサインインする
  - UI001-01を開く
  - IN001の申請情報を入力する
  - 申請を登録する
  - 完了状態と後続処理を確認する

cases:
  - case_id: C001
    title: 正常な新規申請を完了できる
    test_data_refs:
      - TD001
    input_data_refs:
      - IN001
    expected_result:
      result: completed
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
  - 外部試験データを削除する
  - application_noを条件にSharePointデータを削除する

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
n_a_reasons:
  - viewpoint: mcdc
    reason: 本Test Specificationはユースケース正常シナリオを対象とし、個別の判定式は機能単体Test Specificationで確認する
```

---

## 4. Playwrightへ展開した場合の識別例

AIが上記YAMLからPlaywrightを生成する場合、各Caseを以下のように識別する。

```text
FUT-UI001-01-001-C001
FUT-UI001-01-001-C002
FUT-UI001-01-001-C003
FUT-UI001-01-001-C004
FUT-UI001-01-001-C005

FIT-DF001-001-C001

ST-UC001-001-C001
```

Playwrightコードをパラメータ化して1つのループで実装してもよいが、テスト結果、トレース、スクリーンショット等から実行ケースIDを特定できることを必須とする。
