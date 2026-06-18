# Power Apps Canvas Standards

Power Apps キャンバスアプリ開発における設計標準、実装標準、およびベストプラクティスを管理するリポジトリです。

本リポジトリは、人間の開発者だけでなく、GitHub Copilot、ChatGPT、その他の AI コーディング支援ツールが参照することを前提としています。

---

# 目的

以下を実現することを目的とします。

- 可読性の向上
- 保守性の向上
- パフォーマンスの向上
- 開発品質の均一化
- AI支援開発における出力品質の向上

---

# 対象

本標準は以下を対象とします。

- Power Apps Canvas Apps
- Power Fx
- SharePoint データソース
- Dataverse データソース
- Power Platform ソリューション開発

---

# AIへの指示

本リポジトリ内の標準は推奨事項ではなく、特段の指示がない限り遵守すべき開発標準とする。

AIがコード、設計、レビュー、リファクタリングを実施する場合は、本リポジトリ内の標準を優先的に適用すること。

特に以下を重視する。

- 命名規則
- レイアウト標準
- User Defined Function利用方針
- Named Formula利用方針
- パフォーマンスガイドライン

---

# 標準一覧
Before generating code, review:
- docs/naming-conventions.md
- docs/power_apps_canvas_app_dev_standard.md

## 命名規則

- docs/naming-conventions.md

## 開発標準

- docs/power_apps_canvas_app_dev_standard.md

## SharePoint利用標準

- 未定義

## パフォーマンス標準

- 未定義

---

# 設計思想

本標準は以下の思想に基づいて作成する。

- シンプルであること
- 保守しやすいこと
- AIが理解しやすいこと
- Power Platform標準機能を優先すること
- 過度な独自実装を避けること

---

# 変更管理

標準は固定ではない。

実案件で得られた知見や問題点を継続的に反映し、改善していくものとする。
