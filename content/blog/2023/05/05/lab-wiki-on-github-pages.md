---
title: "研究室のWikiはMarkdownをGitHubに置くのがよさげ、Pagesで見られるし！"
date: 2023-05-05T11:50:55+09:00
description: "研究室のWikiをMarkdown直置きのGitHub Pagesで作ったらなかなか便利そうだったという日記"
tags: ["Diary", "GitHub Pages"]
draft: false
---

今から研究室を決めようとする学部生にとって、研究室の雰囲気を外から知ることは困難です。先輩から漏れ伝わってくるわずかな情報と、更新が途絶えがちなホームページから希望を決めなくてはなりません。

配属された学部4年生にとっても、研究室にどんな知見があるのか知るのは困難です。運良くすぐ上の先輩方が知っていればいいですが、知っているはずの先輩はついこの前卒業しちゃった、ということがよく起こります。口伝に頼っているから起こる現象です。

去年の夏に配属された僕の研究室もご多分に漏れず、知っている先輩に聞くことだけが情報を引き継ぐ手段となっています。そこで、研究室のWikiを作成することにしました。

## GitHubを選んだ理由

実は、弊研究室（学部生の段階でへりくだるのは逆に偉そうでしょうか？）には、もともとWikiがあったようです。ホームページに内部向けページとしてリンクが掲載されています。
しかし、今では誰もアクセスできません。ハードディスクが壊れてしまったのです。

大学、しかも計算機の研究室なのですから、オンプレミスのサーバーを持って然るべきなのかもしれませんが、機器の保守は誰だって大変です。外部に任せてしまうのが簡単です。

世の中にはさまざまなWiki・ブログシステムがありますが、新たなプラットフォームの使い方を覚える必要ができると途端に誰も使わなくなるでしょう。
そこで、（おそらく、また願わくば）みんなが最も馴染みのあるプラットフォームで管理するのがよいと考え、GitHub上に情報をまとめることにしました。

## 静的サイトライブラリを使うか否か

Hugo等の静的サイトライブラリを使えば、リッチなWebサイトを作ることもできます。しかし、これも保守の問題がでてきます。残念ながら毎年Webに明るい4年生が入るとは限らず、何かの拍子にレイアウトが崩れた、といった状況ではWikiそのものが機能しなくなります。

そのため、情報をそのままの形で置くことを優先します。
そうすれば、Web側に問題があっても、GitHubのリポジトリがそのままWikiの役割を果たし、情報共有が続けられるためです。Markdownで書くと、何も設定せずともGitHub上で悪くない見た目になるのもいいところですね。

## 設計・構成

さて、ここまでで

- GitHubにMarkdownファイルを置く
- 簡単なWebサイトとして公開する

といった要件が決まりました。
GitHub Pagesを使えばよさそうです。ありがたいことに、MarkdownをHTMLに変換するところまでやってくれます。

### Jekyllの設定は要る

静的サイトライブラリを使わないとは言いましたが、GitHub Pagesを使う以上、Jekyllと無縁ではいられません。なるべく最小限に、次の内容を設定しました。

#### タイトル

デフォルトで使用されるJekyll themeの[Primer](https://github.com/pages-themes/primer)では、サイトとページのタイトルが一致したとき以外はクソデカタイトルヘッダを表示するようになっています（`_layouts/default.html`を見るとわかります）。

リポジトリ名がサイトタイトルとして利用される関係上、大文字小文字が一致せず、このままでは常にクソデカタイトルヘッダが表示されてしまいます。
そこで、`_config.yml`でタイトルを設定することで回避しています。

#### MathJaxとMermaid

GitHub.comとGitHub Pagesでは同じ見た目でも環境が違い、PagesにはMathJaxやMermaidといったライブラリが入っていません。
そのため、別途ヘッダで読み込んであげる必要があります。

またもやPrimerを調べてみると、`_includes/head-custom.html`を読み込んでくれることがわかるので、ここに次のように書きます。

```html
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
  mermaid.initialize({ startOnLoad: false });
  await mermaid.run({
    querySelector: 'pre > code.language-mermaid', // GitHub Flavored Markdownに合わせる
  });
</script>
```

## できたもの

[TakiWiki3](https://takimotolab.github.io/takiwiki3/)

[TakiWiki3 リポジトリ](https://github.com/takimotolab/takiwiki3)

見慣れたデザインのWebページを作ることができました。
GitHubリポジトリにはそのままの形でMarkdownが置いてあるので、簡単に編集することもできます。
研究室メンバーに布教して、散らばった情報をまとめていきたいと思います。
