# 社内連絡アプリ(GTJ版・MA版)

無料のチャットです。Googleスプレッドシート＋Apps Scriptで動き、ブラウザで開いて使います。

| | 持ち主アカウント | 画面 | 人数 |
|---|---|---|---|
| GTJ版 | gt.japan.official@gmail.com | `gtj/index.html`(LINE風) | 74名 |
| MA版 | be.ambitious.lg@gmail.com | `ma/index.html`(掲示板風) | 41名 |

## 社員に配るURL(2026-09-24)

| | 社員に配るURL |
|---|---|
| GTJ | https://lia-group-jp.github.io/renraku/gtj/ |
| MA | https://lia-group-jp.github.io/renraku/ma/ |

画面(HTML)は **GitHub Pages** に置き、データだけをApps Scriptから取りに行きます。Googleのページをiframeで埋め込む方式はやめました。

- **なぜやめたか**: Googleに複数アカウントでログインしている端末だと、埋め込んだGoogleの画面が「現在、ファイルを開くことができません」になり、**誰も入れません**(2026-09-24に複数端末で発生)。アクセス権の設定では直りません
- **今の作り**: `gtj/index.html`・`ma/index.html` が画面そのもの。中の `const API` に書いたApps ScriptのURLへ、`fetch` でJSONをPOSTします。Googleのログイン情報は送らないので、端末のログイン状態と無関係に動きます
- `Content-Type` は **`text/plain`** にしています。`application/json` にするとブラウザが事前確認(preflight)を投げ、Apps ScriptはOPTIONSに応答できないため失敗します
- 置き場所: GitHub `lia-group-jp/renraku`(公開リポジトリ)。パスワードも発言データも入っていません
- ⚠️ **Googleサイトに埋め込む方法も失敗**。枠の縦横比が固定で、スマホでは画面の1割ほどに縮む(375×812で実測 338×209)

## データの置き場とAPI(2026-09-24)

| | Apps ScriptのAPI(画面の `const API` に入れる) | データ(スプレッドシート) |
|---|---|---|
| GTJ | https://script.google.com/macros/s/AKfycbyAAPgEkuv1uxkLTpF-5SspfueI0ffrsdaCQl35NyWwv4nMqFGpRrBLIqfSkfPAY1PDQA/exec | https://docs.google.com/spreadsheets/d/18PGE4PCCZJDfHMllIIes7ex5El8EtJJXGp9Z5D5Z7JM/edit |
| MA | https://script.google.com/macros/s/AKfycbwKtx4KJqixcGJ7XsKlSFQzs0DFiFOl1q2q5k2SYjxD9AryP5HaSo4aN2eAqjxNms7-/exec | https://docs.google.com/spreadsheets/d/1V2hjr5L9ywH7JhA40j7WasCTPdfycbkyx6Y6NQk9brU/edit |

このURLは社員には配りません。画面の中からデータを取りに行くためだけに使います。

サーバー側は両社とも同じ `Code.gs` を使います。
**会社ごとに別のアカウントで、次の手順を1回ずつ行ってください。**

## 公開のしかた(1社あたり約10分)

1. 持ち主アカウントでGoogleにログインし、スプレッドシートを新しく作る
   (名前がブラウザのタブ名になります。例:「GT JAPAN㈱　社内連絡」「（社）Management　社内連絡」)
2. メニューの **拡張機能 > Apps Script** を開く
3. `コード.gs` の中身を全部消し、`Code.gs` の中身を貼り付けて保存する。
   先頭の `TITLE` を会社に合わせて直す(ブラウザのタブに出る名前。スプレッドシート名は使わない)
4. 先頭の `HOME` を、その会社の配布URLに合わせて直す(GTJなら `/gtj/`、MAなら `/ma/`)。
   画面のHTMLはApps Scriptには置きません(GitHub Pages側にあります)
5. 上の関数選択で **`setup`** を選んで「実行」する。
   許可を求められたら持ち主アカウントで許可する。`台本`・`名簿`・`設定` の3シートが作られる
6. スプレッドシートに戻り、次の2つを入れる
   - `設定` シートの **B1** にパスワード(会長が決めた値。このファイルには書かない)
   - `名簿` シートの **A2から下** に社員の氏名(1行に1人)
7. Apps Scriptで **デプロイ > 新しいデプロイ > 種類:ウェブアプリ**
   - 次のユーザーとして実行:**自分**
   - アクセスできるユーザー:**全員**
8. 表示された **ウェブアプリのURL** を、`gtj/index.html`(または `ma/index.html`)の
   `const API` に貼ってGitHubに反映する
9. 社員には **GitHub PagesのURL** と **パスワード** を配る(Apps ScriptのURLは配らない)

## 日々の運用

| やりたいこと | やり方 |
|---|---|
| 発言の日時・発信者・本文を直す | `台本` シートのセルを直接書き換える(画面には次の更新で出る) |
| 社員を足す・外す | `名簿` シートに行を足す・消す |
| 退職者が出た | `設定` B1 のパスワードを変えて、残る社員に知らせる |
| 画面を直した | `gtj/index.html`・`ma/index.html` を直してGitHubに反映するだけ。Apps Scriptは触らなくてよい |
| サーバー側(`Code.gs`)を直した | Apps Scriptに貼り直し、**デプロイ > 新しいデプロイ**。⚠️ URLが変わるので、画面の `const API` を新URLに直してGitHubに反映する |

## 仕様と限界

- 開いた時に全件読みます。件数が多いと数秒かかります(以降の自動更新は最新200件だけ)
- 通知は出ません。画面を開いている間、30秒ごとに新しい発言を読み込みます
- 開くときに数秒かかります。しばらく誰も使っていないと、最初の1回だけ20秒ほどかかることがあります(Google側の起動待ち)
- パスワードは全員共通なので、なりすましは防げません。名前は自己申告です
- 本文が `=` `+` `-` `@` で始まる場合、数式にならないようシートでは先頭に `'` を付けて保存します
