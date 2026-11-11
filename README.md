# 上京サポート 採用LP（AIGeo広告の受け皿）

AIGeo（AdInte）経由のInstagram広告から、地方の美容学生・若手美容師の「まずは話を聞く」申込を受けるページ。
仕組みは `../ai-event-lp` と同じ（GitHub Pages ＋ GAS ＋ スプレッドシート ＋ Chatwork通知 ＋ 自動返信メール）。

```
index.html    LP本体（CSS・JSも同じファイル内）
thanks.html   送信完了ページ（Metaピクセル PageView＋Lead）
privacy.html  プライバシーポリシー（仮。正式版に差し替える）
gas/Code.gs   申込の受け口（GitHubには載せない。.gitignore で除外済み）
images/       ogp.png(1200×630) を置く ※未作成
```

デザインは公式サイト（beautygrow.co.jp）に合わせている：白基調・ネイビー文字・パステルのグラデーション・Noto Sans JP＋Sen・角型ネイビーボタン。給与・デビュー・制度の数字は新卒採用チラシ（2026年10月時点）が出典。

返信期日「1週間以内に必ず連絡」は index.html（フォーム上）・thanks.html・gas/Code.gs（`replyDays`）の3か所にある。変えるときは3か所そろえる。

写真は入れていない（文字だけのページ）。BG_shinsotsu_lp の画像はAI生成のため、採用ページで実在スタッフのように見せるのは避ける。実写の写真が用意できたら追加する。

---

## 1. 広告3本ごとのURL（ファーストビューの文言が切り替わる）

`utm_content` の値で、冒頭の一言（オレンジの帯）とリード文が変わる。文言は index.html の `VARIANTS` で直す。

```
(1) 2年生・未内定    https://beautygrow-lp.github.io/joukyo-recruit-lp/?utm_source=aigeo&utm_medium=paid&utm_campaign=joukyo2611&utm_content=c1
(2) 1年生・早期      https://beautygrow-lp.github.io/joukyo-recruit-lp/?utm_source=aigeo&utm_medium=paid&utm_campaign=joukyo2611&utm_content=c2
(3) 第2新卒          https://beautygrow-lp.github.io/joukyo-recruit-lp/?utm_source=aigeo&utm_medium=paid&utm_campaign=joukyo2611&utm_content=c3
インスタのプロフィール https://beautygrow-lp.github.io/joukyo-recruit-lp/?utm_source=instagram&utm_medium=organic&utm_campaign=joukyo2611
```

- `utm_content` がない・違う値のときは、共通の文言（「地方の美容学生・若手美容師のみなさんへ」）が出る
- スプレッドシートの「utm_content」列で、どの広告から来た申込かを集計できる
- AdInte 側で URL のパラメータが消されないか、入稿前に確認する

---

## 2. 【 】の差し込み一覧

ファイル内を「【」で検索すると見つかる。

**先輩の声（大村さん）のコメントは仮の文章。** 公開前に本人に取材して本人の言葉に差し替え、写真・名前・出身校の掲載同意を取ること（index.html の `PROVISIONAL` コメントの箇所）。上京サポートの対象者・条件はLPに書かず、面談で説明する方針。

| 項目 | ファイル | 確認先 |
|---|---|---|
| 【ピクセルID】 | index.html・thanks.html | Meta広告を使う場合のみ。使わなければそのままでよい（自動で無効） |
| 【AdInteの計測タグ】【AdInteの計測タグ（CV用）】 | index.html・thanks.html | AdInte の担当者 |
| 【および株式会社AdInteの計測タグ（設置する場合）】 | privacy.html | タグを置くなら【】だけ外す。置かないなら文ごと削除 |
| 【GASのURL】 | index.html の CONFIG | 手順4で発行 |
| 【公開URL】（og:url・og:image） | index.html | `https://beautygrow-lp.github.io/joukyo-recruit-lp` |
| 【問い合わせ先メールアドレス】 | privacy.html・gas/Code.gs | 採用担当 |
| 【会社所在地】【制定日】 | privacy.html | 総務（正式版に差し替えるなら不要） |

