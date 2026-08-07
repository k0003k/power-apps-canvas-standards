# Power Apps Canvas Standards ドキュメント構成

改訂日：2026-08-07

本ディレクトリの標準は、人間の開発者だけでなく、GitHub Copilot、Claude Code、その他のAI開発・テスト支援ツールが参照することを前提とする。

---

## 1. 参照順序

AIおよび開発者は、コード生成、レビュー、設計、テスト設計、変更影響分析を行う前に、作業内容に関係する以下の文書を確認する。

1. [`naming-conventions.md`](./naming-conventions.md)
2. [`power_apps_canvas_app_dev_standard.md`](./power_apps_canvas_app_dev_standard.md)
3. [`testing-standard.md`](./testing-standard.md)
4. [`test-traceability-standard.md`](./test-traceability-standard.md)
5. [`test-case-schema.md`](./test-case-schema.md)

---

## 2. 競合時の優先ルール

文書間で記載が競合する場合は、以下の順で判断する。

1. 対象領域に特化した文書を、一般的な文書より優先する
2. 同じ対象領域の文書間で競合する場合は、上記の参照順序が高い文書を優先する
3. 期待結果や業務動作が変わる可能性がある場合は、推測で確定せず競合として報告する

例：

- 命名に関する詳細は`naming-conventions.md`を優先する
- テストレベル、テスト設計技法、MC/DC、境界値分析および実施方法は`testing-standard.md`を優先する
- BFC、ユースケース依存、機能一覧、データフローおよび成果物間のトレーサビリティは`test-traceability-standard.md`を優先する
- Test SpecificationおよびTest Caseの項目・値・YAML記載形式は`test-case-schema.md`を優先する

---

## 3. 各文書の責務

| 文書 | 責務 |
|---|---|
| `naming-conventions.md` | コントロール、変数、データソース、UDF等の命名 |
| `power_apps_canvas_app_dev_standard.md` | レイアウト、UI/UX、データ操作、状態管理、Power Fx、テスト容易性 |
| `testing-standard.md` | 機能単体・機能間結合・システムテスト、テスト設計技法、MC/DC、境界値分析、AI自動テスト、環境、証跡、回帰テスト |
| `test-traceability-standard.md` | BFC、ユースケース依存、機能一覧、データフロー、Test Specificationへのトレーサビリティ、変更影響分析 |
| `test-case-schema.md` | Test Specification / Test Caseの必須項目、ID、`test_data`、`input_data`、`expected_result`、YAML記載形式 |

---

## 4. 設計・テスト成果物の作成順序

新規設計または業務要件からのテスト設計では、以下を原則とする。

```text
業務要件
  ↓
BFC 初版
  ↓
ユースケース間依存関係表
  ↓
機能一覧
  ↓
BFC 更新（正式な機能IDを紐付け）
  ↓
データフロー対応表
  ↓
Test Specification
  └─ Test Case
```

### 4.1 BFC初版

BFC初版では、業務要件から以下を整理する。

- 業務分類
- ユースケース
- 主語（アクター）
- タイミング
- 業務要件
- 業務を成立させるために必要なシステム機能候補

この段階では、機能の共通化、統合、分割、実装配置等を確定する必要はない。

正式な機能IDも未採番でよい。

### 4.2 機能一覧

BFC初版で洗い出した機能候補を、設計・実装・テスト可能な正式な機能単位として整理する。

この段階で、必要に応じて以下を行う。

- 機能候補の統合
- 機能候補の分割
- 共通機能化
- UI / IF / BT / RP / CM / MGへの分類
- 実装責務の整理
- 正式な機能IDの採番

### 4.3 BFC更新

機能一覧で確定した正式な機能IDをBFCへ紐付ける。

機能設計の結果、機能単位や責務が変更された場合は、機能一覧を更新したうえでBFCへ変更結果をフィードバックする。

### 4.4 後続成果物

データフロー対応表およびTest Specificationは、正式な機能IDが確定した状態を前提に作成する。

Test Specificationは1件以上のTest Caseを持つ。

実装資産が機能一覧に存在しない場合は、AIが推測で既存IDへ割り当てず、未登録機能として報告する。

---

## 5. サンプル

### 実装サンプル

- [`../samples/apps/`](../samples/apps/)

### 設計・テスト設計サンプル

- [`../samples/test-design/business-function-chart-sample.md`](../samples/test-design/business-function-chart-sample.md)
- [`../samples/test-design/feature-list-sample.md`](../samples/test-design/feature-list-sample.md)
- [`../samples/test-design/data-flow-test-matrix-sample.md`](../samples/test-design/data-flow-test-matrix-sample.md)
- [`../samples/test-design/test-case-sample.md`](../samples/test-design/test-case-sample.md)

---

## 6. 適用方針

本標準は推奨事項ではなく、特段の指示がない限り遵守すべき標準とする。

標準から外れる場合は、以下を記録する。

- 対象箇所
- 逸脱理由
- 代替策
- 影響範囲
- 承認者
- 見直し期限
