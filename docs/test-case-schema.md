# Power Apps テストケース定義標準

作成日：2026-08-06  
改訂日：2026-08-07（Test Specification / Test Caseの二階層化、test_data / input_data / expected_result定義、MC/DCを反映）

## 0. 目的

本標準は、人間とAIが同じ意味でテスト仕様を作成、実行、変更、評価できるように、Test Specification、Test Case、テストデータ、入力データ、期待結果、およびYAML記載形式を定める。

テスト仕様で参照する機能ID、データフローID、ユースケースIDは、[`test-traceability-standard.md`](./test-traceability-standard.md)で定義されたIDを使用する。

テスト設計技法、MC/DC、境界値分析、期待結果の情報源等の方針は、[`testing-standard.md`](./testing-standard.md)に従う。

### 0.1 記載形式

本標準では、テスト仕様をYAMLで構造化して表現する。

本Markdownファイル内の `yaml` コードブロックは、YAML記載形式の定義・例を示すものであり、Markdownファイル自体をYAMLとして扱うものではない。

実案件でAIによる生成・更新・自動テスト実装へ利用する場合は、原則として1 Test Specificationにつき1つの `.yaml` ファイルとして管理することを推奨する。

例：

```text
test-specs/
├─ FUT-UI001-01-001.yaml
├─ FIT-DF001-001.yaml
└─ ST-UC001-001.yaml
```

---

## 1. Test SpecificationとTest Case

### 1.1 Test Specification

Test Specification（テスト仕様）は、同じ目的、対象機能、テストレベル、操作手順、テスト設計技法を共有する複数のTest Caseをまとめる単位とする。

従来の `FUT-UI001-01-001`、`FIT-DF001-001`、`ST-UC001-001` 形式のIDは、本標準では `test_spec_id` として扱う。

### 1.2 Test Case

Test Case（テストケース）は、1つの具体的な `test_data` と `input_data` の組合せ、およびその組合せに対する `expected_result` を表す。

1つのTest Specificationは、1件以上のTest Caseを持つ。

```text
FUT-UI001-01-001
├─ C001
├─ C002
├─ C003
└─ C004
```

実行結果やPlaywrightのテスト名等で一意に識別する場合は、以下を実行ケースIDとして使用する。

```text
<test_spec_id>-<case_id>
```

例：

```text
FUT-UI001-01-001-C001
FIT-DF001-001-C001
ST-UC001-001-C001
```

YAML内で `execution_case_id` を重複保持することは必須とせず、`test_spec_id` と `case_id` から生成してよい。

---

## 2. テストレベル

| 値 | 意味 |
|---|---|
| `functional_unit` | 機能単体テスト |
| `integration` | 機能間結合テスト |
| `system` | システムテスト |

テストレベルは実施工程ではなく、テストが保証する対象と目的を示す属性である。

---

## 3. Test Specification ID

形式：

```text
<LEVEL>-<TARGET>-<SEQUENCE>
```

| 種別 | 接頭語 | 対象ID | 例 |
|---|---|---|---|
| 機能単体 | `FUT` | 機能ID | `FUT-UI001-01-001` |
| 機能間結合 | `FIT` | データフローID | `FIT-DF001-001` |
| システム | `ST` | ユースケースID | `ST-UC001-001` |

同じ対象に複数のTest Specificationがある場合は、末尾の連番を増やす。

IDは一度発行した後に再利用しない。

### 3.1 Case ID

Test Specification内のTest Caseは、以下の形式とする。

```text
C001
C002
C003
```

Case IDはTest Specification内で一意とし、削除したCase IDを別の意味で再利用しない。

---

## 4. 用語定義

### 4.1 `preconditions`

テストを実行可能にする環境、認証、権限、設定、接続状態等の前提条件を表す。

例：

- 試験環境である
- 申請者ロールのテストアカウントでサインインできる
- 対象SharePointリストのスキーマ確認が完了している
- 外部インターフェースが試験モードで利用可能である

業務データとして存在しているユーザーマスタ、既存トランザクション、ステータス等は原則として `test_data` に記載する。

### 4.2 `test_data`

Test Caseの実行開始時点で存在するデータまたは状態を表す。

テスト実行前のセットアップ処理によって作成する場合も、テスト対象への入力操作を開始する時点ですでに存在していれば `test_data` とする。

