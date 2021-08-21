---
title: Highlight JS
date: 2020-03-10 12:01:00
updated: 2020-03-10 12:01:00
categories:
  - Resources
tags:
  - kod
  - js
  - opensource
---

<a href="/opensource_resursi_highlightjs" aria-label="pročitaj" title="Highlight JS" data-pjax-state="">{% img /covers/highlightjs.webp '"highlight.js" "highlight.js"' %}</a>

<!--more-->

# Upotreba

## node.js & hexo.js

**npm** instalacija & **yml** struktura

```sh
$npm install highlight.js
# ili
$yarn add highlight.js
```

```yml
#_hexo config.yml
highlight:
  enable: true
  theme:
    light: routeros
    dark: tomorrow-night-eighties
```

## javascript injekcija

```html
<link rel="stylesheet" href="/path/to/styles/default.css">
<script src="/path/to/highlight.min.js"></script>
<script>hljs.highlightAll();</script>

```

```html
<pre><code class="language-html">...</code></pre>
```

# Fetch preko CDN-a

```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.1.0/styles/default.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.1.0/highlight.min.js"></script>
<!-- and it's easy to individually load additional languages -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.1.0/languages/go.min.js"></script>

```

# Lokalni cheatsheet sa pregledom

Brzi pregled tema za {% label success@highlight.js %} *(highlighter sintaksi za web)* i `kod` za implementaciju u **hexo.js**:

<p class="centar">{% button /highlight, &nbsp;highlight.js cheetsheet, icon fa fa-paint-bucket, highlight js %}</p>

# Oficijela Stranica

<p class="centar">
{% button https://highlightjs.org/, &nbsp;highlight.js official web sajt, icon fa fa-paint-brush, highlightjs.org %}
{% button https://highlightjs.readthedocs.io/en/latest/, &nbsp;highlight.js dokumentacija, icon fa fa-atlas, highlight js docs %}
{% button https://github.com/highlightjs/, &nbsp;highlight.js source code, icon fa fa-github, highlight js source %}
</p>
