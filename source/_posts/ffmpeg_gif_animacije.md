---
title: ffmpeg gif animacije
date: 2020-08-24 12:00:00
updated: 2020-08-24 12:00:00
categories:
  - Research
tags:
  - ffmpeg
  - animacija
  - cli
---

<a href="ffmpeg_gif_animacije" aria-label="pročitaj" title="ffmpeg gif">{% img /covers/ffmpeg.gif '"ffmpeg gif""ffmpeg gif"' %}</a>

<!--more-->

```
ffmpeg -f image2 -framerate 6 -i ffmpeg%02d.png -s 960x540 ffmpeg.gif
```

# Konvert video isječka u gif loop

```sh ffmpeg https://ffmpeg.org/documentation.html ffmpeg.org
ffmpeg -ss 30 -t 3 -i ulaz.mp4 -vf "fps=10,scale=320:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" -loop 0 izlaz.gif
```

<ul class="fa-ul">
  <li><span class="fa-li pink"><i class="fas fa-plug fa-xs"></i></span>
    <code>-ss 30</code> preskoči prvih 30 sekundi videa
  </li>
  <li><span class="fa-li pink"><i class="fas fa-plug fa-xs"></i></span>
    <code>-t 3</code> kreira isječak u trajanju od 3s
  </li>
  <li><span class="fa-li pink"><i class="fas fa-plug fa-xs"></i></span>
    <code>-vf</code> je {% link video filter https://ffmpeg.org/ffmpeg-filters.html#Video-Filters video filter %}, u primjeru gore odredili smo broj frejmova po sekundi, veličinu, algoritam skaliranja, te split na paletama kako ne bi generisao nove fajlove.
  </li>
  <li><span class="fa-li pink"><i class="fas fa-plug fa-xs"></i></span>
  <code>-loop</code> za ponavljanje: <code>-loop 0</code> - beskonačno, <code>-loop -1</code> - bez ponavljanja, <code>-loop 1</code> - jedno ponavljanje
  </li>
</ul>

{% note primary %}
`ffmpeg -formats` :izlistaj podržane formate
`ffmpeg -codecs`  :izlistaj instalisane codec-e
{% endnote %}

```sh ffplay https://ffmpeg.org/documentation.html ffmpeg.org
ffplay -loop 0 izlaz.gif
```

**ImageMagick pipe metod:**

```sh ffmpeg https://ffmpeg.org/documentation.html ffmpeg.org
ffmpeg -i ulaznivideo.mp4 -vf "fps=10,scale=320:-1:flags=lanczos" -c:v pam -f image2pipe - | convert -delay 10 - -loop 0 -layers optimize izlaz.gif
```
<ul class="fa-ul">
  <li><span class="fa-li pink"><i class="fas fa-plug fa-xs"></i></span>
    <code>-c:v</code> - video codec <code>pam</code> koristi pam enkoder slike. Podržava ga konverter a i brži je od png.
  </li>
  <li><span class="fa-li pink"><i class="fas fa-plug fa-xs"></i></span>
    <code>-f image2pipe</code> bira image2pipe muxer
  </li>
  <li><span class="fa-li pink"><i class="fas fa-plug fa-xs"></i></span>
    <code>-delay</code> - fps postavka, s tim da je vrijednost u tikovima, primjer <code>- fps=12.5 = 100/12.5 = 8 = -delay 8</code>
  </li>
  <li><span class="fa-li pink"><i class="fas fa-plug fa-xs"></i></span>
    <code>-layers optimize</code> generalna optimizacija gif-a {% link ImageMagick https://imagemagick.org/script/convert.php ImageMAgick %} opcija
  </li>
</ul>

# Konvert video u slike

Nekoliko primjera za ekstraktovanje slika / thumbnail-a iz videa.

Jedna slika sa {% label success@0h 0m 19s 9' %} pozicije na vremenskoj liniji videa:
```sh
ffmpeg -i ulaz.mp4 -ss 00:00:19.900 -vframes 1 izlaz.png
```

Isto kao gore koristeći `.sh` skriptu:
```sh
./skripta.sh 00:00:19.900 ulaz.mp4 izlaz.png
```
```sh skripta.sh
#!/bin/bash
ffmpeg -ss $1 -i $2 -qmin 1 -q:v 1 -qscale:v 2 -frames:v 1 -huffman optimal $3.jpg
```

Jedna slika svaku minutu:
```
ffmpeg -i ulaz.mp4 -vf fps=1/60 izlaz%03d.jpg
```
  - `fps=1` - svaku sekundu, `fps=1/600` svakih sat vremena

Skip 10s, jedna slika nakon svakog stotog frejma, skalirane na 600px širine, u mozaik 2x2:
```
ffmpeg -ss 00:00:10 -i ulaz.avi -frames 1 -vf "select=not(mod(n\,1000)),scale=600:-1,tile=2x2" izlaz.png
```

# Konvert slika u gif

```sh
ffmpeg -f image2 -framerate 12 -i ulaz%02d.png -s 320:-1 izlaz.gif
```

```sh
ffmpeg -f image2 -framerate 9 -i ulaz_%3d.jpg -vf "scale=iw*min(1280/iw\,720/ih):ih*min(1280/iw\,720/ih),pad=1280:720:(1280-iw*min(1280/iw\,720/ih))/2:(720-ih*min(1280/iw\,720/ih))/2" izlaz.gif
```

- `-f image2` dozvoljava upotrebu `%d` stringa npr `%4d` = ulaz0001, ulaz0003
- `framerate` = broj slika u sekundi
  - 9 slika + `-framerate 12` = trajanje 0.750 sekundi
- `-s` skaliranje širina:visina, u drugom primjeru procjenjuje odnos za primjenu.

# :movie_camera: Playground

Za primjer napravio sam 10 frejmova u [Inkscape](https://inkscape.org), i snimio kao 420x250 `.png` slike:

<img src="/images/slikovnica0.png" title="inkscape png export" label="inkscape png export" width="100%" />

```
ffmpeg -f image2 -framerate 10 -i chiko%02d.png slikovnica1.gif      
```

Od 1o slika iz mozaika gore dobijem:

<img src="/images/slikovnica2.gif" title="gif animacija 1" label="gif animacija 1" width="100%" />

Smanjim framerate na 9 i ponovim, + sa color mixerom odradim overlay za {% label primary@Vintage Movie %} efekt:

```
ffmpeg -i slikovnica1.gif -i slikovnica2.gif -filter_complex "[1]format=yuva444p,colorchannelmixer=.393:.769:.189:0:.349:.686:.168:0:.272:.534:.131[slikovnica1.gif];[slikovnica1.gif][0]scale2ref[slikovnica1.gif][slikovnica2.gif];[slikovnica2.gif][slikovnica1.gif]overlay" slikovnica3.gif
```

<img src="/images/slikovnica3.gif" title="gif vintage" label="gif vintage" width="100%" />

- `colorchannelmixer` 16 unosa sa vrijednostima od -2 do 2 za rgba
  `colorchannelmixer=rr:rg:rb:ra:gr:gg:gb:ga:br:bg:bb:ba:ar:ag:ab:aa`
  - primjer za samo jednu vrijednost: `colorchannelmixer=aa=0.5`

# Snimanje zaslona u .gif

```
ffmpeg -f x11grab -video_size default -framerate 10 -i :0.0+10,20 izlaz.gif
```

- `video_size` je zapravo i veličina područja koje snimamo, po defaultu je trenutna rezolucija displeja.
- `0.0` broj displeja
- `20,10` ofset hvatanja videa: x=20, y=10