例：

- 有効なユーザーマスタ
- 利用停止中のユーザーマスタ
- ステータスがSubmittedの既存申請
- 特定の残高を持つ既存トランザクション
- テスト対象が参照するマスタ・設定データ

### 4.3 `input_data`

Test Caseの実行時にテスト対象機能へ与える入力を表す。

例：

- 画面入力値
- インターフェースの入力ファイル
- APIリクエスト
- メッセージ
- 関数・UDFの引数
- バッチ起動時のパラメーター

### 4.4 `expected_result`

1つのTest Caseにおける `test_data` と `input_data` の組合せに対して期待される結果を表す。

例：

- 画面表示
- エラーメッセージ
- SharePoint登録・更新結果
- API応答
- 関数の返却値
- 状態遷移
- 外部システムへの送信内容
- 二重登録が発生しないこと

期待結果はTest Specification全体ではなく、原則として各 `cases` 配下へ記載する。

---

## 5. 個別条件とMC/DC

### 5.1 個別条件

本標準における「個別条件」とは、MC/DCにおける原子条件（Atomic Condition）を指し、AND / OR等でそれ以上分解しない、真または偽で評価できる1つの条件をいう。

例：

```text
ユーザーが有効
AND 申請金額が100000円以下
AND 利用停止されていない
```

は以下の3個の個別条件に分解する。

```text
C1: user.status == ACTIVE
C2: amount <= 100000
C3: user.blocked == false
```

### 5.2 `decision`

複数の個別条件を組み合わせた判定式へMC/DCを適用する場合は、`decision` に判定式と個別条件を定義する。

```yaml
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
```

個別条件が `test_data` と `input_data` の両方を比較して決まる場合は、`sources` に両方を記載してよい。

### 5.3 `condition_values`

MC/DC対象の各Test Caseには、個別条件の真偽を `condition_values` として明示する。

```yaml
condition_values:
  C1: true
  C2: false
  C3: true
```

AIは、記載された真偽と実際の `test_data` / `input_data` が矛盾していないことを確認する。

### 5.4 `mcdc_coverage`

各個別条件について、その条件だけを変化させたときに判定結果が変化するTest Caseの組を記録する。

```yaml
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
```

業務上または技術上、独立影響を示す組合せを成立させられない場合は、推測でMC/DC達成とせず、以下のように理由を記録する。

```yaml
mcdc_coverage:
  C4:
    status: not_achievable
    reason: C4がfalseの場合は仕様上C2を評価する状態へ到達できない
```

---

## 6. テスト設計技法

対象仕様に応じて以下を使用する。

- 同値分割：`equivalence_partitioning`
- 境界値分析：`boundary_value`
- MC/DC：`mcdc`
- デシジョンテーブル：`decision_table`
- 状態遷移テスト：`state_transition`
- エラー推測：`error_guessing`
- 処理経路テスト：`path_testing`
- データフローテスト：`data_flow_testing`
- ユースケーステスト：`use_case_testing`

複数の技法を同時に適用してよい。

```yaml
design_technique:
  - mcdc
  - boundary_value
```

複数の個別条件から構成される判定式の組合せ削減には、原則としてMC/DCを使用する。

数値、日付、文字数等の境界を持つ個別条件には、MC/DCに加えて境界値分析を適用する。

MC/DCは1つの判定式に含まれる個別条件の独立影響を確認する技法である。互いに無関係なデータ分類の直積を減らす目的だけで、判定式が存在しない組合せへMC/DCを形式的に適用してはならない。

---

## 7. 組合せ管理

### 7.1 基本方針

Test Caseは、原則として以下の関係で定義する。

```text
test_data × input_data
        ↓
selected case
        ↓
expected_result
```

候補となる `test_data × input_data` の全組合せを機械的にすべて実行するのではなく、仕様上の判定式、MC/DC、境界値、同値クラス、エラー条件等から必要なTest Caseを選択する。

### 7.2 `combination_coverage`

複数パターンからTest Caseを選択した場合は、削減根拠を `combination_coverage` に記録する。

```yaml
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
    - MC/DCの独立影響確認に寄与せず、新たな境界値・仕様分岐も追加しない組合せは省略する
```

`candidate_data_input_combination_count` は、テスト設計上候補とした `test_data` と `input_data` の直積数を表す。

