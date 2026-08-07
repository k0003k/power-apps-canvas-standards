# Power Apps Canvas Standards

Power Apps キャンバスアプリ開発における設計標準、実装標準、テスト標準およびサンプルを管理するリポジトリです。

本リポジトリは、人間の開発者だけでなく、GitHub Copilot、Claude Code、ChatGPT、その他のAI開発・テスト支援ツールが参照することを前提とします。

---

## 目的

以下を実現することを目的とします。

- 可読性と保守性の向上
- 開発品質の均一化
- AI支援開発における出力品質の向上
- 業務要件からシステム機能・テストまでのトレーサビリティ確保
- AIによるテスト仕様作成と自動テスト
- 変更影響とリグレッションテストの追跡
- SharePointを利用する機能間のデータ整合性確保

---

## 対象

本標準の主な対象は以下です。

- Power Apps Canvas Apps
- Power Fx
- SharePointデータソース
- 関連するPower Automate
- 関連するAzure Functions
- レポート
- データ移行アプリ
- 共通User Defined Functionおよび共通メソッド

Dataverse等、個別の標準が未定義のデータソースについては、本標準をそのまま適用できるとは限りません。

---

## 標準一覧

AIおよび開発者は、作業内容に応じて以下を確認します。

1. [命名規則](docs/naming-conventions.md)
2. [キャンバスアプリ開発標準](docs/power_apps_canvas_app_dev_standard.md)
3. [AI自動テスト標準](docs/testing-standard.md)
4. [テスト・トレーサビリティ標準](docs/test-traceability-standard.md)
5. [テスト仕様・テストケース定義標準](docs/test-case-schema.md)

文書構成と競合時の優先ルールは、[docs/README.md](docs/README.md)を参照してください。

---

## 設計・テスト成果物の作成順序

新規設計または業務要件からのテスト設計では、原則として以下の順に成果物を作成します。

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

### BFCの位置づけ

BFC（Business Function Chart）は、業務要件を起点として、

- 誰が
- いつ
- 何を行うか
- その業務に対してシステムが何を担うか

を整理し、必要なシステム機能候補を洗い出すための上流成果物です。

BFC初版では、共通化、統合、分割、実装配置等の詳細設計を確定する必要はありません。機能IDも未採番で構いません。

BFCで洗い出した機能候補を機能一覧で整理し、正式な機能単位、責務および機能IDを確定した後、BFCへ正式な機能IDを紐付けます。

機能設計の結果、機能の統合、分割、共通化または実装方式の変更が発生した場合は、機能一覧を更新し、その結果をBFCへフィードバックします。

### Test SpecificationとTest Case

Test Specificationは、テスト対象、目的、設計技法、テストデータ等を定義する単位であり、その配下に1件以上の具体的なTest Caseを持ちます。

```text
Test Specification
  ├─ Test Case C001
  ├─ Test Case C002
  └─ Test Case C003
```

---

## 機能ID

| 種別 | ID例 |
|---|---|
| アプリ | `UI001` |
| Screen | `UI001-01` |
| インターフェース | `IF001` |
| バッチ | `BT001` |
| レポート | `RP001` |
| 共通 | `CM001` |
| データ移行 | `MG001` |

IFとBTは実装技術ではなく責務で分類します。

- 外部システムとの送受信を主目的とする機能：`IF`
- 定期・一括・非同期処理を主目的とする機能：`BT`

---

## サンプル

```text
samples/
├─ apps/          実装サンプル
└─ test-design/   設計・テスト設計サンプル
```

詳細は[samples/README.md](samples/README.md)を参照してください。

---

## AIへの指示

本リポジトリ内の標準は、特段の指示がない限り遵守すべき標準です。

AIがコード生成、設計、レビュー、テスト設計、変更影響分析を行う場合は、[.github/copilot-instructions.md](.github/copilot-instructions.md)および`docs/`配下の関連標準に従ってください。

実装コードと承認済み仕様が異なる場合は、実装コードを正解としてテストを変更せず、仕様不一致として報告します。

---

## 設計思想

- シンプルであること
- 保守しやすいこと
- AIと人間が同じ意味で理解できること
- 業務要件から実装・テストまで追跡できること
- BFCでは業務上必要な振る舞いを漏れなく整理し、実装詳細を早期に固定しすぎないこと
- Power Platform標準機能を優先すること
- 過度な独自実装を避けること
- 業務上の目的とデータの流れを追跡できること

---

## 変更管理

標準は固定ではありません。

実案件で得られた知見や問題点を継続的に反映し、改善します。

業務要件、BFC、ユースケース、機能、スキーマ、データフロー、Test SpecificationまたはTest Caseを変更した場合は、関連するトレーサビリティ成果物を同じPull Requestで更新します。
