---
title: Linux konzolni servisi
date: 2020-05-29 23:57:00
updated: 2020-05-29 23:57:00
categories:
  - Resources
tags:
  - linux
  - cli
  - natuknice
---

```sh
fetch https://url.link -o sajt.json
curl -I https://url.link
wget https://url.link -S
```
<!--more-->

# Klijenti

otreban je barem jedan od slijedećih klijenata instalisan na računaru:

- aria2 [link](https://aria2.github.io/)
```sh aria2c primjer
#web download:
aria2c http://url.link/f.iso
#download iz 2 izvora:
aria2c http://a/f.iso ftp://b/f.iso
#koristi 2 konekcije po hostu:
aria2c -x2 http://a/f.iso
#torrent
aria2c http://url.link/f.torrent
aria2c 'magnet:?xt=urn:btih:248D0A1CD08284299DE78D5C1ED359BB46717D8C'
#koristi URI listu iz fajla:
aria2c -i uris.txt
```
- bitsadmin [link](https://docs.microsoft.com/windows/win32/bits/)
- curl [link](https://curl.se/)
```sh curl primjer
#download & save fajl
curl -O www.haxx.se/index.html -O curl.se/download.html
#sftp download:
curl -k sftp://sftp.link/lamp0/web/.../fajl --user "username:password" -o /fajl --ftp-create-dirs
#sftp upload
curl -k sftp://sftp.link/lamp0/web/.../ --user "username:password" -T fajl --ftp-create-dirs
```
- httpie [link](https://httpie.io/)
```sh httpie primjer
#download
http pie.dev/image/png > image.png
http --download pie.dev/image/png
#Set Host 2 DNS rekord
http localhost:8000 Host:monochrome.com
```
- httrack [link](https://www.httrack.com/)
- powershell [link](https://microsoft.com/powershell/)
- rclone [link](https://rclone.org/)
```sh rclone primjer
#setup remote con. / storage
rclone config #interaktivno -i
#podkomande
rclone ls remote:path #izlistava remote
rclone copy /local/path remote:path #kopira /local/path 2 remote
rclone sync -i /local/path remote:path #sinhro /local/path 2 remote
```
- wget [link](https://www.gnu.org/software/wget/)
```sh wget primjer
#download websajta
wget -r -l 0 http://url.link/
```
- wget2 [link](https://gitlab.com/gnuwget/wget2)

# Servisi

{% note info no-icon %}
Servisi na listi dole su sortirani prema koristi:
{% endnote %}

## IP Address

### Inline

```sh curl ip address inline
curl ...
        l2.io/ip
        echoip.de
        ifconfig.me
        #ifconfig.me/all
        ipecho.net/plain
        ident.me
        bot.whatismyipaddress.com
        whatismyip.akamai.com
        curlmyip.net
        api.ipify.org
```

## New line

```sh curl ip address in new line
curl ...
        ipcalf.com
        ipaddr.site
        ifconfig.pro
        curlmyip.net
        ipinfo.io/ip
        checkip.amazonaws.com
        smart-ip.net/myip
        ip-api.com/line?fields=query
        ifconfig.io/ip
```

## DNS (IPv4 & IPv6)

```sh cloudflare
dig @1.1.1.1 whoami.cloudflare ch txt +short
dig @2606:4700:4700::1111 whoami.cloudflare ch txt -6 +short
```
```sh google.com
dig @ns1.google.com o-o.myaddr.l.google.com TXT -6 +short
dig @ns1.google.com o-o.myaddr.l.google.com TXT -4 +short
```
```sh dnscrypt.info
dig resolver.dnscrypt.info TXT +short
```
```sh dnsjson.com
#za txt dns rekord u resolver.dnscrypt.info:
curl https://dnsjson.com/resolver.dnscrypt.info/TXT.json
#za ns dns rekord u monkeyshyb.space
curl https://dnsjson.com/monkeyshub.space/NS.json
```
## JSON

```sh curl ip address in .json format
curl ...
        httpbin.org/ip
        wtfismyip.com/json
     -L iphorse.com/json
        geoplugin.net/json.gp
        https://ipapi.co/json
     -L jsonip.com
        gd.geobytes.com/GetCityDetails
        ip.jsontest.com
```

## GeoLocation

```sh ipinfo.io
# za geografsku dužinu i širinu:
curl ipinfo.io/8.8.8.8 #ili:
curl ipinfo.io/8.8.8.8/loc
```
```sh ifconfig.co
curl ifconfig.co/country #za drzavu
curl ifconfig.co/city #za grad
curl ifconfig.co/country-iso #za iso kod drzave

#za full geolokacijski info:
http -body ifconfig.co/json
```
```json output ifconfig.co/json
{
    "asn": "AS59847",
    "asn_org": "WIRAC.NET d.o.o.",
    "city": "Gracanica",
    "country": "Bosnia and Herzegovina",
    "country_eu": false,
    "country_iso": "BA",
    "ip": "185.69.30.209",
    "ip_decimal": 3108314833,
    "latitude": 44.7031,
    "longitude": 18.3101,
    "region_code": "BIH",
    "region_name": "Federation of B&H",
    "time_zone": "Europe/Sarajevo",
    "user_agent": {
        "product": "HTTPie",
        "raw_value": "HTTPie/2.4.0",
        "version": "2.4.0"
    }
}
```

# Null Pointers

## Text Sharing

`echo 'Cao, Vozdrica' | curl -F 'f:1=<-' ix.io`
`cat hello.txt | curl -F file=@- 0x0.st`
`echo "Cao, Vozdrica" | curl -F 'clbin=<-' https://clbin.com`
`echo "Cao, Vozdrica" | nc termbin.com 9999 #req netcat`
`echo "Cao, Vozdrica" | curl -F 'sprunge=<-' sprunge.us`

## File Sharing & shorten URLs

`curl -F'shorten=http://example.com/some/long/url' http://0x0.st`
`curl --upload-file <fajl> transfer.sh/<naziv-fajla>`
`curl --upload-file <fajl> filepush.co/upload/<naziv-fajla>`
`curl -F file=@<fajl> https://ttm.sh`

# Weather

```sh wttr.in
curl wttr.in//Gracanica,BA
```

# Map

`telnet mapscii.me`

# Documentation

`curl cheat.sh`

# Dictionaries

`curl 'dict.org/d:command line'`

# Generators

| </>  |    i |
| :--- | ---: |
| `curl -H 'Accept: text/plain' foaas.com/cool/:from` | fuck off kao servis |
| `curl pseudorandom.name` | generira pseudo random ime |

# Entertainment and Games

| </>  |   i  |
| :--- | ---: |
| `nc towel.blinkenlights.nl 23` | StarWars u terminalu |
| `ssh chat.shazow.net` | chat preko SSH (shazow/ssh-chat) |
| `curl parrot.live` | animirani party papagaj (hugomd/parrot.live) |
| `curl node-web-console.glitch.me` | emoji utrka (source) |

# Telnet / ssh

| </>  |   i  |
| :--- | ---: |
| `ssh sshtron.zachlatta.com` | snake, awsd tipke |
| `ssh netris.rocketnine.space` | multiplayer tetris |
| `ssh nethack@alt.org` | fun roguelike Games |
| `ssh play@anonymine-demo.oskog97.com -p 2222` | pass: play |
| `ssh twenex@sdf.org` | checkers i sl. |
| `telnet freechess.org` | šah |
| `telnet milek7.gq` | pong, break out, tetris |
| `telnet aardmud.org` | MUD |
| `telnet mud.darkerrealms.org 2000` | MUD |
| `telnet telehack.com` | dosta zanimljivih stvari |
