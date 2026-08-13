# kennyfujita.github.io

藤田健人の個人サイト。静的 HTML のみ。ビルド不要。

- 公開先: <https://kennyfujita.github.io/>
- 英語がメイン、日本語版は `/ja/` 以下

## ローカルで見る

```sh
python3 -m http.server 8000
# http://localhost:8000/
```

## 公開する

`main` に push すれば GitHub Pages が自動で配信する（1分程度）。

```sh
git add -A && git commit -m "update" && git push
```

## 編集するとき

編集ルールは [CLAUDE.md](CLAUDE.md) にまとめてある。とくに次の2点:

- 英語ページを直したら `/ja/` の対応ページも必ず直す
- ヘッダー/フッターは全 HTML に複製されているので、ナビ変更は一括で行う
