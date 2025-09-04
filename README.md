
# Intera Legal Site (Relative Paths)

GitHub Pages の Project site / User site どちらでも動くよう相対パスで構成。


## Announcement配信
この項目は、アプリ内お知らせ（Announcements）を JSON ファイルで運用するための手引きです。
配信先ファイル名: announcements.json（GitHub Pages / 任意のサーバ）

仕組みの概要
	•	アプリは最短 15 分間隔で announcements.json を取得します（スロットリングあり）。
	•	フィルタ条件（期間・アプリバージョン・言語・テーマ）を満たす告知だけが「有効」となります。
	•	required=true の告知は「ブロッキング表示」対象です。条件一致した最初の 1 件のみ、フルスクリーンで表示します。閉じると既読となり、同じ id は再表示されません。
	•	required=false の告知は内部配列に保持します。現状の UI では自動表示しません（別のバナー等が必要）。

用語:
	•	既読: フルスクリーン告知を閉じた状態。既読は id 単位で保持され、同じ id は再表示されません。

まずはこれだけ（最短手順）
	1.	announcements.json を開く
	2.	告知を 1 件追加（id は必ず一意）
	3.	必ず見せたい告知は required:true を設定（ブロッキング表示）
	4.	startAt / endAt を ISO8601（タイムゾーン付き）で設定
	5.	サーバへアップ
	6.	端末でアプリを起動（または再起動）して表示確認
	•	取得は 15 分間隔です。急ぐ場合はアプリ再起動が確実です

JSON フォーマット例（ブロッキング告知）

{
“announcements”: [
{
“id”: “2025-09-10-maint-001”,
“title”: “メンテナンスのお知らせ”,
“body”: “9/12 01:00〜03:00 にメンテナンスを行います。”,
“actionLabel”: “詳細を見る”,
“actionUrl”: “https://example.com/news/2025-09-10”,
“priority”: “normal”,
“required”: true,
“startAt”: “2025-09-10T00:00:00+09:00”,
“endAt”:   “2025-09-13T00:00:00+09:00”,
“targets”: {
“minAppVersion”: “1.0.0”,
“maxAppVersion”: null,
“locales”: [“ja”],
“themes”:  [“Simple”,“Dive”]
}
}
]
}

各項目の説明

ルート:
	•	announcements（配列）
	•	告知オブジェクトのリスト。配列の順番が重要です。ブロッキング告知は条件一致した先頭の 1 件のみ表示されます。

告知オブジェクト:
	•	id（必須・文字列）
	•	告知を一意に識別する ID。既読管理にも使用します。再表示したい場合は新しい id にしてください。
	•	title（必須・文字列）
	•	短めのタイトル（1 行想定）。
	•	body（任意・文字列）
	•	詳細本文。複数行可。未指定でも可。
	•	actionLabel / actionUrl（任意）
	•	CTA（ボタン）表示のための文言とリンク先。
	•	挙動ルール: 両方が存在し、かつ actionUrl が https の場合のみボタンを表示。どちらか一方でも欠ける／http など無効 URL の場合はボタンを出しません。
	•	セキュリティ（ATS）のため、actionUrl は https 推奨。http を使う場合はアプリ側の許可設定が別途必要になります。
	•	priority（任意・文字列）
	•	“normal” / “high” を想定。現時点の実装では未使用（将来の並び替え・強調用の予約フィールド）。
	•	required（任意・真偽、既定: false）
	•	true: ブロッキング（フルスクリーンで必ず見せる）。1 件のみ表示。閉じると既読になり同じ id は再表示されません。
	•	false: 非ブロッキング（現行 UI では自動表示なし。別 UI が必要）。
	•	startAt / endAt（任意・ISO8601 日時）
	•	掲載期間。タイムゾーン（Z または +09:00 など）を必ず含めてください。開始前・終了後は表示対象外です。
	•	targets（任意・オブジェクト）
	•	配信対象の絞り込み。指定が無い項目は「全体対象」。
	•	minAppVersion / maxAppVersion（SemVer “1.2.3”）
	•	minAppVersion 未満のアプリには出しません。maxAppVersion を超えるアプリにも出しません。未指定は null。
	•	locales（言語コードの配列。例: [“ja”,“en”]）
	•	端末の言語コードで判定（大文字小文字は不問）。
	•	themes（テーマ名の配列。例: [“Simple”,“Dive”]）
	•	アプリ側で小文字化して比較します。表記ゆれは気にしなくて OK。

運用ルールとコツ
	•	ID 設計: YYYY-MM-DD-用途-連番（例: 2025-10-01-release-001）のように重複しない規則で。
	•	再表示したい: 同じ id は既読で出ません。新しい id にしてください。
	•	すぐ反映されない: 取得は 15 分間隔。テスト時はアプリ再起動が確実です。
	•	複数ブロッキング: 複数が同時に条件一致しても先頭 1 件のみ表示。重要度の高いものを配列先頭へ。
	•	期間の時差: startAt / endAt は必ずタイムゾーンを明記（Z または +09:00 等）。
	•	リンクの安全: actionUrl は https を使用（ATS のため）。http は原則不可。
	•	非ブロッキングの見せ方: 現状 UI では自動表示しません。必要に応じてバナー／お知らせ一覧など別 UI を用意してください。

よくある質問（FAQ）

Q. 公開したのに出ません。
A. 次を確認してください。
	•	15 分以内の再取得制限にかかっていませんか？（再起動で即時確認）
	•	required は true ですか？（非ブロッキングは現行 UI では自動表示されません）
	•	startAt / endAt の期間内で、タイムゾーンは正しく付いていますか？
	•	targets（バージョン・言語・テーマ）の条件に合致していますか？
	•	以前に同じ id を既読にしていませんか？（id を変更）

Q. すぐに停止したい。
A. endAt を現在より前にして再公開してください。端末側の再取得（〜15 分）またはアプリ再起動が必要です。

Q. 同時に複数の重要告知を出したい。
A. ブロッキングは 1 件のみです。配列の先頭に最優先を置き、残りは期間をずらすか、順次 id を変えて出してください。

テンプレ（コピペ用・コメントなし）

{
“announcements”: [
{
“id”: “YYYY-MM-DD-xxxx-001”,
“title”: “タイトル”,
“body”: “本文”,
“actionLabel”: “詳しく見る”,
“actionUrl”: “https://example.com/path”,
“priority”: “normal”,
“required”: true,
“startAt”: “2025-09-10T00:00:00+09:00”,
“endAt”:   “2025-09-12T00:00:00+09:00”,
“targets”: {
“minAppVersion”: null,
“maxAppVersion”: null,
“locales”: [“ja”],
“themes”:  [“Simple”,“Dive”]
}
}
]
}

 
