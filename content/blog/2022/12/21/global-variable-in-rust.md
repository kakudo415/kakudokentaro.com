---
title: "Rustでもグローバル変数"
date: 2022-12-21T00:00:00+09:00
description: "Version 1.63から使えるグローバル変数について"
tags: ["Rust"]
draft: false
---

これは、[RICORA Advent Calendar 2022](https://adventar.org/calendars/7447) の **21日目** の記事です。

昨日は、**remeta**さんの『[またね、サンタさん / Goodbye, Santa](https://speakerdeck.com/000meta/goodbye-santa)』でした。

今日は、Rust 1.63から使えるようになった、**const** な `Mutex::new()` についてです。

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">Another thing I&#39;m very excited about, is that Mutex, RwLock and Condvar now all have a _const_ new function.<br><br>This means you can now have a static Mutex without having to use lazy_static or once_cell. ✨<br><br>3/16 <a href="https://t.co/M9X71vmZ0j">pic.twitter.com/M9X71vmZ0j</a></p>&mdash; Mara Bos (@m_ou_se) <a href="https://twitter.com/m_ou_se/status/1557743601761013762?ref_src=twsrc%5Etfw">August 11, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## これまでのグローバル変数

1.62以前のRustでは、`lazy_static` や `once_cell` を利用して可変なグローバル変数を作っていました。

```rust
use once_cell::sync::Lazy;
static GLOBAL: Lazy<Mutex<Global>> = Lazy::new(|| Mutex::new({ ... }));
```

## これからのグローバル変数

新しい書き方について知る前に、まずはRustの`const`文脈について知る必要があります。

### `const` 文脈とは

Rustの式・変数・定数には、コンパイル時に値が決定されなくてはならないものがいくつかあります。
具体的には以下に示した通りで、グローバル変数を実現するために必要な static 変数も該当します。

- 配列の長さ部分 `[型; 長さ]`
- 配列の繰り返し回数部分 `[要素; 繰り返し回数]`
- 以下のものを初期化する式
  - 定数
  - static 変数
  - enum 型の定数部分(descriminant)
- ジェネリック定数変数 `fn f<const N: isize>()`の`N`に渡されるもの

詳しくは、[Constant Evaluation - The Rust Reference](https://doc.rust-lang.org/reference/const_eval.html#const-context)

### `const fn` について

そのため、実行時に評価される通常の関数はグローバル変数の初期化には使えません。

そこで登場するのが`const`文脈で呼び出せる`const fn`です。
一定の制限をクリアすることでコンパイル時に実行してくれるため、`const`文脈でも使えるわけです。
コンパイル時に実行して困ることはあまりないため、標準ライブラリの関数たちも順次`const fn`に書き換えが進んでいるようです。

### 新しい `Mutex::new()`

そして、Rust 1.63から遂に`Mutex::new()`も`const fn`となりました！

これによってstatic変数の初期化が可能になったため、以下のようにグローバル変数を簡単に、かつ安全に書くことができるようになったわけです。

```rust
static GLOBAL: Mutex<Global> = Mutex::new( ... );
```

## 終わりに

一瞬で3年生も終わりとなり、もうOBと呼ばれる学年になったことに戦慄しています。
学年間の垣根が非常に低いRICORAですが、かと言ってうざったい先輩にならないように気をつけていきます。

明日は**unagi_dog**さんの『OAuth2.0まとめ』が予定されていますのでお楽しみに！

<p class="warn">
  <strong>2022-12-22追記</strong><br>
  『OAuth2.0まとめ』は24日に引っ越しになり、同じく<strong>unagi_dog</strong>さんの『<a href="https://miso.sabanium.me/post/throwback2022/">ふりかえり2022</a>』が公開されていますので是非ご覧ください！（サイトのデザインを褒めてくれてありがとう）
</p>
