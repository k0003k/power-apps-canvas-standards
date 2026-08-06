# Power Apps Canvas Standards 構造修正ガイド

作成日：2026-08-06

## 1. 目的

本ガイドは、現在のリポジトリ構造を、AIが標準・機能一覧・データフロー・ユースケース・テストケースを一貫して参照できる構造へ修正するための差分を示す。

主な修正点は以下とする。

- ルートREADMEから全標準へ到達できるようにする
- Copilotの参照対象へテスト関連標準を追加する
- 機能IDを `UI / IF / BT / RP / CM / MG` へ統一する
- 要件、ユースケース、データフローのIDを `REQ / UC / DF` へ統一する
- IFとBTを実装技術ではなく責務で分類する
- 実装サンプルとテスト設計サンプルを別階層に分ける
- `samples/REDME.md` のスペルを修正する

---

## 2. 修正前の構造

```text
power-apps-canvas-standards/
├─ README.md
├─ .github/
│  └─ copilot-instructions.md
├─ docs/
│  ├─ README.md
│  ├─ naming-conventions.md
│  ├─ power_apps_canvas_app_dev_standard.md
│  ├─ testing-standard.md
│  ├─ test-traceability-standard.md
│  └─ test-case-schema.md
└─ samples/
   ├─ REDME.md
   └─ license_card_mobile_capture/
```

修正前の主な問題：

- ルートREADMEが新しいテスト標準を案内していない
- Copilot指示が命名規則と開発標準しか参照しない
- `test-traceability-standard.md` が旧ID体系を使用している
- `testing-standard.md` がIFとBTを同じ機能種別としている
- `docs/README.md` が存在しないサンプルへリンクしている
- 実装サンプルとテスト設計サンプルの責務が混在する
- `REDME.md` がスペルミスである

---

## 3. 修正後の構造

```text
power-apps-canvas-standards/
├─ README.md
├─ .github/
│  └─ copilot-instructions.md
├─ docs/
│  ├─ README.md
│  ├─ naming-conventions.md
│  ├─ power_apps_canvas_app_dev_standard.md
│  ├─ testing-standard.md
│  ├─ test-traceability-standard.md
│  └─ test-case-schema.md
└─ samples/
   ├─ README.md
   ├─ apps/
   │  ├─ README.md
   │  └─ license_card_mobile_capture/
   └─ test-design/
      ├─ README.md
      ├─ feature-list-sample.md
      ├─ data-flow-test-matrix-sample.md
      ├─ business-function-chart-sample.md
      └─ test-case-sample.md
```

---

## 4. ファイル操作

### 4.1 置換するファイル

```text
README.md
.github/copilot-instructions.md
docs/README.md
docs/testing-standard.md
docs/test-traceability-standard.md
docs/test-case-schema.md
```

### 4.2 追加するファイル

```text
samples/apps/README.md
samples/test-design/README.md
samples/test-design/feature-list-sample.md
samples/test-design/data-flow-test-matrix-sample.md
samples/test-design/business-function-chart-sample.md
samples/test-design/test-case-sample.md
```

### 4.3 移動・名称変更するファイル

```text
samples/REDME.md
  → samples/README.md

samples/license_card_mobile_capture/
  → samples/apps/license_card_mobile_capture/
```

### 4.4 変更しないファイル

```text
docs/naming-conventions.md
docs/power_apps_canvas_app_dev_standard.md
```

---

## 5. Gitコマンド例

リポジトリのルートで実行する。

```powershell
git mv samples/REDME.md samples/README.md

New-Item -ItemType Directory -Force samples/apps | Out-Null
New-Item -ItemType Directory -Force samples/test-design | Out-Null

git mv samples/license_card_mobile_capture samples/apps/license_card_mobile_capture
```

本セットのファイルを上書き・追加した後、以下を確認する。

```powershell
git status
git diff --check
git diff
```

問題がなければコミットする。

```powershell
git add .
git commit -m "Align standards structure and test traceability IDs"
git push
```

---

## 6. ID体系

### 6.1 機能ID

| 種別 | ID例 | 定義 |
|---|---|---|
| アプリ親管理単位 | `UI001` | 1キャンバスアプリ |
| 画面 | `UI001-01` | `UI001`配下の1 Screen |
| インターフェース | `IF001` | 外部システムとの1送受信機能 |
| バッチ | `BT001` | 定期・一括・非同期で実行する1処理 |
| レポート | `RP001` | 1帳票 |
| 共通 | `CM001` | 1公開UDFまたは1公開メソッド |
| データ移行 | `MG001` | 1移行先 |

### 6.2 設計・テストID

| 種別 | ID例 |
|---|---|
| 要件 | `REQ001` |
| ユースケース | `UC001` |
| データフロー | `DF001` |
| 機能単体テスト | `FUT-UI001-01-001` |
| 機能間結合テスト | `FIT-DF001-001` |
| システムテスト | `ST-UC001-001` |

IDは名称変更時にも原則として維持し、廃止したIDを別の対象へ再利用しない。

---

## 7. 適用確認

- [ ] ルートREADMEから5つの標準へ移動できる
- [ ] Copilot指示に5つの標準が記載されている
- [ ] `docs/README.md` のサンプルリンクがすべて存在する
- [ ] 旧ID（`SCR-`、`FLOW-`、`FUNC-`等）がテスト標準内に残っていない
- [ ] IFとBTが別の機能種別になっている
- [ ] 機能一覧がデータフロー・ユースケース・テストケースより先に作成される
- [ ] 機能一覧のIDを各成果物が参照している
- [ ] `samples/REDME.md` が存在しない
- [ ] アプリサンプルが `samples/apps/` に配置されている
- [ ] テスト設計サンプルが `samples/test-design/` に配置されている