※ gas/Code.gs のメール件名・本文にある「【受付完了】」「【このあとの流れ】」は飾りなので、そのまま残す。

---

## 3. ローカルでの表示確認

```
cd /Users/kakina/cockpits/beautygrow/lp/joukyo-recruit-lp
python3 -m http.server 8766
```

ブラウザで次を開く（止めるときは `Ctrl + C`）。

```
http://localhost:8766/?utm_content=c1
http://localhost:8766/?utm_content=c2
http://localhost:8766/?utm_content=c3
```

スマホ表示は Chrome で `⌘ + Option + I` → `⌘ + Shift + M`。

---

## 4. GAS（申込の受け口）のセットアップ

手順は `../ai-event-lp/README.md` の「3. GAS（申込の受け口）のセットアップ」と同じ。違いは次のとおり。

- スプレッドシートの名前の例：`上京サポートLP 申込`
- 貼り付けるのは **このフォルダの `gas/Code.gs`**
- 定員・締切はない。ステータスは「新規／重複（同じメールアドレスの再送）／テスト」の3つ
- 「対応状況」列は最初「未対応」で入る。連絡したら担当者が「連絡済」「面談設定済」などに書き換える
- 自動返信メールは、ご希望（話を聞きたい／見学／情報だけ）と今の状況（勤務中かどうか）で「このあとの流れ」の文が変わる
- Chatwork 通知には、メールアドレスと電話番号を載せない（詳細はシートで確認）

---

## 5. GitHub Pages への公開

```
cd /Users/kakina/cockpits/beautygrow/lp/joukyo-recruit-lp
git init
git add .
git status
```

`git status` に **`gas/` が出ていないこと**を確認してから、GitHub で `joukyo-recruit-lp`（Public）を作って push する。

```
git commit -m "Add joukyo recruit LP"
git branch -M main
git remote add origin https://github.com/beautygrow-lp/joukyo-recruit-lp.git
git push -u origin main
```

リポジトリの Settings → Pages → Branch：`main`・`/(root)` → Save。

テストモード（`window.LP_TEST_MODE`）の使い方は ai-event-lp と同じ。公開前は `true`、広告の配信開始前に `false` にして push する。

---

## 6. 公開前チェックリスト

**内容（求人の表示は職業安定法で正確さが求められる）**
- [ ] 「【」で検索して、何も残っていない（メールの飾りは除く）
- [ ] 上京サポートの金額（引越し5万円・家賃月4万円・1年半）が実際の制度と一致している
- [ ] 給与（基本給25.6万円〜／アイリスト22万円〜）・デビュー平均期間・手当が最新の募集要項・チラシと一致している
- [ ] 「今の職場に知られることはありません」（FAQ）の運用が守れる
- [ ] 大村さんのコメントを本人の言葉に差し替え、写真・名前・出身校の掲載同意を取った
- [ ] 赤字の「※社内確認用の仮のコメントです」（`id="voice-provisional"`）を削除した
- [ ] privacy.html を正式版に差し替えた（またはこの仮版で公開してよいと確認した）
- [ ] OGP 画像（images/ogp.png）を置いた

**動作**
- [ ] `?utm_content=c1 / c2 / c3` で冒頭の文言が切り替わる
- [ ] テストモードで公開URLから送信 → シート（テスト行）・Chatwork・自動返信メールを確認
- [ ] 「情報だけほしい」「美容師として勤務中」で送ったとき、自動返信の文が変わる
- [ ] iPhone・Android の実機で送信まで通る
- [ ] AdInte 経由のクリックで、URLのパラメータが残っている

**本番切り替え**
- [ ] `window.LP_TEST_MODE = false;` にして push した
- [ ] スマホで黄色の「テスト中」帯が消えている
