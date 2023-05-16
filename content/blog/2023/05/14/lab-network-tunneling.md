---
title: "研究室PCにCloudflare TunnelsでSSHする"
date: 2023-05-17T03:22:26+09:00
description: "学内ネットワークの研究室PCに、Cloudflare Tunnelsを利用してSSHした備忘録"
tags: ["Network", "Cloudflare"]
draft: false
---

研究室に置いてある自分のPCに外部からSSHしたいとき、ありますよね？　私はあります。

しかし、セキュリティの都合か、学内ネットワークはたいてい外部に公開されていません。ファイアウォール以前にグローバルIPアドレスを持ってさえいないわけです。僕もその壁を前に途方に暮れていたんですが、ふと呟いてみたところ次のような返信をいただきました。

<blockquote class="twitter-tweet" data-conversation="none" data-dnt="true" data-theme="light"><p lang="ja" dir="ltr">cloudflare tunnelというまさしくなツール・サービスがあります<a href="https://t.co/vCBbaWdJgC">https://t.co/vCBbaWdJgC</a></p>&mdash; とりのマンボウ (@flat35hd99) <a href="https://twitter.com/flat35hd99/status/1656943423558148098?ref_src=twsrc%5Etfw">May 12, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

この返信がドンピシャで、完璧な解決案でした。ありがとうございます！
本記事では、設定手順と過程で学んだことを書き残しています。

## 設定手順

以下の手順はあくまで備忘録です。次のような公式ドキュメントが存在しますので、詳しくはそちらをご覧ください。

- Tunnelの設定方法など: [Via the command line · Cloudflare Zero Trust docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/tunnel-guide/local/)
- SSH接続を利用するときの設定方法など: [SSH · Cloudflare Zero Trust docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/use-cases/ssh/)

ここでは仮に`example.com`をCloudflareのDNSで運用しているものとし、`mylab.example.com`にアクセスすることで研究室PCにSSH接続できるようにします。適宜お持ちのドメインに読み替えてください。

### `cloudflared`のインストール

まずは接続元・接続先PC共に`cloudflared`をインストールし、ログインします。

OSごとのインストール方法は[Downloads · Cloudflare Zero Trust docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/)をご覧いただくとして、macOSの場合は次のようになります。

```sh
brew install cloudflared
```

インストールできたらログインします。

```sh
cloudflared tunnel login
```

Webブラウザが立ち上がった方はそこから、CUI環境の方は表示されるURLをコピペしてCloudflareにログインします。どのドメイン下にTunnelを作るか聞かれるので、お好きなものを（この記事では`example.com`を）答えてください。

### 研究室PC側（Tunnel作成）

```sh
cloudflared tunnel create <TUNNEL_NAME>
```

トークンなどの認証情報が書かれた`~/.cloudflared/<TUNNEL_ID>.json`ができます。

そうしたら、`~/.cloudflared/config.yml`に次のような設定を追加します。

```yml
url: ssh://localhost:22
tunnel: <TUNNEL_ID>
credentials-file: <<TUNNEL_ID>.json への絶対パス>
```

次に、DNSの設定をします。これでプライベートネットワーク外からは`mylab.example.com`にアクセスすることで研究室PCにアクセスすることができるようになります。

```sh
cloudflared tunnel route dns <TUNNEL_ID> mylab.example.com
```

これでTunnelの設定は完了です。`cloudflared`を再起動して設定を適用します。

```sh
cloudflared service restart
```

公式ドキュメントによるとこれで再起動されるはずですが、自分の環境では`Register tunnel error from server side error="Unauthorized: Failed to get tunnel"`というエラーが出て上手くいかなかったため次のように`service`を再インストールしました。

```sh
sudo cloudflared service uninstall
sudo cloudflared --config <config.jsonへの絶対パス> service install
```

再起動、ないし再インストールを行うことで、研究室PCとCloudflareがTunnelで接続されます。

### 手元のPC（SSH設定）

`~/.ssh/config`を次のように編集して、Cloudflare経由でSSH接続するように設定します。

```
Host <NICKNAME>
    Hostname mylab.example.com
    User <USERNAME>
    ProxyCommand <cloudflaredへの絶対パス> access ssh --hostname %h
```

### 接続方法

設定名だけで接続できるのを初めて知りました。

```sh
ssh <NICKNAME>
```

これでプライベートネットワーク内の研究室PCに外部からSSH接続できます！めでたい！便利！


## Cloudflare Tunnelの仕組み

TODO: そのうち勉強して書く

## SSH ProxyCommandとは

TEACHME: 結局どういう風に動作しているのか全然わからん。
そもそも、このProxyCommandは手元で実行されているという理解でよい？

## 終わりに

Cloudflareはどうやって儲けてるんですか？

## 参考にしたもの

1. [Downloads · Cloudflare Zero Trust docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation/)
1. [Via the command line · Cloudflare Zero Trust docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/tunnel-guide/local/)
2. [SSH · Cloudflare Zero Trust docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/use-cases/ssh/)
3. [proxy - How does ssh ProxyCommand actually work? - Server Fault](https://serverfault.com/questions/544545/how-does-ssh-proxycommand-actually-work)

