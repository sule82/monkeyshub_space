---
title: ffmpeg Baby tricks
date: 2020-06-14 22:11:20
updated: 2020-06-14 22:11:20
categories:
  - Research
tags:
  - ffmpeg
  - media
  - cli
---

<a href="/ffmpeg_baby_tricks" aria-label="pročitaj" title="ffmpeg" data-pjax-state="">{% img /covers/ffmpeg06.webp '"ffmpeg" "ffmpeg"' %}</a>

<!--more-->

# ffmpeg generalije

Oficijelni linkovi, websajtovi:

<div class="centar">
{% button http://ffmpeg.org/download.html, <br/>ffmpeg Download, icon fa fa-cloud-download-alt fa-fw, ffmpeg Download %}
{% button http://ffmpeg.org/documentation.html, <br/>ffmpeg Dokumentacija, icon fa fa-book fa-fw, ffmpeg Dokumentacija %}
{% button https://trac.ffmpeg.org/, <br/>ffmpeg Wiki, icon fa fa-play fa-fw, ffmpeg Wiki %}
</div>

`ffmpeg` je veoma brzi video i audio konverter koji može da *grabi* i sprema zapis sa živog audio/video izvora. Također konvertuje između proizvoljnih *sample rate* i mjenja veličinu videa {% label success@u letu %} sa visoko kvalitetnim polifaznim filterom.

```sh sinopsis
ffmpeg [global_options] {[input_file_options] -i input_url}
       ... {[output_file_options] output_url}
```
```sh najkraća komanda, koristi zadane postavke za upisane fajl ekstenzije
ffmpeg -i in_video.avi out_video.mp4
```

{% note primary no-icon %}
Obavezno pogledati output od:
- liste podržanih formata: `ffmpeg -formats`
- liste instalisanih codec-a: `ffmpeg -codecs`

Prelistati online dokumentaciju sa linkova gore, ili priručnik: `man ffmpeg`.

Ovdje se nalazi nekoliko sabranih komandi <span class="tekst-77-7">iz historije terminala koje koristim, ovaj post meni dođe kao podsjetnik</span> :notebook_with_decorative_cover: leksikon primjera sa :memo: kratkim opisom →
{% endnote %}

## Audio

**Konvertovanje**:

```sh minimalni transcode iz mp3 u flac ili opus
ffmpeg -i in.mp3 -c:a flac out.flac
ffmpeg -i in.mp3 -c:a libopus out.opus
```
```sh mix down na mono, bit rate, sample rate
ffmpeg -i in.wav -ac 1 -ab 64000 -ar 22050 out.mp3
```
```sh Batch audio konverter unutar datoteke
for i in *.mp3; do ffmpeg -i "$i" -vn -ac 2 -c:a libopus -b:a 96k "${i%.mp3}.opus"; done
```
```sh 30 sekundi dug fragment na ofsetu od 1 minute
ffmpeg -i in.mp3 -ss 60 -t 30 out.opus
```
```sh u HH:MM:SS formatu
ffmpeg -i in.mp3 -ss 0:01:00 -t 0:00:30 out.wav
```
```sh split pri specifiranom rejtu segmenta od 3s
ffmpeg -i in.mp3 -f segment -segment_time 3 -c copy triout%03d.mp3
```
**Video u audio**:
```sh konvertovanje jednog fajla
ffmpeg -i in.mp4 -vn -acodec libvorbis audioout.ogg
```
```sh konvertovanje mnogih fajlova
for vid in *.mp4; do ffmpeg -i "$vid" -vn -acodec libopus "${vid%.mp4}.opus"; done
```
**Ekstraktovanje**:
```sh copy audio
ffmpeg -i in.avi -vn -acodec copy out.aac
```
```sh audio bitrate
ffmpeg -i in.mp4 -f mp3 -ab 192000 -vn out.mp3
```

**Zamjena / uklanjanje / miksanje audio fajla**:
```sh preferirana metoda!
ffmpeg -i video_in.mp4 -i audio_in.wav -shortest -c:v copy -c:a aac -b:a 256k video_out.mp4
```
```sh uklanjanje zvuka
ffmpeg -i video.mp4 -codec copy -an video_pssst.mp4
```
```sh kombinovanje dva stream-a
ffmpeg -i video_in.mov -i audio_in.wav -map 0:v -map 1:a -c copy -y video_encdd.mov
```
```sh ofset zvuka - ručni sinhro
ffmpeg -i video_in.mov -itsoffset -0.25 -i audio_in.wav -map 0:v -map 1:a -c copy -y video-encdd.mov
```
```sh sh loop enkodiranje fajlova u datoteku
mkdir encddd/
for f in *.m4a do ffmpeg -i "$f" -codec:v copy -codec:a libmp3lame -q:a 2 encddd/"${f%.m4a}.mp3"; done
```
```sh da li svi fajlovi sadrže isti format zvuka
for file in *; do ffprobe $file 2>&1 |grep Audio; done
```
```sh automatsko audio codec parsiranje
for file in *mp4 *avi; do ffmpeg -i "$file" -vn -acodec copy "$file".`ffprobe "$file" 2>&1 |sed -rn 's/.*Audio: (...), .*/\1/p'`; done
```
- ova komanda koristi **sed** da parsira izlaz iz `ffprobe` za svaki fajl, i pretpostavlja 3-slovni audio format (mp3, ogg, acc, m4a) te će puknuti ako bude drugačije (npr opus).

```sh batch enkodiranje i konvertovanje
for f in *.m4a; do ffmpeg -i "$f" -acodec libmp3lame -ab 256k "${f%.m4a}.mp3"; done
```

## Slika - fotografija

```sh skriptovanje
jedna_tada [ss][ulazni_fajl][izlazni_fajl]
```
gdje je:
```sh jedna_tada skripta
ffmpeg -ss $1 -i $2 -qmin 1 -q:v 1 -qscale:v 2 -frames:v 1 -huffman optimal $3.jpg
```

**Split video u slike**:
```sh jedna slika po frejmu
ffmpeg -i in.flv out_%d.jpg
```

**Konvertovanje slika u video**:
```sh slideshow
ffmpeg -f image2 -i in_%d.jpg out.mp4
```
```sh enkodirani slideshow
ffmpeg -i in_%03d.png -c:v libx264 -pix_fmt yuv420p out.mp4
```
```sh trajanje slike i br kopija po sekundi
ffmpeg -r 1/5 -i in_%03d.png -c:v libx264 -vf fps=25 -pix_fmt yuv420p out.mp4
```

**Konvertovanje jedne slike u video**:
```sh u trajanju od 1 min.
ffmpeg -loop 1 -i slika.png -c:v libx264 -t 60 -pix_fmt yuv420p -vf scale=1920:1080 out.mp4
```

**Poster + audio**:
```sh za youtube npr.
$ffmpeg -loop 1 -i slika.jpg -i pjesma.mp3 -c:v libx264 -c:a aac -strict experimental -b:a 192k -shortest yt_video.mp4
```

**Konvertovanje nesekvenciono imenovanih slika iz datoteke u video**:
```sh prema glob uzorku
ffmpeg -framerate 30 -pattern_type glob -i '*.jpeg' -c:v libx264 -pix_fmt yuv420p gan-1.mov
```

**Konvertovanje sekvenciono imenovanih slika raznih veličina**:
```sh
ffmpeg -i in_%04d.jpg -c:v libx264 -pix_fmt yuv420p -vf "scale=max(1280\,a*720):max(1280\,720/a),crop=1280:720" out.mp4
```

**Zadržavanje odnosa širina/visina pri skaliranju**:
```sh proporcionalno skaliranje
ffmpeg -i in_%04d.jpg -c:v libx264 -pix_fmt yuv420p -vf "scale=720:-2" out.mp4
```

**Procjena koji odnos da se primjeni**:
```sh maksimalo 1280:-
ffmpeg -i in_%04d.jpg -c:v libx264 -pix_fmt yuv420p -vf "scale=iw*min(1280/iw\,720/ih):ih*min(1280/iw\,720/ih), pad=1280:720:(1280-iw*min(1280/iw\,720/ih))/2:(720-ih*min(1280/iw\,720/ih))/2" out.mp4
```
```sh maksimalno 1920:-
ffmpeg -i in_%04d.jpg -c:v libx264 -pix_fmt yuv420p -vf "scale=iw*min(1920/iw\,1080/ih):ih*min(1920/iw\,1080/ih), pad=1920:1080:(1920-iw*min(1920/iw\,1080/ih))/2:(1080-ih*min(1920/iw\,1080/ih))/2" out.mp4
```
## Video

**Bilo koji video codec u H264 .mp4**:
```sh sa pixel format opcijom
ffmpeg -i in.mov -vcodec libx264 -pix_fmt yuv420p out.mp4
```

**Ultrafast promjena veličine slike videa**:  
```sh bez promjene codec-a
ffmpeg -i ffmpeg.mp4 -c:v copy -s 720x480 -preset ultrafast ffmpeg.mkv
```

**Enkodiranje ili Re-enkodiranje?**:
```sh bez ponovnog enkodiranja
ffmpeg -i in.mp4 -ss 00:00:05 -t 00:00:10 -c copy cut_out.mp4
```
```sh sa ponovnim enkodiranjem
ffmpeg -i in.mp4 -ss 00:00:05 -t 00:00:10 -async 1 -strict -2 cut_out.mp4
```
```sh ultrafast sa codec, kvalitet i veličina opcijom
ffmpeg -i in.mp4 -ss 00:01:40 -t 00:05:10 -c:v libx264 -crf 21 -vf "scale=960:-1" -preset ultrafast -c:a aac -ac 2 cut_out.mp4
```

**Splitovanje u više parčadi bez re-enkodiranja**:
```sh za koliko odredimo trajanje za toliko i premotajemo video
ffmpeg -i in.mp4 -t 00:00:50 -c copy out-prvi.mp4 -ss 00:00:50 -t 00:02:30 -c copy out-drugi.mp4 -ss 00:02:30 -c copy out-zadnji.mp4
```

**Smanjivanje veličine fajla**:
```sh crf 24
ffmpeg -i in.mov -vcodec libx264 -crf 23 out.mp4
```
```sh bez boje 640x480
ffmpeg -i in.mov -vf eq=saturation=0 -s 640x480 -c:v libx264 -crf 23 out.mp4
```

**Bulk konvertovanje**:
```sh bulk konvert video u specifični audio format
for i in *.mp4; do ffmpeg -i "$i" -vn -c:a libopus -b:a 196k "${i%.mp4}.opus"
```

**Double pass - za ujednačen bitrate**:
```sh double pass 18
ffmpeg -i in.mp4 -c:v libx264 -b:v 960k -crf 18 -maxrate 960k -bufsize 2M -pass 1 -an -f null /dev/null
ffmpeg -i in.mp4 -c:v libx264 -b:v 960k -crf 18 -pass 2 -c:a aac -ac 2 -b:a 64k out_18.mp4
```
```sh double pass za online stream 23 @ 100000 + subtitle
ffmpeg -i video.mp4 -c:v libx264 -b:v 1M -crf 23 -maxrate 1M -bufsize 2M -pass 1 -an -f null /dev/null
ffmpeg -i in.mp4 -vf "ass=in_sub.ass" -c:v libx264 -b:v 1M -crf 23 -pass 2 -c:a aac -ac 2 out_23.mp4
```

**Spajanje mnogih fajlova - concat**:
```sh mojalista.txt
cat mojalista.txt
file '/path/to/file1'
file '/path/to/file2'
file '/path/to/file3'
```
```sh concat demuxer
ffmpeg -f concat -safe 0 -i mojalista.txt -c copy out.mp4
```

**Konvertovanje video fajla u gif animaciju**:
```sh sa zadanom širinom, trajanjem i br frejmova
ffmpeg -i in.mp4 -vf scale=480:-1 -t 10 -r 10 anime_out.gif
```
