# Power Apps キャンバスアプリ命名規則


作成日：2026-06-24  
改訂：2026-08-06（AI自動テストで利用するコントロール識別ルールを追加）

本規則は、Power Apps キャンバスアプリで使用するコントロール、変数、コレクション、データソース、Named Formula、User Defined Function等の命名を定める。

---

### 1.1 基本方針

命名は、検索性、参照性、可読性を重視する。

命名では以下を意識する。

- 何の部品か分かること
- どの役割か分かること
- 長すぎないこと
- アプリ全体で一意であること
- 画面ツリーや数式内で探しやすいこと
- 標準関数と独自定義要素を見分けやすいこと

独自定義要素は snake_case を基本とする。

```text
例：
search_button
capture_result_screen
body_container
sp_license_application
```

ただし、コントロールを Power Fx の数式から参照する場合は、種別プレフィックスを付与する。

```text
例：
btn_submit
txt_comment
ico_back
gal_requests
```

### 1.2 アプリ全体での一意性

Power Apps のコントロール名は、スクリーン単位ではなくアプリ全体で一意にする。

そのため、`header`、`body`、`footer` のような画面をまたいで重複しやすい名前は使用しない。  
画面固有の部品には、原則として画面または機能を表すサフィックスを付与する。

```text
例：
header_capture
body_capture
footer_capture
action_area_capture
capture_guide_card_capture
```

画面サフィックスは、`scr_` を除いた画面名を使用する。

```text
例：
scr_capture        -> _capture
scr_search_result  -> _search_result
```

数式から参照するコントロールは、種別プレフィックスと画面サフィックスを組み合わせる。

```text
例：
btn_submit_capture
lbl_title_capture
ico_back_capture
gal_requests_search_result
frm_request_edit_capture
```

### 1.3 プレフィックス省略時の扱い

数式から参照しない小規模コントロールは、命名変更を必須としない。  
その場合は、Power Apps のデフォルト名を許容する。

```text
例：
Button1
Label3
Icon2
```

デフォルト名を許容するコントロールであっても、後から数式、操作または自動テストの対象となった時点で明示的に命名する。

独自に名前を付ける場合は、原則として種別プレフィックスまたは画面サフィックスを付与し、アプリ全体で一意にする。

良い例：

```text
btn_submit_capture
lbl_title_capture
ico_back_capture
header_capture
```

避ける例：

```text
submit
title
back
comment
header
body
footer
```

### 1.4 必須プレフィックス

以下の要素は、種別の判別性が重要なため、プレフィックスを必須とする。

| 種別 | プレフィックス | 例 |
|---|---|---|
| Screen | `scr_` | `scr_home` |
| Gallery | `gal_` | `gal_requests` |
| Form | `frm_` | `frm_request_edit_capture` |
| Component | `cmp_` | `cmp_common_header` |
| User Defined Function | `fn_` | `fn_validate_request()` |

### 1.5 任意プレフィックス

以下のコントロールは、Power Fx から参照する場合にプレフィックスを付与する。

| 種別 | プレフィックス | 例 |
|---|---|---|
| Button | `btn_` | `btn_submit` |
| Label | `lbl_` | `lbl_title` |
| Text input | `txt_` | `txt_comment` |
| Combo box | `cmb_` | `cmb_approver` |
| Dropdown | `drp_` | `drp_status` |
| Icon | `ico_` | `ico_back` |
| Image | `img_` | `img_license` |
| Group | `grp_` | `grp_action_buttons` |

### 1.6 画面名

画面名は、画面の業務上の役割を表す。  
`Screen` は `scr_` プレフィックスを必須とする。

```text
例：
scr_home
scr_capture
scr_capture_result
scr_search_result
```

### 1.7 コンテナ名

コンテナは、見た目ではなくレイアウト上の役割で命名する。

コンテナは画面をまたいで同じ役割名が発生しやすいため、原則として画面または機能を表すサフィックスを付与する。

```text
例：
root_capture
header_capture
body_capture
footer_capture
content_capture
search_area_search
filter_area_search
action_area_capture
left_panel_search_result
right_panel_search_result
```

コンテナはコードから参照する場合であっても、原則として `con_` プレフィックスは付与しない。

垂直コンテナ、水平コンテナ、通常コンテナの種類は、画面ツリー上のアイコンで判別できるため、原則として `vertical` / `horizontal` のサフィックスは付与しない。

ただし、同一画面内で同じ役割のコンテナが複数あり、方向を名前に含めないと判別しづらい場合のみ、補助的に方向名を使用してよい。

```text
例：
action_top_capture
action_bottom_capture
```

### 1.8 変数名

グローバル変数は接頭語なしで命名する。

```text
例：
login_user
selected_request
user_role
```

ローカル変数はアンダースコア始まりで命名する。

```text
例：
_is_loading
_is_edit_mode
_error_message
```

### 1.9 コレクション名

コレクションは複数形で命名する。

```text
例：
requests
approvers
search_results
validation_errors
```

### 1.10 データソース名

データソースは、種別を判別できる接頭辞を付与する。

| 種別 | 接頭語 | 例 |
|---|---|---|
| SharePoint | `sp_` | `sp_requests` |
| Dataverse | `dv_` | `dv_accounts` |
| API | `api_` | `api_weather` |

### 1.11 Named Formula

Named Formula はプレフィックスを付与しない。

```text
例：
login_user
is_admin
current_fiscal_year
base_screen_width
```

Named Formula は、読み取り専用の計算値、参照値、共通定数として使用する。  
Named Formula を、グローバル変数経由で関数のように使うことはしない。

### 1.12 User Defined Function

処理を再利用する場合は、User Defined Function の利用を強く推奨する。

User Defined Function は `fn_` プレフィックスを付与し、処理内容が分かる動詞句で命名する。

```text
例：
fn_get_login_user()
fn_validate_request()
fn_calculate_total_amount()
fn_format_application_status()
```

Boolean を返却する場合は、意味が分かる補助動詞を使用する。

```text
例：
fn_is_admin_user()
fn_has_attachment()
fn_can_submit_request()
```

### 1.13 Boolean 命名

Boolean 値は、以下の接頭語を推奨する。

```text
is_
has_
can_
should_
needs_
requires_
exists_
```

---

### 1.14 自動テストにおける命名の扱い

Power Apps Studioで定義したコントロール名は、Playwrightから `data-control-name` として利用できるため、テスト対象コントロールの安定した識別子として扱う。

以下を標準とする。

- `data-control-name` をテストコードとアプリ間の明示的な契約として扱う
- テスト対象コントロール名を理由なく変更しない
- コントロール名を変更した場合は、関連するPage Objectとテストケースを同一変更内で更新する
- 表示文言だけをセレクターとして使用しない
- AccessibleLabelは利用者向けの意味を優先し、テスト専用の不自然な文言にしない

コントロール名の変更は、外部仕様変更ではない場合でも、自動テストに対する破壊的変更となり得る。

参考：

- [Test Canvas Apps with Power Platform Playwright Samples](https://learn.microsoft.com/en-us/power-platform/developer/playwright-samples/canvas-application)
