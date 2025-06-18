mkdir -p ~/Downloads/mitsumo && cat << 'EOF' > ~/Downloads/mitsumo/RULES.md
# mitsumo 開発ルール（RULES.md）

本ドキュメントは、建築積算SaaS「mitsumo」の開発・運用に関するすべてのルールを定めるものである。

---

## 1. プロジェクト概要

- 「mitsumo」は、建築図面（PDF・画像・CAD）や仕上げ表・展開図から構造データを抽出し、積算見積書を自動生成するSaaSである。
- OCR・構造化・材料参照を行い、PDF・CSV形式で出力する。
- ファイル保存・ログ・履歴・テンプレート管理は Supabase 上で行う。

---

## 2. ディレクトリと出力ルール

- 作業ディレクトリは必ず：  
  `~/Downloads/mitsumo`

- ファイル出力形式：**heredoc構文を使用すること**  
  ```bash
  mkdir -p [dir] && cat << 'EOF' > [filename]
  ...（ファイル内容）...
  EOF

ファイル出力後には Git コマンドを提示：

git add [filename]
git commit -m "[変更内容]"

✅ 部分修正は禁止。常に全文再生成すること。

✅ ZIP形式での出力は禁止。

3. UI設計ルール
使用UI基盤：NobleUI（Bootstrapベース）

禁止事項：TailwindCSSは使用禁止

使用コンポーネント：card, form-control, btn, container, row, col など

使用ライブラリ：bootstrap@5.3.x, react-icons

Sidebar構成（白背景・黒文字・青選択）

mitsumo

▶ 見積もり
  └ ファイルアップロード
  └ 構造データ編集
  └ PDF出力 / CSV出力

▶ テンプレート管理
  └ テンプレート一覧
  └ タグ・カテゴリ管理

▶ 履歴・ログ
  └ 見積もり履歴
  └ 操作ログ

▶ マスター管理（社内）
  └ 顧客管理
  └ Webhook設定
  └ 使用量レポート

Sidebar幅は 220px〜240px

見出し：小文字・text-muted

リンク：text-dark、選択中は text-primary fw-semibold bg-light

ドット表示は禁止（NobleUIに準拠）

4. Supabaseセキュリティルール
4.1 RLS（Row Level Security）
publicスキーマ内のすべてのテーブルで RLS を必ず有効化すること。

最低限のセレクトポリシーを設定：

alter table public.[table_name] enable row level security;

create policy "Allow read for authenticated users"
on public.[table_name]
for select
to authenticated
using (true);

4.2 認証セキュリティ（Auth）
漏洩パスワード保護（Leaked Password Protection）を有効化すること

多要素認証（MFA）は TOTP（Google Authenticator等）を最低1つ有効にすること

参照：

https://supabase.com/docs/guides/auth/password-security

https://supabase.com/docs/guides/auth/auth-mfa

5. ファイル生成・AI出力のルール
ChatGPT などのAIによる出力において：

数値の憶測・補完は禁止

不明な場合は "status": "unverifiable" を含めること

曖昧な情報・自動補完による誤出力は明確に拒否する設計とする

3度修正を試して解決しない場合は別角度からアプローチ開始

開発ユーザーに対象ファイル、画面のスクショを撮ってもらい確認するなど

6. 禁止事項（厳守）
TailwindCSS の使用

ファイルの部分出力（常に全文）

ZIP形式でのファイル出力

Slack / LINE など通知機能の提案

数値の仮定・補完出力（例：「たぶんこの面積は○○㎡」など）

Supabase テーブルの RLS 無効状態の放置

7. その他
このRULES.mdは常に最新版を維持し、ChatGPT・開発者・レビューア間で共通基盤とする。

記載されていない新規機能やAPI設計についても、このルールに基づいて拡張・検討すること。

