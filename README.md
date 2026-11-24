# GitHub(する のライブラリ)

日本語のプログラミング言語「[する](https://github.com/suru-lang/suru)」から GitHub の REST API(と GraphQL)を使うライブラリです。リポジトリの設定・ツリー・ファイル・issue・PR を読み、issue やコメントや PR を書けます。中身は全部 する で書いてあり、[suru-lang/http](https://github.com/suru-lang/http) のクライアント(HTTPS)で GitHub に聞きます。

## 入れ方

[kumu](https://github.com/suru-lang/suru#プロジェクトを組むkumu) のプロジェクトで:

```sh
kumu 追加 github.com/suru-lang/github
```

依存の suru-lang/http@0.4.0 の HTTPS のために、システムに OpenSSL が要ります(Debian・Ubuntu なら `sudo apt install libssl-dev`、macOS なら `brew install openssl@3` して `export PKG_CONFIG_PATH="$(brew --prefix openssl@3)/lib/pkgconfig"`)。

## 使い方

```
「標準」 を 読み込んで 使う
「JSON」 を 読み込んで 使う
「環境」 を 読み込む
「GitHub」 を 読み込んで 使う

表示処理する は （数 と 文字列） の 成否 の 動作
表示処理する とは 入出力手順
  w とは 「GITHUB_TOKEN」 を 環境内の環境変数取得した 結果 を GitHub窓口にする
  設定 とは w で 「suru-lang/suru」 を GitHubリポジトリ取得した 中身
  （設定 から 「default_branch」 を GitHub文字値取得する） を 表示する
  f とは w で 「suru-lang/suru」 に 「README.md」 を 「」 から GitHubファイル取得した 中身
  （f を 200 で GitHubファイル切詰する） の 内容 を 表示する
  案 とは 題 は 「動きません」 で 本文 は 「手順: …」 で ラベル群 は 空 で 担当群 は 空 の GitHubIssue案
  新規 とは w で 「someone/計算」 に 案 を GitHubIssue作成した 中身
  （新規 から 「html_url」 を GitHub文字値取得する） を 表示する
  0 の 成功 を 結果にする
```

名前はどれも `GitHub` で始まります。`「GitHub」 を 読み込む` なら `GitHub内のGitHub窓口にする` のように書きます。例は [例/リポジトリ.suru](例/リポジトリ.suru)(設定・ラベル・README・開いている issue を表示する)です。

### 窓口と土台

| 書き方 | 結果 |
|---|---|
| `鍵 を GitHub窓口にする` | 窓口(`GitHub窓口`)。基点は `https://api.github.com`。鍵が「」なら鍵なしで聞く |
| `w を 基点 で GitHub基点設定する` | 基点を変えた窓口(GitHub Enterprise なら `https://主機/api/v3`) |
| `w で 方法 を 道 へ 本文 から GitHub呼出する` | 本文(JSON の文字列。なければ「」)を送って、答えの JSON |
| `w で 道 を GitHub取得する` | GET して答えの JSON |
| `w で 道 を 上限 から GitHub全件取得する` | ページをたどって、上限のページまでの要素のリスト |
| `w で 問合 を 変数 から GitHubGraphQL呼出する` | GraphQL の問合(変数は JSON)を送って、`data` の JSON |
| `v から 名前 を GitHub文字値取得する` / `GitHub数値取得する` | JSON の項目の文字列(なければ「」)/ 数(なければ 0) |

### 読む

リポジトリは 「owner/名前」 の文字列で渡します。

| 書き方 | 結果 |
|---|---|
| `w で 倉 を GitHubリポジトリ取得する` | リポジトリの設定(既定の枝・公開か・権限・トピック・issue が使えるか など)の JSON |
| `w で 倉 を GitHubラベル一覧取得する` | ラベルの JSON のリスト |
| `w で 倉 を 枝 から GitHubツリー取得する` | ファイルの一覧(`GitHubツリー`: 項目群 と 途切れ)。枝が「」なら既定の枝 |
| `w で 倉 に 道 を 参照 から GitHubファイル取得する` | ファイル(`GitHubファイル`: 道・識別(SHA)・大きさ・内容・途切れ)。参照が「」なら既定の枝 |
| `f を 上限 で GitHubファイル切詰する` | 内容を先頭の上限の文字までにしたファイル(切ったら途切れが 真) |
| `w で 倉 に 状態 を ラベル群 から GitHubIssue一覧取得する` | issue の一覧(PR は除く。状態は open・closed・all) |
| `w で 倉 に 番号 を GitHubIssue取得する` | issue |
| `w で 倉 に 番号 を GitHubコメント一覧取得する` | issue・PR のコメントの一覧 |
| `w で 倉 に 状態 を GitHubPR一覧取得する` / `w で 倉 に 番号 を GitHubPR取得する` | PR の一覧 / PR |
| `w で 倉 を GitHubブランチ一覧取得する` | ブランチの一覧 |
| `w で 倉 に 枝 を GitHubブランチ先端取得する` | その枝の先のコミットの SHA |

### 書く

| 書き方 | 結果 |
|---|---|
| `w で 倉 に 案 を GitHubIssue作成する` | 案(`GitHubIssue案`: 題・本文・ラベル群・担当群)から issue を作る |
| `w で 倉 に 番号 を 変更 から GitHubIssue更新する` | 変更(`JSON項目` のリスト)の項目だけを変える |
| `w で 倉 に 番号 を 理由 から GitHubIssue閉鎖する` | 閉じる(理由は `completed` か `not_planned`) |
| `w で 倉 に 番号 を GitHubIssue再開する` | 閉じた issue を開く |
| `w で 倉 に 番号 を 本文 から GitHubコメント作成する` | issue・PR にコメントする |
| `w で 倉 に コメント番号 を 本文 から GitHubコメント更新する` / `GitHubコメント削除する` | コメントを直す / 消す |
| `w で 倉 に 案 を GitHubPR作成する` | 案(`GitHubPR案`: 題・本文・頭・基・下書き)から PR を作る |

書いたものは、GitHub が返した JSON(できた issue など)を結果にします。コメントを消したときのように本文のない答えは `JSON無` です。

## 決めたこと

### 窓口と鍵

1. **鍵は窓口の中だけに持つ。** 鍵は `Authorization: Bearer …` の見出しにだけ入れます。失敗の理由の文には入れません(GitHub の `message` と、こちらで書いた日本語だけです)。鍵が「」なら `Authorization` を送らないので、公開のリポジトリを鍵なしで読めます(1時間に60回まで)。
2. **見出し。** どの要求にも `Accept: application/vnd.github+json` と `X-GitHub-Api-Version: 2022-11-28` を付けます。`User-Agent`(GitHub はないと断ります)は http のクライアントがいつも付ける `suru-http` を使います。http のクライアントも `Accept: */*` を付けるので Accept は2つになりますが、GitHub はどちらも受け付けます。
3. **基点。** `GitHub基点設定する` で変えられます(終わりの「/」は除きます)。道が `http://`・`https://` で始まれば、基点につながずにそのまま使います(ページ送りの URL のため)。GraphQL は、基点が `…/api/v3` なら `…/api/graphql`(GitHub Enterprise)、ほかは `基点/graphql` です。
4. **鍵の権限。** fine-grained の個人アクセストークンなら、読むだけは Metadata(読み)と Contents・Issues・Pull requests の読み、書くなら Issues・Pull requests の読み書きが要ります。

### 失敗

1. **動作の結果はどれも成否。** GitHub に断られたとき、つなげないとき、答えが JSON でないときは、理由の `失敗` を結果にします(実行時エラーにはしません)。「…した 中身」で受け取れます。
2. **状態ごとの理由。** 401 は「GitHub の鍵が違うか、期限が切れています(401): Bad credentials」、`x-ratelimit-remaining: 0` の 403・429 は「GitHub の API を使える回数を使い切りました(403。UNIX 時刻 … にもどります): …」、`retry-after` のある 403・429 は「GitHub に、間をあけるように言われました(429。30秒ほど待ってください): …」、ほかの 403 は「鍵にこの操作の権限がないようです」、404 は「名前が違うか、鍵にこのリポジトリの権限がありません」(GitHub は権限がないリポジトリにも 404 を返すため)、422 は `message` に `errors` の中身を添えます。
3. **GraphQL の errors も失敗。** 状態が 200 でも、答えに `errors` があれば「GitHub の GraphQL で失敗しました: …」の失敗です。

### 道を作る

1. **送る前に確かめる。** リポジトリは `owner/名前` の形(英数字と `-` `_` `.`、`..` は不可)、ファイルの道に `..` があれば、状態が open・closed・all でなければ、GitHub に聞かずに失敗にします。issue の題が空のときと、PR に題・頭・基のどれかがないときも送りません。
2. **符号化。** ファイルの道は「/」で分けて、それぞれを UTF-8 の %XX にします(日本語のファイル名も使えます)。枝・参照・ラベルは「/」も含めて符号化します(`feature/x` は `feature%2Fx`)。issue の番号は正の整数だけを道に入れます。

### ページ送り

1. **Link の rel="next" をたどる。** `GitHub全件取得する` は `per_page=100` を足して聞き、答えの `Link` 見出しに `rel="next"` があれば、その URL を続けて聞きます。上限のページ数(一覧の関数は10ページ、1000件)で止めます。
2. **issue の一覧から PR を除く。** GitHub の issue の一覧には PR も入るので、`pull_request` のある項目を除きます。

### ファイル

1. **Base64 を戻して文字列にする。** contents API の `content`(改行入りの Base64)を戻して UTF-8 の文字列にします。0 のバイトがあれば文字のファイルではないとして失敗にします。
2. **大きいファイルとフォルダは失敗。** 1MB を超えるファイル(GitHub が中身を返さない)とフォルダは、理由の失敗です。フォルダの中はツリーで見ます。
3. **先頭だけにするのは使う側で。** モデルに渡すときなどに長すぎるなら、`GitHubファイル切詰する` で先頭だけにします(途切れ が 真 になります)。

### 作らなかったもの

1. **issue を消す機能は作らない。** REST にはなく、GraphQL の `deleteIssue` はリポジトリの管理者しか使えません。閉じる(`not_planned`)で足りるので、作りませんでした(suru-lang/ohanashi-ollama#10 で決めました)。要るときは `GitHubGraphQL呼出する` で呼べます。
2. **push は API でしない。** ブランチに書くのは git で行う前提です。API では枝の先のコミットを読むだけです。

## 試験

```sh
kumu 試験
kumu 点検
```

試験は [試験/偽GitHub.suru](試験/偽GitHub.suru) の偽の GitHub(する で書いたもの)を相手にします。ページ送り、状態ごとの理由、送った方法・道・本文・見出しを確かめます。

## 今後の候補

- ファイルを API で書く(`PUT /repos/{o}/{r}/contents/{道}`。git を使わずに1つだけ直すとき)
- PR のレビューとレビューのコメント
- 検索(`/search/issues`・`/search/code`)
- 回数制限の時刻を、その地域の時刻で見せる
- 一覧の結果を JSON のままでなく組にする

## ライセンス

[MIT](LICENSE)
