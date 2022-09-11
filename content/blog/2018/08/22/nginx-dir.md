---
title: "nginxでrewriteを使わずにディレクトリ構造にするproxy_pass"
date: 2018-08-22T00:00:00+09:00
tags: ["Former Blog Post", "Web", "nginx"]
draft: false
---

<p class="warn">
  <strong>この記事は以前のブログから引っ越してきたものです！</strong><br>
  <a href="https://github.com/kakudo415/blog">以前のブログのGitHubリポジトリ</a>
</p>

こんにちは、今回はこのブログのようにドメインの後ディレクトリを挟んでアプリケーションを動かすときにrewriteを使わない方法をご説明します  

## locationで正規表現を使う
locationを指定するところで下のようにチルダ等を入れることで単に前方一致ではない正規表現を使うことができます  
<pre><code>location = 完全一致 {}  
location ^~ 正規表現(マッチしたら検索終了) {}  
location ~ 正規表現 {}  
location ~* 正規表現(大文字小文字区別しない) {}</code></pre>

## マッチした文字列を変数にする
<pre><code>location ~ (?&lt;変数名&gt;正規表現) {}</code></pre>

このように変数名を指定して正規表現を使いかっこで括ることでlocation内で使える変数を作ることができます（変数名は既存のものと被らないように）  
そしてこのように書くことで  
<pre><code>location ~ /dir/(?&lt;path&gt;.*) {
	proxy\_pass http://localhost:12345/$path$is\_arg$args;
}</code></pre>

**$path** には/dir/以下のURLが入り、  
**$is\_args** にはクエリがある場合に ? が入り、  
**$args** にクエリが入るので、正しく/dir/を飛ばしてリバースプロキシできるわけですね  

## 最後に(rewriteとの比較)
自分の環境ではrewrite時にPOSTするとうまくいかないところがあったので他のやり方を探したところこれが良さそうだと思いました 
