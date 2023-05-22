---
title: "透過ウィンドウの裏にあるChromeが動画を描画しないときの対処法"
date: 2023-05-23T00:28:37+09:00
description: "Windows Terminalを透明にして裏でYouTubeでも見ようとしたら描画されなかったのでフラグを切り替えて対処した話"
tags: ["Tips", "Chrome"]
draft: false
---

Windows Terminalを透明にしてYouTube見ながらプログラム書いたら最高じゃん！と思ったが、Chromeが無情にも真っ黒な再生画面を提示してくれたため。

1. `chrome://flags`を開く。
2. 「Calculate window occlusion on Windows」というフラグがあるので**Disabled**に切り替えてChromeを再起動する。

## 参考

1. [Google Chromeでバックグラウンドにするとゲームが止まる現象 &#8211; ミミブロ](https://slither-io.jp/%E3%81%9D%E3%81%AE%E4%BB%96/2202031547/)