`theoretical_condition_combination_count` は、MC/DC対象となる個別条件がN個ある場合の理論上の真偽組合せ数を表す。すべての組合せが業務上成立するとは限らない。

MC/DCで選択されなかった全組合せを1件ずつ列挙することは必須としない。

ただし、業務上禁止されている組合せ、成立しない組合せ、リスク上明示すべき除外条件は `excluded_combinations` に記録してよい。

---

## 8. 必須項目

### 8.1 Test Specification必須項目

| 項目 | 必須 | 内容 |
|---|---:|---|
| `test_spec_id` | ○ | 一意なTest Specification ID |
| `title` | ○ | 検証内容を表す名称 |
| `test_level` | ○ | `functional_unit` / `integration` / `system` |
| `target_feature_ids` | ○ | 機能一覧で定義した対象機能ID |
| `requirement_ids` | ○ | 対応する要件ID |
| `use_case_ids` | 条件付 | システムテストまたは関連時 |
| `data_flow_ids` | 条件付 | 機能間結合テストまたは関連時 |
| `specification_refs` | ○ | 期待結果の根拠となる承認済み仕様 |
| `objective` | ○ | 何を保証するか |
| `design_technique` | ○ | 使用したテスト設計技法 |
| `preconditions` | ○ | 環境、認証、権限、設定等 |
| `decision` | 条件付 | MC/DC対象の判定式と個別条件 |
| `test_data` | ○ | テスト開始時に存在するデータ。不要なら `{}` |
| `input_data` | ○ | テスト対象へ与える入力。不要なら `{}` |
| `steps` | ○ | 人間またはAIが実行可能な共通操作 |
| `cases` | ○ | 具体的なTest Case。1件以上 |
| `combination_coverage` | 条件付 | 複数パターンからケースを選択した場合 |
| `mcdc_coverage` | 条件付 | `mcdc`適用時 |
| `postconditions` | ○ | 後処理、削除、復元 |
| `automation` | ○ | `automated` / `manual` / `hybrid` |
| `execution_profiles` | ○ | `smoke`、`impact`、`full`等 |
| `coverage_targets` | ○ | 対象分岐、処理経路、仕様項目 |
| `evidence` | ○ | 保存する証跡 |
| `priority` | ○ | `critical` / `high` / `medium` / `low` |
| `owner` | ○ | 責任者またはチーム |
| `status` | ○ | `draft` / `approved` / `active` / `suspended` / `retired` |
| `assumptions` | ○ | AIまたは作成者が置いた前提。ない場合は空配列 |
| `n_a_reasons` | 条件付 | 適用しない観点と理由 |

### 8.2 Test Case必須項目

| 項目 | 必須 | 内容 |
|---|---:|---|
| `case_id` | ○ | Test Specification内で一意なCase ID |
| `title` | ○ | パターンを識別できる名称 |
| `test_data_refs` | ○ | 使用する `test_data` ID。不要なら空配列 |
| `input_data_refs` | ○ | 使用する `input_data` ID。不要なら空配列 |
| `condition_values` | 条件付 | MC/DC対象の場合の個別条件真偽 |
| `expected_result` | ○ | 当該組合せに対する期待結果 |
| `coverage` | 条件付 | 境界値、異常系、MC/DC等で当該ケースが担う観点 |

---

## 9. `test_data` と `input_data` の定義形式

複数のTest Caseから再利用できるように、Test Specification内でIDを付けて定義する。

```yaml
test_data:
  TD001:
    description: 有効かつ利用停止されていないユーザー
    values:
      user_id: USER001
      user_status: ACTIVE
      blocked: false

  TD002:
    description: 無効ユーザー
    values:
      user_id: USER002
      user_status: INACTIVE
      blocked: false

input_data:
  IN001:
    description: 申請金額の上限値
    values:
      amount: 100000

  IN002:
    description: 申請金額の上限超過
    values:
      amount: 100001
```

IDはTest Specification内で一意とする。

目安として以下の接頭語を使用する。

```text
TD001  test_data
IN001  input_data
```

---

## 10. `cases` と `expected_result`

```yaml
cases:
  - case_id: C001
    title: 有効ユーザー・上限値
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
```

期待結果は、必要に応じて以下のような観点へ分けて記載してよい。

