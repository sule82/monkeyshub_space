---
title: Radio Gračanica Live Stream
date: 2020-02-09 20:01:00
updated: 2020-05-02 17:20:00
categories:
  - Projects
tags:
  - stream
  - web sajt
  - opensource
---

<a href="opensource_projekat_radio_gracanica" aria-label="pročitaj" title="Radio Gračanica Live Stream">{% img /covers/radiostream.webp '"Radio Gracanica" "Live Stream"' %}</a>

<!--more-->

# C Player

Ovdje pokreni i slušaj <i class="fa fa-music"></i> Radio Gračanica stream.

<div class="centar">
{% cplayer %}
- name: Radio Gracanica Live
  poster: https://static.monkeyshub.space/fragments/sky/rg-logo.png
  src: http://185.69.28.214:1234/broadwave.mp3
{% endcplayer %}
</div>

**Source**:

```
ip: 185.69.28.214
port: 1234
```

**Format**: <span style="color:var(--link-color)" aria-label="mp3"><i class="fa fa-file-audio"></i></span> .mp3

{% note default %}
**Radio Gračanica** zapravo nema online radio, nego zemaljski signal konvertuje u broadwave i dijeli preko otvorene lokalne adrase bez ssl certifikata.
{% endnote %}

# Backup Web Player

React <span style="color:var(--link-hover-color)" aria-label="mp3"><i class="fab fa-react"></i></span> nativni sićušni, <span aria-label="laganini"><i class="fa fa-feather"></i></span> lagani app sa HQ streamom testirajte na linku:

<div class="centar">{% button http://rgstream.monkeyshub.space/, &nbsp;backup web player, icon fa fa-microphone-alt, radio gracanica %}</div>

# Stream na official sajtu

<div class="centar">{% button https://radiogracanica.ba/uzivo/, &nbsp;oficijelni web sajt, icon fa fa-volume-up, radio gracanica %}</div>
