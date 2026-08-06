# 機能一覧サンプル

## 1. 機能IDの体系

機能IDは、機能種別ごとの接頭語と連番で構成する。

| 機能種別 | 接頭語 | ID例 | 補足 |
|---|---|---|---|
| アプリ / UI | `UI` | `UI001` | 1キャンバスアプリを表す |
| 画面 | 親UI ID + 子番号 | `UI001-01` | `UI001`配下の1 Screenオブジェクト |
| インターフェース | `IF` | `IF001` | 外部システムとの1送受信機能。実装技術は問わない |
| バッチ | `BT` | `BT001` | 定期・一括・非同期で実行する1処理。実装技術は問わない |
| レポート | `RP` | `RP001` | 1帳票 |
| 共通 | `CM` | `CM001` | 1公開UDFまたは1公開メソッド |
| データ移行 | `MG` | `MG001` | 1移行先 |

画面の子番号は、文字列順でも正しく並ぶように2桁以上でゼロ埋めする。

`UI001`はアプリ全体を表す親管理単位であり、画面の機能単体テスト対象は原則として`UI001-01`以下のScreen機能とする。

`IF`と`BT`は実装技術ではなく責務で分類する。Azure FunctionsまたはPower Automateのいずれで実装されていても、外部システムとの送受信が主目的なら`IF`、定期・一括・非同期処理が主目的なら`BT`とする。

```text
UI001
├─ UI001-01 申請入力画面
├─ UI001-02 申請履歴画面
└─ UI001-03 申請詳細画面
```

`UI001-1`ではなく、原則として`UI001-01`を使用する。

## 2. 機能一覧

| 機能ID | 親機能ID | 機能名 | 機能種別 | 実装資産名 | 主な入力 | 主な出力 | SharePoint / 外部接続 | 関連ユースケース | 備考 |
|---|---|---|---|---|---|---|---|---|---|
| `UI001` | － | 申請管理アプリ | アプリ | `RequestManagementApp` | サインインユーザー、申請情報 | 申請登録、履歴表示 | `sp_requests` | `UC-REQUEST-SUBMIT`, `UC-REQUEST-VIEW` | 親UI機能 |
| `UI001-01` | `UI001` | 申請入力画面 | 画面 | `scr_request_entry` | 申請内容 | 申請レコード | `sp_requests` | `UC-REQUEST-SUBMIT` | 1 Screen |
| `UI001-02` | `UI001` | 申請履歴画面 | 画面 | `scr_request_history` | サインインユーザー | 申請一覧 | `sp_requests` | `UC-REQUEST-VIEW` | 1 Screen |
| `UI001-03` | `UI001` | 申請詳細画面 | 画面 | `scr_request_detail` | 申請ID | 申請詳細 | `sp_requests` | `UC-REQUEST-VIEW` | 1 Screen |
| `IF001` | － | 外部申請送信API | インターフェース | `SendRequest` | 申請メッセージ | 受付結果 | 外部申請API | `UC-REQUEST-SUBMIT` | 1 Azure Functions関数 |
| `IF002` | － | 外部結果受信API | インターフェース | `ReceiveResult` | 結果メッセージ | 申請状態更新 | 外部申請API、`sp_requests` | `UC-REQUEST-VIEW` | 1 Azure Functions関数 |
| `BT001` | － | 申請登録フロー | バッチ | `flow_submit_request` | 申請情報 | SharePoint登録、通知 | `sp_requests` | `UC-REQUEST-SUBMIT` | 子フローを含む |
| `RP001` | － | 申請一覧帳票 | レポート | `request_list_report` | 期間、状態 | 申請一覧 | `sp_requests` | `UC-REQUEST-REPORT` | 1帳票 |
| `CM001` | － | 申請入力検証 | 共通 | `fn_validate_request` | 申請情報 | 検証結果 | なし | `UC-REQUEST-SUBMIT` | 1公開UDF |
| `MG001` | － | 申請データ移行 | データ移行 | `request_migration_app` | 旧申請データ | `sp_requests` | `sp_requests` | `UC-DATA-MIGRATION` | 1移行先 |

## 3. 作成ルール

- 機能一覧を、データフロー対応表、ビジネスファンクションチャート、テストケースより先に作成する
- すべての機能に一意な機能IDを付与する
- 画面機能は親アプリIDを継承する
- IDは意味を持たせすぎず、名称変更時にも原則として維持する
- 廃止したIDを別機能へ再利用しない
- 機能追加・削除・責務変更時は、同一Pull Requestで機能一覧を更新する
- データフロー、ユースケース、テストケースは必ず機能一覧のIDを参照する

## 4. AI利用時の扱い

AIは、変更影響分析やテスト生成を行う前に機能一覧を読み込み、実装資産名から機能IDを特定する。

実装資産が機能一覧に存在しない場合は、推測で既存IDへ割り当てず、未登録機能として報告する。
