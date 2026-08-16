# このリポジトリの編集ルール

藤田健人の個人サイト。`https://kennyfujita.github.io/` で公開。
ビルド工程なし・外部ライブラリなし・JavaScript なしの静的 HTML。
`main` に push した内容がそのまま公開される。

**JavaScript なしはこのリポジトリ全体に効く。例外は無い。**
以前 `tools/tsundoku/` に Web アプリを置いていたが、2026年8月に
[`tsundoku-tracker`](https://github.com/KennyFujita/tsundoku-tracker) として
独立したリポジトリへ移した。**このリポジトリに Web アプリを置き直さないこと**
（詳しくは「制作物の置き方」の節）。

## 掲載しないもの（重要）

- **現在の勤務先の社名**。職種（ロボットエンジニア / Robotics Engineer）までにとどめる
- **住所・居室番号・電話番号**
- メールアドレスは必ず `[at]` 表記にする（`@` を直接書かない）

過去の所属（名古屋大学、東京都市大学、株式会社MAZIN、セビージャ大学、東京農工大学）は掲載してよい。

## 構成

```
index.html            Home        /ja/index.html
publications.html     業績        /ja/publications.html
cv.html               経歴        /ja/cv.html
works.html            制作物      /ja/works.html
notes/index.html      ノート一覧  /ja/notes/index.html
notes/_template.html  記事の雛形  /ja/notes/_template.html
contact.html          連絡先      /ja/contact.html
assets/style.css      全ページ共通の唯一のスタイルシート
assets/img/           画像
.nojekyll             GitHub Pages の Jekyll 処理を止める（`_template.html` を配信するため必須）
```

**このリポジトリにあるのは文書ページだけ。** 制作物（Web アプリ）はそれぞれ別リポジトリにあり、
ここには `works.html` の紹介カードとリンクだけを置く。

## 守ること

### 1. 英語と日本語は必ず対で更新する

英語ページを変更したら、対応する `/ja/` のページも同じ内容に更新する。
これは旧 Google Sites で英日の情報量がずれていた（業績が英語版だけ少ない、
受賞が日本語版だけ少ない）ことへの再発防止策。**片方だけの更新はしない。**

対応が取れているかは件数で確認できる:

```sh
grep -c '<li>' publications.html ja/publications.html   # 業績の件数が一致すること
```

### 2. ヘッダーとフッターは全ファイルに複製されている

include の仕組みを持たないため、`<header class="site-header">` と
`<footer class="site-footer">` は各 HTML に同じものが書かれている。
**ナビゲーション項目を増減するときは、以下すべてを一括で書き換える。**

```sh
grep -rl 'class="site-nav"' --include='*.html' .
```

英語ページのナビは `/` 始まりの絶対パス、日本語ページは `/ja/` 始まり。
現在開いているページのリンクには `aria-current="page"` を付ける。
`lang-switch` は対になる相手の言語ページを指す（例: `cv.html` ⇔ `/ja/cv.html`）。

### 3. スタイルは `assets/style.css` だけ

ページ内に `<style>` を書かない。色は必ず CSS 変数（`var(--fg)`, `var(--accent)` など）を使う。
ライト/ダーク両対応になっているので、色を直書きすると片方で読めなくなる。

日本語は **BIZ UDPGothic**（モリサワのユニバーサルデザイン書体、SIL OFL）を
`style.css` 冒頭の `@import` で Google Fonts から読み込んでいる。欧文は
フォントスタックの先頭にある OS 標準の UI フォントが当たる。外部読み込みはこの1件だけ。

主なクラス:

| クラス | 用途 |
|---|---|
| `.wrap` | 横幅を揃えるコンテナ。`main` と `header`/`footer` の直下に置く |
| `.hero` / `.hero-photo` | トップの顔写真＋名前 |
| `.linkrow` | 丸いリンクボタンの並び |
| `.cards` / `.card` | カードのグリッド（研究紹介、制作物） |
| `.pub-list` | 業績リスト。`.authors` `.title` `.venue` `.self` `.award` `.orig` を内側で使う |
| `.timeline` | 経歴（`.when` と `.what`、`.what` の中に `.sub`） |
| `.plain-list` | 箇条書き（`.when` で日付を先頭に置ける） |
| `.note-list` | ノート一覧（`.date` と `.tag`） |
| `.note` / `.muted` | 補足テキスト |

上の3つはこのリポジトリの全ページに効く。**Web アプリはこのリポジトリに置かない**（次節）。

## 制作物の置き方

**制作物（Web アプリ）は1つにつき1リポジトリ。このリポジトリには置かない。**

| 制作物 | リポジトリ | 公開URL |
|---|---|---|
| 積読と進捗 | [`tsundoku-tracker`](https://github.com/KennyFujita/tsundoku-tracker) | `/tsundoku-tracker/`（GitHub Pages） |
| RM換算表 | `rm-calculator`（PRIVATE） | Netlify |

理由:

1. **プロフィールに作品として並ぶ。** リポジトリ一覧に出るのは独立したリポジトリだけで、
   このリポジトリの中のフォルダは「個人サイト」の一部としか見えない
2. **文書ページのルールを緩めずに済む。** JavaScript なし・外部依存なしという方針は
   業績や経歴を確実に読ませるためのもので、例外区画を作るとルールが二段構えになって濁る
3. **アプリ固有の知識がコードと一緒に旅する。** API の叩き方や配色の検証結果は、
   そのリポジトリの `CLAUDE.md` に置いた方が次に触るときに見つかる

新しい制作物を足すときも同じ形にする。**このリポジトリでやるのは `works.html` /
`ja/works.html` にカードを1枚ずつ（英日ペアで）足すことだけ。**

`tools/` は空になったので使わない。以前ここに `tsundoku` があった経緯は
`git log -- tools/` で辿れる。

### 制作物として公開するときに揃えること

`works.html` に載せる前に、アプリ側で次を済ませておく（`tsundoku-tracker` でやったこと）:

1. `noindex` の meta を外す
2. **サンプル投入ボタン**を足す。初見の人は空の画面では何も分からないため。
   **これは忘れやすい。** RM換算表は開いた瞬間に使えるが、状態を持つ道具は
   データが無いと何も見えず、初見の人がそのまま帰ってしまう
3. SEO 一式（`description` / Open Graph / canonical）
4. MIT ライセンスの `LICENSE` ファイル（GitHub のサイドバーに表示が出る）

## スライドの置き方

**発表スライドは別リポジトリ。[`slides`](https://github.com/KennyFujita/slides) に置き、
`/slides/private/` で公開している。** 理由は Web アプリと同じで、復号に JavaScript が
要るため。ここに置くと「JS なし・例外なし」が崩れる。

あちらは**入口ページ自体が暗号文**で、解錠すると初めてタイトルの一覧が現れる。
配信される HTML に題名は書かれておらず、ファイル名も内容を示唆しない文字列にしてある。
決まりごとは `slides/CLAUDE.md` にある。

**ただしこれは「不意に見られたくない」ものを置く場所であって、秘密の保護ではない。**
暗号文は誰でも落とせてオフラインで総当たりできる。本当に外に出せないものは
private リポジトリ + Netlify（`rm-calculator` と同じ形）にすること。

このリポジトリでやるのは `notes/index.html` / `ja/notes/index.html` に
`/slides/private/` への 🔒（`&#128274;`）リンクを1行置くことだけ。

**リンクの文言にタイトルを書かないこと。** 現在は次の2つで、説明は本文に足さず
括弧書きに収めている（一覧が長くならないように）:

```html
<a href="/slides/private/">&#128274; Restricted notes (password required)</a>
<a href="/slides/private/">&#128274; 限定公開のノート（パスワードが要ります）</a>
```

ここに題名を書くと、鍵の内側に隠した意味が消える。個々のスライドへ直接リンクするのも
不可。入口を通らないとパスワードの引き継ぎ（`sessionStorage`）が効かず、二度訊かれる。

## 作業手順

### ノートを1本追加する

1. `notes/_template.html` をコピーして `notes/YYYY-MM-DD-slug.html` を作る（slug は英小文字とハイフン）
2. `<title>`、`<meta name="description">`、`<h1>`、日付、タグ、本文を埋める
   - タグは `study` / `reading` / `travel` / `misc` のいずれか
3. `notes/index.html` の `<ul class="note-list">` の **先頭** に1件追加する（新しい順）
4. 初めて記事が入るときは `notes/index.html` の `.empty-state` ブロックを削除する
5. 日本語版でも同じことを `/ja/notes/` に対して行う

### 制作物を1件追加する

`works.html` の `<div class="cards">` 内、コメントアウトされたテンプレートをコピーして埋める。
`/ja/works.html` も同様に。

### 業績を1件追加する

`publications.html` の該当セクション（学術論文 / 国際会議 / 国内学会）の `<ol class="pub-list">` の
**先頭** に追加する。自分の名前は `<span class="self">` で囲む。
書誌情報は DOI から Crossref で裏取りできる:

```sh
curl -s "https://api.crossref.org/works/<DOI>" | python3 -m json.tool | head -40
```

`/ja/publications.html` も忘れずに。国際的な論文は日本語ページでも英語表記のまま、
国内学会は日本語表記で載せる。

## 確認とデプロイ

```sh
# ローカル確認
python3 -m http.server 8000     # http://localhost:8000/

# 内部リンクがファイルとして存在するかの確認
grep -rhoE 'href="/[^"]*"' --include='*.html' . | sort -u

# 公開
git add -A && git commit -m "..." && git push
```

push から反映まで1分程度かかる。

## 画像の扱い

- 公開する画像は **長辺 600px 程度・数十 KB** まで縮小してから置く。カメラ原本をそのまま
  コミットしない（`assets/img/profile.jpg` は 12000×9000 の原本から切り出して 600×600 にしたもの）

```sh
sips -c <辺> <辺> --cropOffset <top> <left> 原本.jpg --out /tmp/crop.jpg
sips -Z 600 /tmp/crop.jpg --out assets/img/出力.jpg
```

- 再エンコードで EXIF（位置情報を含む）が落ちる。人物写真を置く前に必ず通すこと

## 未対応の TODO

- `ja/cv.html` の勉強会メンバー名がローマ字のまま（漢字表記が未確認）
- **研究セクションに図は置かない方針**。一度 Asian J. Control と IEEE Access の図を
  載せたが、本人判断で外した。元画像は `.gitignore` 済みで手元にのみ残っている

## デザインを変えるときの原則

一度「全体をもっと目立たせる」方向で作り直して差し戻した経緯がある
（`git log` の `Redesign around type scale` と、その revert）。

失敗の原因は大きな文字そのものではなく、**文字・顔写真・余白・図を同時に大きくしたこと**。
全部を一斉に強くしたので、どれも主役にならず全体のバランスが崩れた。
階層を作るために文字を大きくしたのに、周りも一緒に大きくしたので差が消えた、という自己矛盾。
とくに顔写真は「名前の横に小さく添える識別子」から「本文の隣に置かれた 150px の円」になり、
見出しと張り合ってしまった。

守ること:

- **1画面の中で最も強い要素は1つだけ**。何かを大きくしたら、周りは同じかそれ以下に抑える
- **一度に変える要素は1つ**。文字サイズ・写真サイズ・余白・図の追加を同時にやらない。
  変えたら都度見てもらう
- **顔写真は控えめに保つ**（現状 132px）。名前より目立たせない
- 大きな文字や余白そのものは禁じ手ではない。使うなら他を引くこと
