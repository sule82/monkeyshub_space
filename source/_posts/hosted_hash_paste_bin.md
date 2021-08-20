---
title: Hashbin [#]
date: 2020-04-13 17:10:10
updated: 2020-04-13 17:10:10
categories:
  - Projects
tags:
  - opensource
  - pastebin
  - hosted
---

<a href="opensource_projekat_radio_gracanica" aria-label="pročitaj" title="Hashbin [#]">{% img /covers/hashbin.webp '"Hashbin [#]" "Hashbin [#]"' %}</a>

<!--more-->

HashBin je paste bin koji nikada ne vidi svoj zaljepljeni (paste) sadržaj.

Šta je "hash" URL-a, i zašto je specifičan? Hash je dio linka koji počinje sa `#` simbolom, npr:

```jinja #
http://webstranica.ba#hash-unos
```

A evo zašto je specijalan:

Kada agent (npr web browser) traži web resurs od web servera, agent šalje URI prema serveru, ali ne šalje [hash] `#`.

{% blockquote Wikipedia http://en.wikipedia.org/wiki/Fragment_identifier Fragment Identifier %}
Wikipedia
{% endblockquote %}

Tako da ga možete koristiti za djeljenje zaljepaka bez da im trakeri uđu u trag.

Na linku dole se nalazi hashbin hosted na mom serveru, i slobodni ste ga koristiti pod [Creative Common licenci](https://creativecommons.org/licenses/by-nc-sa/4.0/):

<p class="centar">{% btn https://static.monkeyshub.space/apps/hashbin, &nbsp;hashbin.space, icon fa fa-hashtag fa-fw, hashbin.space %}</p>