```yaml
expected_result:
  result: accepted
  ui:
    - 完了メッセージが表示される
  sharepoint:
    - sp_requestsに1件登録される
    - status列がSubmittedである
  integration:
    - IF001への送信が成功する
  state:
    - 二重登録が発生しない
  return_value:
    value: true
  error:
    code: null
```

期待結果の根拠は `specification_refs` へ記録し、[`testing-standard.md`](./testing-standard.md)の情報源優先順位に従う。

実装コードと上位仕様が異なる場合は、実装コードを期待結果の根拠として優先しない。

---

## 11. 共通手順とケース固有値

`steps` には、Test Specification内の各Test Caseで共通する操作を記載する。

```yaml
steps:
  - 対象Caseのtest_dataが存在することを確認する
  - UI001-01を開く
  - 対象Caseのinput_dataを入力する
  - 登録ボタンを押す
  - 対象Caseのexpected_resultを確認する
```

特定Caseだけ追加操作が必要な場合は、`case_steps` をTest Caseへ追加してよい。

```yaml
cases:
  - case_id: C010
    title: 再実行
    test_data_refs:
      - TD010
    input_data_refs:
      - IN010
    case_steps:
      - 同一入力で登録操作を2回実行する
    expected_result:
      state:
        - 二重登録が発生しない
```

---

## 12. 実行プロファイル

| 値 | 意味 |
|---|---|
| `smoke` | PRまたはデプロイごとに行う短時間の重要テスト |
| `impact` | 変更影響に基づき選択するテスト |
| `full` | 夜間、定期実行、リリース前の全体テスト |
| `manual_release` | リリース前に人間が実施するテスト |

1つのTest Specificationに複数の実行プロファイルを設定してよい。

必要に応じて、AIは実行プロファイルごとに対象Caseを選択してよい。ただし選択理由を追跡可能にする。

---

## 13. 自動化区分

| 値 | 意味 |
|---|---|
| `automated` | 全手順と判定を自動化する |
| `manual` | 人間による実施・判定が必要 |
| `hybrid` | 自動実行と人間の判定を組み合わせる |

自動化しない場合は、理由、実施タイミング、実施責任者を記録する。

---

## 14. Playwrightへの展開

YAMLはPlaywrightが直接実行する形式ではない。

AIがYAMLのTest Specificationを読み取り、各Test CaseをPlaywrightの実行可能なTypeScript / JavaScriptへ変換する。

原則として各Test Caseの実行結果を個別に識別できるようにする。

例：

```text
YAML
FUT-UI001-01-001
├─ C001
├─ C002
└─ C003

↓ AIがPlaywrightへ変換

FUT-UI001-01-001-C001
FUT-UI001-01-001-C002
FUT-UI001-01-001-C003
```

AIがループやパラメータ化テストとして実装することは許容するが、テスト結果・証跡から `test_spec_id` と `case_id` を特定できることを必須とする。

UIセレクター、待機、認証等は [`testing-standard.md`](./testing-standard.md) のUI自動テスト標準に従う。

---

## 15. テンプレート

以下は、判定式を持たない基本的なTest Specificationの最小例である。

```yaml
test_spec_id: FUT-UI001-01-001
title: 必須項目を入力して申請を登録できる
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

objective: 申請入力画面がSharePointへの登録責務を完結できることを確認する

design_technique:
  - equivalence_partitioning

preconditions:
  environment: test
  user_role: applicant
  sharepoint_schema_check: passed

test_data: {}

input_data:
  IN001:
    description: 正常な新規申請
    values:
      application_no: runtime-unique
      title: テスト申請

steps:
  - UI001-01を開く
  - 対象Caseのinput_dataを入力する
  - btn_submit_request_entryを押す
  - 対象Caseのexpected_resultを確認する

cases:
  - case_id: C001
    title: 正常な新規申請を登録できる
    test_data_refs: []
    input_data_refs:
      - IN001
    expected_result:
      result: accepted
      ui:
        - 完了メッセージが表示される
      sharepoint:
        - sp_requestsに1件登録される
        - application_noが入力値と一致する
        - titleが入力値と一致する

postconditions:
  - application_noを条件に作成データを削除する

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

MC/DCと境界値分析を含む完成例は、[`../samples/test-design/test-case-sample.md`](../samples/test-design/test-case-sample.md)を参照する。
