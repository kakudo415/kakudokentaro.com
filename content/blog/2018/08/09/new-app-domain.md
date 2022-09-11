---
title: "強制HTTPSのAPPドメインでLet's Encryptを活用しよう！"
date: 2018-08-09T00:00:00+09:00
tags: ["Former Blog Post"]
draft: false
---

<p class="warn">
  <strong>この記事は以前のブログから引っ越してきたものです！</strong><br>
  <a href="https://github.com/kakudo415/blog">以前のブログのGitHubリポジトリ</a>
</p>

こんにちは！今日は自分の新しいドメイン [kakudo.app](//kakudo.app) についてです  
## ほかのドメインと何が違うのか？
.appは今年(2018)の5月から運用が開始されたGoogle謹製の新作TLDです  
Googleが当時過去最高の2500万ドル(約30億円)で競り落としたことで話題にもなりました  
しかし、appドメインが他と違うのは落札額だけではありません  
サイトをHTTPS化するのが **強制** です(HTTP Strict Transport Security)  
.appドメインの使用が広がったり、同じようなHTTPS強制のドメインが増えれば、さらにWebの暗号化が進むと思われます  
## Let's Encryptとは？
Let's Encryptとは、HTTPS接続をするために必要なSSL証明書を無料で発行してくれる機関です  
簡単な手続きで今あるサイトをHTTPS化することができます  
今回はこのLet's Encryptの力を借りて強制HTTPSに対応したいと思います  
## 自分の環境
- Ubuntu 18.04 LTS
- nginx (Let's Encryptの使用にはWebサーバーが必要です 他のWebサーバーも可)
- letsencrypt (aptでインストールしておいてください)

## やってみる
### Let's Encryptのアクセスへの準備
まずは通常の通常のHTTPで認証用のアクセスを受け付ける準備をします(/etc/nginx/conf.d/hogehoge.conf内に追記)
<pre><code>server {
	listen 80;
	listen [::]:80;  
	server\_name kakudo.app;  
	location / {
		return 301 https://$server\_name$request\_uri;
	}  
	location /.well-known/acme-challenge/ {
		root /var/www/letsencrypt;
	}
}</code></pre>

この次に誤字脱字などが無いか、テストコマンドを実行します
<pre><code>sudo nginx -t</code></pre>

成功したら次のコマンドでnginxを再読み込みします(エラーが出たら間違いがないか確認してください)
<pre><code>sudo nginx -s reload</code></pre>

### コマンド実行で証明書を発行する
<pre><code>sudo letsencrypt certonly --webroot -w /var/www/letsencrypt/ -d kakudo.app</code></pre>

.appは無印HTTPを受け付けないと聞いていたので（Googleの中の人もそういってた、詳細は後述）  
ここはエラーが出てその解決策をブログのネタにしようかと企んでいたのですが、あっさり通りました  
なので、「.appドメイン取ってみたいけど証明書発行めんどくさいんちゃうん？」と思っている方。発行手順は他のドメインと同じ模様です  
思っていた以上に無事発行できてしまったので、これをnginxに設定します  

### できた証明書を設定しよう
さっきの **hogehoge.conf** 内の **server {}** の外に以下を追記してください
<pre><code>server {
	listen 443 ssl http2;
	listen [::]:443 ssl http2;  
	server\_name kakudo.app;  
	ssl\_certificate /etc/letsencrypt/live/kakudo.app/fullchain.pem;
	ssl\_certificate_key /etc/letsencrypt/live/kakudo.app/privkey.pem;  
	# お好みで location などの設定を追加してください
}</code></pre>

この後テストコマンドと再読み込みをすれば設定は完了です  
お疲れさまでした  

## 【余談】Googleの中の人にappドメインについて聞いてみた
中の人ってすごいパイプがあるように聞こえる便利な言葉ですねｗ  
要は問合せメールを送らせていただいただけなのですが、要約すると次のような質問と返答でした  

*自分「.appドメインはHTTPS必須って書いてあるけどSSHとかでは使えないんですか？」*  

これを聞いたのは、.appというドメインだったので単にWebアプリだけでない使い方もできるのではと思ったためです  
そして、1日後帰ってきた答えがこちらです  

*中の人「HTTPS以外は使えないです、でもテクニカルサポートまでリクエストを送ったのでまだ確約できないです」*  

つまり、もうちょっと技術的な人にさらに又聞きしていただいているということみたいです  
なので後日さらに回答いただけたら追記したいと思います  

ただ現時点で分かっていることがあって、少なくとも **SSHは使えます**  
試してみたら普通に使えましたね  

[HSTS Wikipedia](https://ja.wikipedia.org/wiki/HTTP_Strict_Transport_Security)  
ここを読むに、ブラウザがドメインを判別してHTTPで接続できないようにする仕組みのようです(間違えてたら [Twitter](https://twitter.com/kakudo415/)とかに教えて頂けると嬉しいです)  
なのでそもそもブラウザを介さないSSHなどは普通に使えるのではないかと思います  

## (ドメインを)換装した感想
(逃したネタは大きいけど)めっちゃ設定は簡単でしたね！  
appドメインは見た目が何となく好きなので今後はこっちメインに使うかもしれません  
通信を暗号化するのはユーザーにとっても利益が大きいので、未対応の方は設定してみてはいかがでしょうか？  
