# Power Apps Canvas Standards ドキュメント構成

改訂日：2026-08-06

本ディレクトリの標準は、人間の開発者だけでなく、GitHub Copilot、Claude Code、その他のAI開発・テスト支援ツールが参照することを前提とする。

---

## 1. 参照順序

AIおよび開発者は、コード生成、レビュー、テスト設計、変更影響分析を行う前に、作業内容に関係する以下の文書を確認する。

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
- テストレベルと実施方法は`testing-standard.md`を優先する
- 成果物間のID参照は`test-traceability-standard.md`を優先する
- テストケースの項目・値・形式は`test-case-schema.md`を優先する

---

## 3. 各文書の責務

| 文書 | 責務 |
|---|---|
| `naming-conventions.md` | コントロール、変数、データソース、UDF等の命名 |
| `power_apps_canvas_app_dev_standard.md` | レイアウト、UI/UX、データ操作、状態管理、Power Fx、テスト容易性 |
| `testing-standard.md` | 機能単体・機能間結合・システムテスト、AI自動テスト、環境、証跡、回帰テスト |
| `test-traceability-standard.md` | 機能一覧、データフロー、ビジネスファンクションチャート、ユースケース依存、変更影響分析 |
| `test-case-schema.md` | テストケースの必須項目、ID、状態、分類、記載形式 |

---

## 4. テスト設計成果物の作成順序

以下を原則とする。

```text
機能一覧
  ↓
データフロー対応表
  ↓
ビジネスファンクションチャート
  ↓
ユースケース間依存関係表
  ↓
テストケース
```

後続成果物は、前段の成果物で定義されたIDを参照する。

実装資産が機能一覧に存在しない場合は、AIが推測で既存IDへ割り当てず、未登録機能として報告する。

---

## 5. サンプル

### 実装サンプル

- [`../samples/apps/`](../samples/apps/)

### テスト設計サンプル

- [`../samples/test-design/feature-list-sample.md`](../samples/test-design/feature-list-sample.md)
- [`../samples/test-design/data-flow-test-matrix-sample.md`](../samples/test-design/data-flow-test-matrix-sample.md)
- [`../samples/test-design/business-function-chart-sample.md`](../samples/test-design/business-function-chart-sample.md)
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
