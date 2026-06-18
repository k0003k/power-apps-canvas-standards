# Power Apps キャンバスアプリ開発標準（命名規則）

## プレフィックス方針

### 基本方針
プレフィックスは検索性および参照性を向上させるために利用する。

コントロールを Power Fx の数式から参照する場合は、種別プレフィックスを付与する。

例:
- btn_submit
- txt_comment
- ico_back
- gal_requests

### プレフィックス省略時の扱い
数式から参照しない小規模コントロールは、命名変更を必須としない。

その場合は Power Apps のデフォルト名を許容する。

例:
- Button1
- Label3
- Icon2

ただし独自に名前を付ける場合は、原則として種別プレフィックスを付与する。

良い例:
- btn_submit
- lbl_title
- ico_back

避ける例:
- submit
- title
- back
- comment

---

## コンテナ命名

コンテナはレイアウト上の役割で命名する。

例:
- header
- content
- footer
- search_area
- filter_area
- action_area
- left_panel
- right_panel

コンテナはコードから参照する場合であっても、原則として con_ プレフィックスは付与しない。

---

## 変数

### グローバル変数
接頭語なし

例:
- login_user
- selected_request
- user_role

### ローカル変数
アンダースコア始まり

例:
- _is_loading
- _is_edit_mode
- _error_message

---

## コレクション

複数形で命名する。

例:
- requests
- approvers
- search_results
- validation_errors

---

## データソース

| 種別 | 接頭語 |
|------|------|
| SharePoint | sp_ |
| Dataverse | dv_ |
| API | api_ |

例:
- sp_requests
- dv_accounts
- api_weather

---

## User Defined Function

User Defined Function は fn_ プレフィックスを付与する。

例:
- fn_get_login_user()
- fn_validate_request()
- fn_calculate_total_amount()

Booleanを返却する場合:
- fn_is_admin_user()
- fn_has_attachment()
- fn_can_submit_request()

---

## Named Formula

Named Formula はプレフィックスを付与しない。

例:
- login_user
- is_admin
- current_fiscal_year

Named Formula は読み取り専用の値として扱う。

---

## 必須プレフィックス

- scr_ (Screen)
- gal_ (Gallery)
- frm_ (Form)
- cmp_ (Component)

## 任意プレフィックス

- btn_
- lbl_
- txt_
- cmb_
- drp_
- ico_
- img_
- grp_

---

## Boolean命名

推奨:
- is_
- has_
- can_
- should_
- needs_
- requires_
- exists_

---

## 命名の基本思想

- 標準関数は CamelCase
- 独自定義要素は snake_case
- 関数は fn_
- ローカル変数は _
- コレクションは複数形
- コンテナは役割名
- データソースは接頭語で種別を表現する
