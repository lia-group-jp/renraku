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

Apps Scriptのウェブアプリを直接開くと、上に「このアプリケーションは Google Apps Script のユーザーによって作成されたものです」の帯が出ます。Google側が出すもので消せません。
そこで **iframe 1枚だけのページ**(`wrap/gtj/index.html`・`wrap/ma/index.html`)にアプリを埋め込み、そのURLを配ります。帯が出ず、スマホでも画面いっぱいに出ます。

- 置き場所: GitHub `lia-group-jp/renraku`(公開リポジトリ・GitHub Pages)。中身はこの2枚とREADMEだけで、パスワードも発言データも入っていません
- 埋め込みを許可するため、`Code.gs` の `doGet` に `setXFrameOptionsMode(ALLOWALL)` を入れています
- ⚠️ **Googleサイトに埋め込む方法は失敗**。埋め込み枠の縦横比が固定で、スマホでは画面の1割ほどに縮む(375×812で実測 338×209)。作った `sites.google.com/view/gtj-renraku` は公開停止済み
- 画面を直した時は、アプリ本体(Apps Script)を新バージョンでデプロイするだけ。入り口ページは触らなくてよい

## 公開済みのURL(2026-09-21)

| | 社員に配るURL | データ(スプレッドシート) |
|---|---|---|
| GTJ | https://script.google.com/macros/s/AKfycbwXPn372KuZeq0ePxuChG7QIUq8yK6S5Ztz1_5412LmzmGpf1eavO5KOx-p2lGnFHe-VA/exec | https://docs.google.com/spreadsheets/d/18PGE4PCCZJDfHMllIIes7ex5El8EtJJXGp9Z5D5Z7JM/edit |
| MA | https://script.google.com/macros/s/AKfycbxOuhl5j4-xRrinahXusHPli-lse0iUm4sg_psPltjiJzhlt97-3Q_q51nZrgOw1SM4/exec | https://docs.google.com/spreadsheets/d/1V2hjr5L9ywH7JhA40j7WasCTPdfycbkyx6Y6NQk9brU/edit |

⚠️ 複数のGoogleアカウントにログインしたChromeで開くと「ファイルを開くことができません」と出る(Googleの既知の制限)。シークレットウィンドウかスマホで開く。

サーバー側は両社とも同じ `Code.gs` を使います。
**会社ごとに別のアカウントで、次の手順を1回ずつ行ってください。**

## 公開のしかた(1社あたり約10分)

1. 持ち主アカウントでGoogleにログインし、スプレッドシートを新しく作る
   (名前がブラウザのタブ名になります。例:「GT JAPAN㈱　社内連絡」「（社）Management　社内連絡」)
2. メニューの **拡張機能 > Apps Script** を開く
3. `コード.gs` の中身を全部消し、`Code.gs` の中身を貼り付けて保存する。
   先頭の `TITLE` を会社に合わせて直す(ブラウザのタブに出る名前。スプレッドシート名は使わない)
4. 左の「ファイル ＋」から **HTML** を選び、名前を **`index`** にする。
   会社に合った `index.html`(GTJなら `gtj/`、MAなら `ma/`)の中身を貼り付けて保存する
5. 上の関数選択で **`setup`** を選んで「実行」する。
   許可を求められたら持ち主アカウントで許可する。`台本`・`名簿`・`設定` の3シートが作られる
6. スプレッドシートに戻り、次の2つを入れる
   - `設定` シートの **B1** にパスワード(会長が決めた値。このファイルには書かない)
   - `名簿` シートの **A2から下** に社員の氏名(1行に1人)
7. Apps Scriptで **デプロイ > 新しいデプロイ > 種類:ウェブアプリ**
   - 次のユーザーとして実行:**自分**
   - アクセスできるユーザー:**全員**
8. 表示された **ウェブアプリのURL** と **パスワード** を社員に配る

## 日々の運用

| やりたいこと | やり方 |
|---|---|
| 発言の日時・発信者・本文を直す | `台本` シートのセルを直接書き換える(画面には次の更新で出る) |
| 社員を足す・外す | `名簿` シートに行を足す・消す |
| 退職者が出た | `設定` B1 のパスワードを変えて、残る社員に知らせる |
| 画面を直した | Apps Scriptに貼り直し、**デプロイ > 新しいデプロイ**。⚠️ **/exec のURLが変わる**ので、`wrap/*/index.html` の `iframe src` を新URLに直してGitHubに反映する(社員に配るURLは変わらない)。「デプロイを管理 > 編集 > 新バージョン」はURLが変わらないが、操作が効かないことが多い |

## 仕様と限界

- 画面に出るのは最新の200件です。古い発言もシートには全部残ります
- 通知は出ません。画面を開いている間、30秒ごとに新しい発言を読み込みます
- 開くときに1〜3秒かかります。画面の上に「Googleのユーザーが作成」という帯が出ます(無料版の仕様で消せません)
- パスワードは全員共通なので、なりすましは防げません。名前は自己申告です
- 本文が `=` `+` `-` `@` で始まる場合、数式にならないようシートでは先頭に `'` を付けて保存します
