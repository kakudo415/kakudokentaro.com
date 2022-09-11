---
title: "禁忌かもしれない未読マークと、消えないCSS疑似要素"
date: 2022-09-03T06:38:53+09:00
draft: false
tags: ['Web', 'CSS']
---

## 未読マークの魔力

未読マークってどうしてあんなに消したくなるんでしょうか。特に用のないアプリ、今読まなくてもよいメールでも、未読マークのためについつい一度開いたりしてしまいます。

整然と並んだ「既読たち」の中に、目立つはみ出し者の「未読」がいると気持ち悪くなる人間の習性でもあるのかもしれません。

さて、おもしろそうだったのでそんな未読マークを実装してみました。まだお読みでない記事があったら[ブログ記事一覧](/blog/)を見てみてください。このように青い点が未読記事の右に表示されているはずです。（2022-09-11追記: 邪魔だったので廃止しました）

![未読マーク](../unread-marker.png)

実装といっても、既読かどうかはブラウザが判断してくれるので簡単です。

```css
a.unread-marker {
  display: flex;
  align-items: center;
}

a.unread-marker::after {
  content: "";
  display: block;
  height: 0.5rem;
  width: 0.5rem;
  margin-left: auto;
  flex-shrink: 0;
  border-radius: 50%;
  background: #0077C8;
}

a.unread-marker:visited::after {
  display: none;
}
```

未読マークを付けたいリンクに対して、CSSの疑似要素でマークを作ればよいです。

## なぜか消えない ::after

### 起こったこと

未読マークの役割から言って、読んだ記事の横にあるマークは当然消すべきです。そこで以下のように書いているわけですが、なぜか疑似要素`::after`が消えません･･･。

```css
a.unread-marker:visited::after {
  display: none;
}
```

消す系の指定をてんこ盛りにしてもダメでした。

```css
a.unread-marker:visited::after {
  content: none;
  display: none;
  visibility: hidden;
}
```

ブラウザの開発者ツールを確認すると正常に`display`を上書きできているように見えます。

![Checking with dev tool](../checking-with-dev-tool.png)

しかし、計算済みタブを見ると、`display: none`が認識されていながら`display: block`が使われています。不思議です。

![計算済みタブでおかしな挙動になる様子](../calculated.png)

### 理由

プライバシー上の理由から、`:visited`疑似クラスでは`display`等が使えないことが原因でした。

[プライバシーと :visited セレクター - CSS: カスケーディングスタイルシート | MDN](https://developer.mozilla.org/ja/docs/Web/CSS/Privacy_and_the_:visited_selector)には、次のように書かれています。（参照: 2022-09-03）

> およそ 2010 年よりも前、 CSS の `:visited` セレクターによって、ウェブサイトがユーザーの閲覧履歴を明らかにし、ユーザーがどのサイトを訪問したかを把握することができました。これは `window.getComputedStyle` およびその他の技術を用いて行われていました。この処理はすばやく実行され、ユーザーがどこを訪れたかを特定できるだけでなく、ユーザーの身元に関する多くの情報を推測することも可能になりました。
> 
> この問題を緩和するために、ブラウザーは訪問済みリンクから受け取ることができる情報量を制限されるようになりました。

[:visited - CSS: カスケーディングスタイルシート | MDN](https://developer.mozilla.org/ja/docs/Web/CSS/:visited)によると、具体的には次のプロパティが使えます。（参照: 2022-09-03）

> 利用できる CSS プロパティは、 `color`, `background-color`, `border-color`, `border-bottom-color`, `border-left-color`, `border-right-color`, `border-top-color`, `column-rule-color`, `outline-color`, `text-decoration-color`, `text-emphasis-color` です。

### 対症療法

とりあえず、`background: #FFF`とすることで見えなくしました。ダークモード対応は遠そうです。

## 未読マークは黒魔術かもしれない

この記事を書いていて、未読マークは読む人の負荷をいたずらに上げる良くないデザインな気がしてきました。
僕の気が変わったら、デザインを変更したり、廃止したりすると思います。(2022-09-11追記: 廃止しました)
