# このリポジトリの編集ルール

藤田健人の個人サイト。`https://kennyfujita.github.io/` で公開。
ビルド工程なし・外部ライブラリなし・JavaScript なしの静的 HTML。
`main` に push した内容がそのまま公開される。

**この「JavaScript なし」は文書ページに対するルール。`tools/` 配下の Web アプリは対象外**
（理由と条件は「`tools/` 配下の扱い」の節）。

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

tools/tsundoku/index.html   積読・進捗トラッカー（1枚で完結する Web アプリ）
tools/tsundoku/README.md    使い方・データの扱い・MIT ライセンス
```

`tools/` 配下は文書ページとは別扱い。日本語版・英語版を対で作ることはしない。

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

上の3つは**文書ページに対するルール**。`tools/` 配下には次節の別ルールが当たる。

## `tools/` 配下の扱い

### なぜ文書ページで JavaScript を禁じているのか

理由は4つ。**どれも「公開された文書」を守るためのもの**である。

1. **寿命** — ビルド工程も外部ライブラリも無いので、数年後でもそのまま開けて壊れない
2. **確実に読まれること** — 業績や経歴は検索エンジン・Google Scholar・スクリーンリーダーに
   確実に読まれる必要がある。JS で生成すると読まれない可能性が残る
3. **外部依存を増やさない** — 外部読み込みは Google Fonts の1件だけに抑えている
4. **表示の即時性** — JS を待たずに本文が出る

`tools/` 配下に置くのは文書ではなく**道具**で、本人が操作するもの。検索される必要がなく、
JavaScript が無ければ機能自体が成立しない。上の4つはどれも当てはまらないので、
ここだけ適用範囲の外にする。**ルールを緩めたのではなく、対象が違う。**

### 例外として許すもの

- **JavaScript**（素の DOM 操作のみ）
- **ページ内の `<style>` と `<script>`** — 1ファイルに内包する。
  理由は、**保存して手元で開いてもそのまま動くようにするため**。
  `assets/style.css` を参照する作りにすると、ダウンロードした人の手元で色が壊れる
- **独自のパレット** — `assets/style.css` のトークンを写す必要はない。
  サイトの色を変えても `tools/` 配下は追随しなくてよい。意図的に切り離している
- **外部 API への通信** — 現在の通信先は次の2つだけ。増やすときはここに追記する

  ISBN を調べたときだけ、3つを順に引いて欠けている項目を埋める。得意分野が違うので3つとも要る。

  | 通信先 | 役割 | 実測 |
  |---|---|---|
  | `api.openbd.jp` | **新刊**。JPRO に発売前から載るのでここでしか拾えない本がある | 専門書のカバー率 6/10、ページ数 1/12 |
  | `ndlsearch.ndl.go.jp` (SRU) | **著者とページ数**。納本制度で収録が厚い。新刊は目録作成のラグで載らない | 届いた3件は3件とも著者・ページ数が揃った |
  | `www.googleapis.com/books` | **洋書**の受け皿 | キー無しの共有クォータですぐ 429。当てにしない |

  **国立国会図書館サーチの叩き方（重要）**

  - エンドポイントは **SRU の方**（`/api/sru`）。`/api/opensearch` は **CORS ヘッダを返さない**ので
    ブラウザからは呼べない。以前これを混同して「NDL は使えない」と誤って結論した経緯がある
  - **URL に毎回ユニークなパラメータ（`&_=` + タイムスタンプ）を付ける。これは必須。**
    NDL の前段の CloudFront は Origin をキャッシュキーに含めないため、キャッシュに当たると
    `access-control-allow-origin` の付かない応答が返り、ブラウザが遮断する。実測では
    `x-cache: Miss` なら ACAO あり、`Hit` なら ACAO なしと完全に対応していた。
    **キャッシュ避けを消すと、たまに動いてたまに失敗するという厄介な壊れ方をする**
  - `recordSchema=dcndl`。`dcndl_simple` はエラーになる
  - クエリは `query=isbn=9784873115658`（引用符は不要）
  - 応答は `recordData` の中身がエスケープされているので、**DOMParser で2段階に解く**
  - 著者は `dc:creator`（`dcterms:creator` は RDF の塊なので使わない）
  - ページ数は `dcterms:extent`（例 `237p ; 21cm`）から `(\d+)\s*p` で取る
  - **同時アクセス制限がある。** 連続で叩くと `Too Many Requests` が返る。
    1回だけ2秒待って再試行する実装にしてある。まとめて引く処理を足さないこと

  **openBD の著者は図書館の典拠形**（`三浦,宏文,1938-2020`）で人名として読めない。
  `tidyAuthor()` で整形しているが、NDL が返す `dc:creator`（`三浦宏文 著`）の方が読みやすいので
  取れた場合は上書きしている。

  **API キーを要求するサービスは足さない。** 公開ページにキーが載る上、リファラ制限をかけると
  ダウンロードして `file://` で開いた配布版が動かなくなる（持ち帰って使ってもらう方針と衝突する）。

### 例外にしないもの

- **ビルド工程**（`git push` したものがそのまま動くこと）
- **JS フレームワーク・外部ライブラリの読み込み**
- **色の直書き** — 必ず CSS 変数として `:root` に定義する
- **ライト/ダーク両対応** — `prefers-color-scheme` と `:root[data-theme]` の両方を書く
- **Web フォントの読み込み** — 配布物に外部依存を持ち込まないため OS 標準書体のみ

### 適用されないルール

- **ルール1（英日を対で更新）** — `tools/` 配下に `/ja/` 版は作らない
- **ルール2（ナビの一括更新）** — **`tools/` 配下をサイトのナビに載せない**。
  載せると全 HTML のヘッダー/フッターを書き換えることになる。アプリ側からサイトへ戻るリンクだけ置く

### そのほか

- **利用者のデータをリポジトリにコミットしない**。データは localStorage にのみ置く
- **MIT ライセンス**で公開している（README に全文）。ソースを持ち帰って使ってもらう前提
- **`<meta name="robots" content="noindex">` を入れている**。いまは自分用のため

### 制作物として本格的に公開に切り替えるとき

`tsundoku` は「自分用に作り、ソースは持ち帰れるようにしておく」段階にある。
`works.html` の制作物として正式に出す場合、以下が必要になる。

1. `noindex` の meta を外す
2. サンプルデータ投入ボタンを足す（初見の人が空の画面で何もできないため）
3. SEO 一式を足す（`description` / Open Graph / canonical）。RM換算表と同水準で
4. `works.html` と `ja/works.html` の両方に紹介カードを追加する（ルール1に従い英日ペアで）

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
