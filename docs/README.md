# Power Apps Canvas Standards ドキュメント構成

改訂日：2026-08-06

本ディレクトリの標準は、人間の開発者だけでなく、GitHub Copilot、Claude Code、その他のAI開発・テスト支援ツールが参照することを前提とする。

## 参照優先順位

AIおよび開発者は、コード生成、レビュー、テスト設計、変更影響分析を行う前に、以下を順に確認する。

1. [`naming-conventions.md`](./naming-conventions.md)
2. [`power_apps_canvas_app_dev_standard.md`](./power_apps_canvas_app_dev_standard.md)
3. [`testing-standard.md`](./testing-standard.md)
4. [`test-traceability-standard.md`](./test-traceability-standard.md)
5. [`test-case-schema.md`](./test-case-schema.md)

競合する記載がある場合は、対象領域に特化した文書を優先する。

## 各文書の責務

| 文書 | 責務 |
|---|---|
| `naming-conventions.md` | コントロール、変数、データソース、UDF等の命名 |
| `power_apps_canvas_app_dev_standard.md` | レイアウト、UI/UX、データ操作、状態管理、Power Fx、テスト容易性 |
| `testing-standard.md` | 機能単体・機能間結合・システムテスト、AI自動テスト、環境、証跡、回帰テスト |
| `test-traceability-standard.md` | データフロー、ビジネスファンクションチャート、ユースケース依存、変更影響分析 |
| `test-case-schema.md` | テストケースの必須項目、ID、状態、分類、記載形式 |

## サンプル

- [`../samples/test-case-sample.md`](../samples/test-case-sample.md)
- [`../samples/data-flow-test-matrix-sample.md`](../samples/data-flow-test-matrix-sample.md)
- [`../samples/business-function-chart-sample.md`](../samples/business-function-chart-sample.md)

## 適用方針

本標準は推奨事項ではなく、特段の指示がない限り遵守すべき標準とする。

標準から外れる場合は、以下を記録する。

- 対象箇所
- 逸脱理由
- 代替策
- 影響範囲
- 承認者
- 見直し期限
