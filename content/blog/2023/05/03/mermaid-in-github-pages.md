---
title: "MermaidをGithub Pagesでも使う"
date: 2023-05-03T20:15:08+09:00
description: "MermaidをGithub Pagesでも使う"
tags: ["GitHub Pages", "Mermaid"]
draft: false
---

GitHub PagesでもMermaidが使いたくなったので備忘録。単刀直入には`<header>`に以下を追加。

```html
<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
  mermaid.initialize({ startOnLoad: false });
  await mermaid.run({
    querySelector: 'pre > code.language-mermaid', // GitHub Flavored Markdownに合わせる
  });
</script>
```

詳しくは、[Using mermaid.run](https://mermaid.js.org/config/usage.html#using-mermaid-run) に書いてあるが簡単に理解を残しておくと･･･

```js
  mermaid.initialize({ startOnLoad: false });
```

初期化時に自動で`mermaid.run()`を走らせないようにしています。

```js
  await mermaid.run({
    querySelector: 'pre > code.language-mermaid', // GitHub Flavored Markdownに合わせる
  });
```

手動で`mermaid.run()`させるときに、オプションとして変換すべきタグがデフォルトの`pre.mermaid`ではなく`pre > code.language-mermaid`なのを伝えています。
`querySelector`形式で伝えられるのは便利ですね！
