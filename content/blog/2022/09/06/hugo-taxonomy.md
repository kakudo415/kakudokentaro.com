---
title: "HugoのTaxonomyを設定する"
date: 2022-09-06T01:12:54+09:00
description: "HugoのTaxonomy（タグ・カテゴリ）について。設定や無効化の方法。"
tags: ['Web', 'Hugo']
draft: false
---

ブログを書き始めて6日。だんだんHugoの気持ちがわかってきました。そろそろTaxonomy（分類）も触れたいなと感じたので、このブログに「タグ」を導入したいと思います。

## タグを設定する

### ArchetypesにデフォルトのFront Matterを書く

`/archetypes/`内にテンプレートを置いておくことで、新しい記事を作成したときにテンプレートの内容が原稿にコピーされて便利です。

```yaml
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
description: ""
tags: []
---



```

### config.tomlの編集

[Custom taxonomy under section? - support - HUGO](https://discourse.gohugo.io/t/custom-taxonomy-under-section/12905/13)が非常に参考になりました。ありがとうございます。

以下のように追記します。今回は、`/blog/`下でタグを管理したかったので、パーマリンクをイジっています。  

```toml
[taxonomies]
  tag = 'tags'

[permalinks]
  '/tags' = '/blog/tags/:slug'
```

#### taxonomies

`内部的な分類方法名 = パス等に使うときの分類方法名`という形で設定します。（`単数形 = 複数形`が想定されている？）

参考: [Taxonomies | Hugo](https://gohugo.io/content-management/taxonomies#configure-taxonomies)

#### permalinks

デフォルトだと`/tags/`下でタグ関係のページが公開されてしまうので、それを置き換えています。

参考: [URL Management | Hugo](https://gohugo.io/content-management/urls/#permalinks)

### 記事ページでのタグ

記事ページ（`single.html`）内では、`.Params.tags`にその記事が持つタグが全て入っています。

```html
  {{ range .Params.tags }}
    <li><a href="{{ $.Site.BaseURL }}blog/tags/{{ . | urlize}}">{{ . }}</a></li>
  {{ end }}
```

このブログでは現在以上のようになっています。組み込みの`urlize`関数が便利ですね。

参考: [Single Page Templates | Hugo](https://gohugo.io/templates/single-page-templates/)

### タグを持つ記事一覧

`taxonomy.html`内の`.Pages`にそのタグを持つ記事が全て入っています。

```html
{{ range .Pages }}
  <article>
    <h4><a href="{{ .Permalink }}">{{ .Title }}</a></h2>
    <time datetime="{{ .Date.Format "2006-01-02" }}">{{ .Date.Format "2006-01-02" }}</time>
  </article>
{{ end }}
```

このブログでは現在以上のようになっています。

### タグ一覧

`terms.html`内の`.Site.Taxonomies.tags`にタグについて情報が入っています。今回は、`.ByCount`を使って、記事数に応じて並び替えています。

```html
{{ range .Site.Taxonomies.tags.ByCount }}
  <article>
    <h4><a href="{{ $.Site.BaseURL }}blog/tags/{{ .Name | urlize }}/"># {{ .Name }} ({{ .Count }})</a></h2>
  </article>
{{ end }}
```

## Taxonomyの完全・部分的な無効化

タグ等のTaxonomyを使わない場合は無効化することができます。
無駄なページが生まれるだけなので、必要ないなら切ってしまっても良いかと思います。

### 完全な無効化

以下の設定で、Taxonomy機能自体を無効化できます。

```toml
disableKinds = ['taxonomy']
ignoreErrors = ["error-disable-taxonomy"]
```

単に`disableKinds`に追加すればよいかと思いきや、

```
ERROR 2022/09/06 00:56:59 You have the value 'taxonomy' in the disabledKinds list. In Hugo 0.73.0 we fixed these to be what most people expect (taxonomy and term).
But this also means that your site configuration may not do what you expect. If it is correct, you can suppress this message by following the instructions below.
If you feel that this should not be logged as an ERROR, you can ignore it by adding this to your site config:
ignoreErrors = ["error-disable-taxonomy"]
```

と怒られてしまうので、言われた通りに`ignoreErrors = ["error-disable-taxonomy"]`を追加しています。

### 部分的な無効化

Categoryだけ要らないといった場合は、デフォルトの設定を上書きすることで無効化することができます。

```toml
[taxonomies]
  tag = 'tags'
```

たとえば、このブログの設定はこのようになっていますが、デフォルトであるはずの`category`を書かなかったため、その機能が無効化されています。

[Taxonomies | Hugo](https://gohugo.io/content-management/taxonomies#example-removing-default-taxonomies)も併せてご覧ください。
