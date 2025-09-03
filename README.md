
# Intera Legal Site (Relative Paths)

GitHub Pages の Project site / User site どちらでも動くよう相対パスで構成。

## 公開手順
1. GitHub でリポジトリ作成（例：`mio-legal`）。
2. このフォルダの中身を `main` に push。
3. Settings → Pages → Deploy from a branch → Branch: `main` / Folder: `/`。

## ストアURL
- プライバシーポリシー：`https://<you>.github.io/mio-legal/legal/privacy/`
- 利用規約：`https://<you>.github.io/mio-legal/legal/terms/`
- Privacy Choices（任意）：`https://<you>.github.io/mio-legal/legal/choices/`
- アカウント削除（Play Web開始リンク）：`https://<you>.github.io/mio-legal/legal/delete/`

## 置換
- 事業者名 / 住所 / お問い合わせメール
- 施行日・最終更新日・版番号（`policy.json` と各ページ表記）
- Google フォーム `FORM_ID`

## 備考
- `<meta name="robots" content="noindex">` を入れています。インデックスさせたい場合は削除してください。
- `/legal/policy.json` をアプリでフェッチし、同意バージョン判定に利用できます。
