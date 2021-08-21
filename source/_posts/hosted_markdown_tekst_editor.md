---
title: Markdown tekst editor
date: 2020-06-09 16:09:00
updated: 2020-06-09 16:09:00
categories:
  - Projects
tags:
  - tekst
  - opensource
  - hosted
---

<a href="/hosted_markdown_tekst_editor" aria-label="pročitaj" title="Markdown tekst editor" data-pjax-state="">{% img /covers/editormd.webp '"MD tekst editor" "MD tekst editor"' %}</a>

<!--more-->

## Opensource

<i class="fa fa-cat"></i> Tekst editor, sa markdown jezičnom sintaksom, sa {% label default@flowChart, html tagovima & emoji ikonama %} :computer:, prilagođen uređivanju ovog bloga <span role="img" aria-label="ikona">:orange_book:</span>.

<span role="img" aria-label="ikona"><i class="fa fa-scroll"></i></span> Browser {%label default@desktop size %} verziju možete pronaći na linku dole i koristiti prema *CC-BY-NC-SA* licenci<a href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img src="/images/cc-by-nc-sa.svg" aria-label="licenca" title="Creative Common Attribution-NonCommercial-ShareAlike 4.0" /></a>
<div class="centar">
{% btn https://static.monkeyshub.space/apps/editormd/, &nbsp;Markdown tekst editor, fa fa-pen fa-fw, Markdown tekst editor %}
</div>

___

Editor {% label info@ne pohranjuje %} fajl na server, <kbd>Ctrl</kbd>+<kbd>c</kbd> & <kbd>Ctrl</kbd>+<kbd>v</kbd>, snimi u lokalni fajl <kbd>Ctrl</kbd>+<kbd>s</kbd>.

## Screenshot

{% gp 3-2 %}
<picture>
  <source srcset="/images/md_editor.webp" media="(max-width: 100%)" type="image/webp" />
  <source srcset="/images/md_editor.png" media="(max-width: 100%)" type="image/png" />
  <img src="/images/md_editor.webp" title="md_editor" label="md_editor" />
</picture>
<picture>
  <source srcset="/images/md_2editor.webp" media="(max-width: 100%)" type="image/webp" />
  <source srcset="/images/md_2editor.png" media="(max-width: 100%)" type="image/png" />
  <img src="/images/md_2editor.webp" title="md_editor" label="md_editor" />
</picture>
<picture>
  <source srcset="/images/md_3editor.webp" media="(max-width: 100%)" type="image/webp" />
  <source srcset="/images/md_3editor.png" media="(max-width: 100%)" type="image/png" />
  <img src="/images/md_3editor.webp" title="md_editor" label="md_editor" />
</picture>
{% endgp %}

## DCode
{% codeblock lang:markdown %}

# Heading <sup>1</sup> :fa-book:
Tekst editor, sa <mark>markdown</mark> jezičnom sintaksom, sa `flowChart`,vlastitim tagovima & prilagođenim emoji ikonama; :tw-1f412: :tw-1f338: :tw-1f34c:
  > - koristio se za uređivanje hexo.js bloga

<div style="text-align:center;">
![slika](https://static.monkeyshub.space/fragments/monkey/icongrey.png)<br>
![slika](https://static.monkeyshub.space/fragments/monkey/2560x1600.jpg)
</div>
___

**bold** *cursive* <small>small</small>
```
<span>:emoji-name:</span>
:tw-code:
:fa-name:
```
+++  [externi link](https://static.monkeyshub.space/apps/smajlici/ "Smile") :fa-external-link:

#### Tabela

|:---:| :------------- | -----: |
| Put | Oko Svijeta |
| :fa-truck: 1 2  :tw-33-20e3: :tw-34-20e3: :tw-35-20e3: | :fa-rocket: -----> :tw-1f310: |
| <small>za 345 dana</small> ? | :fa-star: :fa-truck: |

#### FlowChart

flowChart : `true`

```flow
st=>start: korisnik
op=>operation: autentifikacija
cond=>condition: uspjesna Da ili Ne?
e=>end: into admin

st->op->cond
cond(yes)->e
cond(no)->op
```
### Video multi source

<media>
<video width="100%" height="100%" controls loop muted >
 <source src="https://static.monkeyshub.space/motions/wiracnet_teaser.webm" type="video/webm">
 <source  src="https://static.monkeyshub.space/motions/wiracnet_teaser.mp4" type="video/mp4">
<source src="https://static.monkeyshub.space/motions/wiracnet_teaser.mkv" type="video/mkv">
</video>
<iframe width="100%" height="254" src="https://www.youtube.com/embed/8AfXlAZracw" frameborder="0" allowfullscreen></iframe>
</media>

> The End

{% endcodeblock %}
