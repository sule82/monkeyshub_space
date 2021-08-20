---
title: ffmpeg demuxer
date: 2020-08-27 12:00:00
updated: 2020-08-27 12:00:00
categories:
  - Research
tags:
  - ffmpeg
  - demuxer
  - cli
---

<a href="ffmpeg_concate_demuxer" aria-label="pročitaj" title="ffmpeg gif">{% img /covers/ffmpeg05.webp '"ffmpeg demuxer""ffmpeg demuxer"' %}</a>

<!--more-->

```sh osnovna ffmeg shema
        _______              ______________            
       |       |            |              |           
       | input |  demuxer   | encoded data |   decoder
       | file  | ---------> | packets      | -----+    
       |_______|            |______________|      |    
                                                  v    
                                             _________
                                            |         |
                                            | decoded |
                                            | frames  |
                                            |_________|
       ________             ______________        |    
       |        |           |              |      |    
       | output | <-------- | encoded data | <----+    
       | file   |   muxer   | packets      |   encoder
       |________|           |______________|           
```

Za spajanje većeg broja fajlova ~ inputa u jedan izlaz > output `ffmpeg` je također majstor <small>ako se snalazite u tekstualnom sučelju</small>.

Concat Demuxer:

```sh
ffmpeg -f concat -i spisak_fajlova.txt -c copy out.mp4
```

`concat` demuxer čita listu fajlova i drugih direktiva iz tekstualnog fajla i demuxira ih jedn za drugim. Vremenske tačke su podešeno tako da prvi fajl počinje od 0 te svaki sljedeći tamo gdje prethodni završava.Svi fajlovi treba da imaju iste streamove (codec-e, vremensku osnovicu itd). U slučaju da je vremensko trajanje fajla iz nekog razloga neispravno, to možemo ispraviti `duration` direktivom unutar tekstualnog fajla.

`spisak_fajlova.txt` je tekstualna skripta sa jednom direktivom po liniji. Prazne linije, početni razmaci kao i linije koje počinju sa `#` se ignorišu.

```txt primjer spisak_fajlova.txt
# prvi fajl
file /putanja/do/fajla-1.mp4
# drugi fajl sa razmakom u nazivu
file '/putanja/do/fajla 2.mp4'
# treći fajl sa razmacima i jednim podnavodnikom
file '/putanja/do/fajla 3'\''.mp4'
# četvrti fajl sa korigovanom dužinom trajanja
file fajl-4.mp4
duration 20.0
```

**Opcije** koje demuxer prihvata:

- `safe` ako je postavljeno na `1` odbija nesigurne putanje do fajlova.
- `auto_convert` ako je podešeno na `1` pokušava napraviti automatsku konverziju data paketa kako bi stream bio konkatibilan.

Najčešće korišteni demuxeri za online stream u zadnje vrijeme su: `dash` i `hls`.

Za **automatsko kreiranje liste** možemo korisiti jednu od dole navedenih komandi:
```sh for $1 in $2 do $3; done
for f in ./*.mp4; do echo "file '$f'" >> spisak_fajlova.txt; done
```
```sh printf
printf "file '%s'\n" ./*.mp4 >spisak_fajlova.txt
```

Za **loop jednog video** fajla:
```sh for i in {1..10};
for i in {1..10}; do printf "file '%s'\n" input.mkv >> spisak_fajlova.txt; done
ffmpeg -f concat -i fajlovi.txt -c copy output.mkv
```

**Promjena plejliste** "on the fly" ~ 3 koraka:

Pripremimo tekst fajl sa video fajlom:

```sh spisak_fajlova.txt
file videofajl.mxf
file videofajl.mxf
```
- unesemo videofajl dva puta kako bi bili sigurni da će ffmpeg otvoriti drugi / ponoviti videofajl kada prvi završi.

U kombinaciji sa beskonačnim loop-om možemo na ovaj način napraviti stream:

```sh -stream_loop
ffmpeg -re -stream_loop -1 -i spisak_fajlova.txt -flush_packets 0 -f mpegts udp://127.0.0.1:5000?pkt_size=1316
```

te jednostavnom komandom promjeniti ponavljajući video fajl ~ input trenutnog:

```sh mv
mv naredni-videofajl.mxf videofajl.mxf
```

FIN
