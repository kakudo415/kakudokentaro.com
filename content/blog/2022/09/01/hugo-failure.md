---
title: "HugoとGitHub Pagesでハマったこと"
date: 2022-09-01T10:54:12+09:00
draft: false
tags: ['Web', 'Hugo']
---

このページを作る過程でハマったこととその対処法について書きます。

## 静的なファイルが配信されない・パスがおかしい

Hugoは`/static/`内のファイルを配信してくれますが、中身がそのままルート直下に置かれます。たとえば`/static/hoge.fuga`というファイルにアクセスしたいときは、`https://example.com/static/hoge.fuga`ではなく、`https://example.com/hoge.fuga`としなくてはなりません。これに気づかなかったため、前者のURLで404を吐き続けていました。

この仕様で問題となりそうなのが、`/static/`以下のファイル・ディレクトリ名とページ名が被ることです。そのため、`/static/assets/`のようにもう一段ディレクトリを深くして、`https://example.com/assets/hoge.fuga`のように静的ファイルをひとまとめに配信するようにしました。

設定ファイルでなんとかできると綺麗なのですが･･･

## Trailing Slash（URL末尾のスラッシュ）を無くせない

[ここ](https://github.com/gohugoio/hugo/issues/7458)を読む限り、サーバー側で対処する以外に解決策が無さそうです。

Trailing Slashは無いのが好みなのですが、天下のW3Cのブログ（下記）が有りのスタイルなのでこれに倣ったと思い納得することとします。

[https://www.w3.org/blog/2022/08/https-redirection-observations/](https://www.w3.org/blog/2022/08/https-redirection-observations/)

## GitHub Pagesに設定したはずのCustom domainが消える

当初は問題なくページを閲覧できるのに、しばらくすると404になり、設定したはずのCustom domainが消えているという症状です。

![Custom domain disappeared](../custom-domain-disappeared.png)

原因は、Custom domain設定時に自動的に追加された`CNAME`ファイルを、GitHub Actionsが消し飛ばしてしまったからでした。
新しい公開ファイルで`gh-pages`ブランチを更新するときに、`CNAME`ファイルごと置き換えてしまっているわけです。

### 対処法

`/static/`下に、直接`CNAME`ファイルの内容を書いておけば良いです。
`/static/`ディレクトリの内容は、生成時に公開ディレクトリ直下にそのままコピーされます。

というか、[Host on GitHub | Hugo](https://gohugo.io/hosting-and-deployment/hosting-on-github/#use-a-custom-domain)にめちゃくちゃ丁寧に書いてありました。ちゃんと読まないのが悪い･･･。n敗目。
