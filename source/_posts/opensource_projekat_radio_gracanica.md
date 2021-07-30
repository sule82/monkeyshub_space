---
title: Radio Gračanica Live Stream
date: 01.02.2020 20:01:00
updated: 09.05.2020 17:20:00
categories:
  - Projects
tags:
  - stream
  - web sajt
  - opensource
---

<a href="opensource_projekat_radio_gracanica" aria-label="pročitaj" title="Radio Gračanica Live Stream">{% img /covers/radiostream.png '"Radio Gracanica" "Live Stream"' %}</a>

<!--more-->

## C Player

Ovdje pokreni i slušaj <i class="fa fa-music"></i> Radio Gračanica stream.

<div class="centar">
{% cplayer %}
- name: Radio Gracanica Live Stream
  poster: https://static.monkeyshub.space/fragments/sky/rg-logo.png
  src: http://185.69.28.214:1234/broadwave.mp3
{% endcplayer %}
</div>


{% note warning %}
**Radio Gračanica** zapravo nema online radio, nego zemaljski signal konvertuje u broadwave i dijeli preko otvorene lokalne adrase bez ssl certifikata.
{% endnote %}

## Backup Web Player

React nativni sićušni, <i class="fa fa-dove"></i> lagani app sa HQ streamom testirajte na linku:

<p class="centar">{% button http://rgstream.monkeyshub.space/, &nbsp;backup web player, icon fa fa-microphone-alt, radio gracanica %}</p>

## Stream na official sajtu

<p class="centar">{% button https://radiogracanica.ba/uzivo/, &nbsp;oficijelni web sajt, icon fa fa-volume-up, radio gracanica %}</p>
