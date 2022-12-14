---
title: "Rustでもグローバル変数"
date: 2022-12-12T00:00:00+09:00
description: "Version 1.63から使えるグローバル変数について"
tags: ["Rust"]
draft: false
---

これは、[RICORA Advent Calendar 2022](https://adventar.org/calendars/7447) の **21日目** の記事です。

Rust 1.63から使えるようになった、**const** な `Mutex::new()` についてです。

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">Another thing I&#39;m very excited about, is that Mutex, RwLock and Condvar now all have a _const_ new function.<br><br>This means you can now have a static Mutex without having to use lazy_static or once_cell. ✨<br><br>3/16 <a href="https://t.co/M9X71vmZ0j">pic.twitter.com/M9X71vmZ0j</a></p>&mdash; Mara Bos (@m_ou_se) <a href="https://twitter.com/m_ou_se/status/1557743601761013762?ref_src=twsrc%5Etfw">August 11, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>