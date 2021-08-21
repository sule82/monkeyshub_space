---
title: Hosts fajl
date: 2020-07-30 11:01:00
updated: 2020-07-30 11:01:00
categories:
  - Research
tags:
  - hosts
  - ads
  - network
---

<a href="/linux_hosts_fajl" aria-label="pročitaj" title="Hosts fajl" data-pjax-state="">{% img /covers/hosts.webp '"Hosts fajl""Hosts fajl"' %}</a>

<!--more-->

# Info

Hosts fajl je fajl operativnog sistema koji mapira hostnames u IP adrese. Za razliku od udaljenih DNS servera ovdje korisnik / admin lokalne mašine ima potpuno kontrolu. Čisti je tekstualni fajl i u najjednostavnijoj verziji izgleda ovako:

```jinja etc/hosts
127.0.0.1  localhost loopback
::1        localhost
```
Ovaj primjer sadrži samo loopback adrese sistema i njegove hostnames. IP adresa može sadržavati višestruke hostnames i hostname može biti mapiran i do IPv4 i IPv6 IP adresa. Finija, proširena verzija, sa opcijama koju preporučujem, i koja će se koristi kao početak svakog generiranog hosts fajla bi bila:

```jinja etc/hosts
127.0.0.1	localhost localhost.localdomain localhost4 localhost4.localdomain4
::1 ip6-localhost ip6-loopback localhost6 localhost6.localdomain6
# 127.0.1.1	UNESI-SVOJ-HOSTNAME-OVDJE-I-UKLONI-#
# Poželjne linije za IPv6 sposoban hosts na LiNUX-u:
# fe00::0 ip6-localnet
# ff00::0 ip6-mcastprefix
# ff02::1 ip6-allnodes
# ff02::2 ip6-allrouters
```
Da bi mapiranje funkcionisalo mašina (OS) treba da bude podešena da po defaultu čita hosts fajl. I da, obavezno (preporučuje se toplo) treba ukloniti IPv6 konekciju sa mrežnog interfejsa ako je ne koristimo, inače poželjno je generirati hostnames mapu posebno za IPv4 i za IPv6 IP adrese.

**Gdje se nalazi, ovisno o OS**:

| Operativni sistem | verzija | lokacija |
| :--- | :--- | :--- |
| Unix, Unix-like, POSIX | <span style="color:#87d1ea" role="img" aria-label="linux"><i class="fab fa-linux"></i></span> | /etc/hosts |
| Microsoft Windows | 95, 98, ME | %WinDir%\hosts |
| Microsoft Windows | NT, 2000 - Win10 | %SystemRoot%\System32\drivers\etc\hosts |
| Apple Macintosh | Mac OS X 10.2 i noviji | /etc/hosts |
| Android | <span style="color:#87d1ea" role="img" aria-label="linux"><i class="fab fa-android"></i></span> | /etc/hosts |
| iOS | iOS 2.0 i noviji | /etc/hosts |

# Primjena

**Šira primjena**:

U svojoj funkciji za `resolving host names`, host fajl se može koristiti za definisanje bilo kojeg hostname ili domain name za korištenje u lokalnom sistemu.

**Preusmjeravanje lokalnih domena**:

Neki web servisi, intranet developeri i administratori definišu lokalno definisane domene u LAN-u za razne potrebe.

**Blokiranje internet resursa**:

{% note default no-icon %}
Unosi u hosts fajlu se mogu koristiti za blokiranje online reklama, ili/i domena (poznatog) zlonamjernog sadržaja i servera koji sadrže spyware, adware i dr malware.
{% endnote %}

Ovo postižemo unosom zahtjeva za preusmjerenje do drugih adresa koje ne postoje ili do sigurnih destinacija kao što je lokalna mašina.

<span style="color:#87d1ea" role="img" aria-label="linux"><i class="fab fa-linux fa-lg"></i></span> **hosts primjer za blokiranje internet resursa**:

Primjer hosts fajla koji pokušava preusmjeriti / blokirati google tracking i google ads (unosi preuzeti od adguard i drugih ip listi), {% label danger@napomena: sa ovom listom nije moguće napraviti 100% g-ad-block filter %}, te za google treba specificirati po regionima kako bi "odustao" od plasiranja reklama:

```jinja /etc/hosts
127.0.0.1 localhost localhost4
::1       ip6-localhost ip6-loopback localhost6

0.0.0.0 ads.google.com
0.0.0.0 www.ads.google.com
0.0.0.0 adservice.google.com
0.0.0.0 adservice.google.ba
0.0.0.0 adwords.l.google.com
0.0.0.0 id.google.com
0.0.0.0 google-analytics.com
0.0.0.0 analytics.corp.google.com
0.0.0.0 ssl.google-analytics.com
0.0.0.0 ssl-google-analytics.l.google.com
0.0.0.0 googleads.g.doubleclick.com
0.0.0.0 googleads.g.doubleclick.net
0.0.0.0 googleads2.g.doubleclick.net
0.0.0.0 googleads4.g.doubleclick.net
0.0.0.0 googleadservices.com
0.0.0.0 gstaticadssl.l.google.com
0.0.0.0 pagead-tpc.l.google.com
0.0.0.0 pagead.l.google.com
0.0.0.0 googleadservices.com
0.0.0.0 partner.googleadservices.com
0.0.0.0 partnerad.l.google.com
0.0.0.0 partnerssl.googleadservices.com
0.0.0.0 pagead2.googleadservices.com
0.0.0.0 pagead2.googlesyndication.com
0.0.0.0 pagead46.googlesyndication.com
0.0.0.0 ogs.google.com
0.0.0.0 s0-2mdn-net.l.google.com
0.0.0.0 tpc.googlesyndication.com
0.0.0.0 tpcnc.googlesyndication.com
0.0.0.0 video-ad-stats.googlesyndication.com
0.0.0.0 www.google-analytics.com
0.0.0.0 www.googleadservices.com
0.0.0.0 www.googletagmanager.com
0.0.0.0 www.googletagservices.com
0.0.0.0 www-google-analytics.l.google.com
0.0.0.0 www-googletagmanager.l.google.com
0.0.0.0 10.afs.googleadservices.com
0.0.0.0 18.afs.googleadservices.com
0.0.0.0 2.afs.googleadservices.com
0.0.0.0 4.afs.googleadservices.com
0.0.0.0 ad-creatives-public.commondatastorage.googleapis.com
0.0.0.0 ampcid.google.com
0.0.0.0 cdn.googletoolservices.com
```
# Generator hosts fajla

Ovdje ću pokazati jedan od načina kako da generišemo blocklist-u koja je spremna za primjenu u `hosts` fajlu iz izvora po našoj želji, sa statistikom izmjena.

Izgled `generator.sh` skripte:

```sh generator.sh
#!/bin/bash

SOURCE_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" >/dev/null 2>&1 && pwd)"
EXPORT_DIR="$SOURCE_DIR/izvoz"
WORK_DIR="/home/mono/adblock-generator"
STATS_DIR="$SOURCE_DIR/statistika"

LISTE="$SOURCE_DIR/lista.txt"
REGEX="$SOURCE_DIR/regex.txt"
MOJ_BLACKLIST="$SOURCE_DIR/moj_blacklist.txt"
MOJ_WHITELIST="$SOURCE_DIR/moj_whitelist.txt"

TMP_LISTA="$WORK_DIR/blocklista.tmp"
TMP_LISTA2="$WORK_DIR/blocklista.tmp2"
FNL_LISTA="$EXPORT_DIR/blocklista.txt"
FNL_LISTA_HOSTS="$EXPORT_DIR/blocklista_za_hosts.txt"

# STATISTIKA
CHANGELOG="$STATS_DIR/changelog.txt"
DODANE_DOMENE="$STATS_DIR/dodane.txt"
UKLONJENE_DOMENE="$STATS_DIR/uklonjene.txt"
GRUPISANO_PO_DOMENI="$STATS_DIR/top_50_blokiranih_tld.txt"

# Kreiraj datoteke
mkdir -p "$STATS_DIR" "$EXPORT_DIR" "$WORK_DIR"

# Skini sve blockliste u jedan fajl
echo -e "Sve blocklist-e se trenutno skidaju ..."
while read -r url; do curl -s "$url" >>"$TMP_LISTA"; done <"$LISTE"
cat "$MOJ_BLACKLIST" >> "$TMP_LISTA"

echo -e "Uklanjam sve sa punkcijom ..."
sed -i '/^[[:punct:]]/ d' "$TMP_LISTA"

echo -e "Uklanjam IP adrese ..."
sed -i -e 's/[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}//g' "$TMP_LISTA"

echo -e "Uklanjam razmake i pretvaram sve u mala slova ..."
cat "$TMP_LISTA" | tr -d ' ' | tr -d '\t' | sed '/^[[:space:]]*$/d' | tr '[:upper:]' '[:lower:]' >"$TMP_LISTA2" && mv -f "$TMP_LISTA2" "$TMP_LISTA"

echo -e "Sortiram i uklanjam duplikate ..."
grep -oe '^.*\S' "$TMP_LISTA" | sort | uniq >"$TMP_LISTA2" && mv -f "$TMP_LISTA2" "$TMP_LISTA"

echo -e "Provjeravam valjanost domena i pod domena ..."
pcregrep -o '^([a-z0-9][a-z0-9-_]*\.)*[a-z0-9]*[a-z0-9-_]*[[a-z0-9]+$' "$TMP_LISTA" >"$TMP_LISTA2" && mv -f "$TMP_LISTA2" "$TMP_LISTA"

echo -e "Primjenjujem regex filtere ..."
while read -r r; do sed -i -E "${r}d" "$TMP_LISTA"; done <"$REGEX"


echo -e "Pazim na unose iz whiteliste ..."
comm -23 <(sort "$TMP_LISTA") <(sort "$MOJ_WHITELIST")  >"$TMP_LISTA2" && mv -f "$TMP_LISTA2" "$TMP_LISTA"

echo -e "Statistika: Top 50 blokiranih domena ..."
cat "$TMP_LISTA" | sed 's/.*\.//' | sort | uniq -c | sort -nr | head -50 >"$GRUPISANO_PO_DOMENI"

echo -e "Statistika: Changelog ..."
echo "$(date +"%d.%m.%Y %H:%M:%S")    $(wc -l <"$TMP_LISTA") Domene" >>"$CHANGELOG"
echo "$(tail -10 "$CHANGELOG")" >"$CHANGELOG"

comm -13 <(sort "$TMP_LISTA") <(sort "$FNL_LISTA") >"$UKLONJENE_DOMENE"
comm -23 <(sort "$TMP_LISTA") <(sort "$FNL_LISTA") >"$DODANE_DOMENE"
echo -e "Statistika: \e[32m$(wc -l <"$DODANE_DOMENE") \e[39mdodane domene ..."
echo -e "Statistika: \e[31m$(wc -l <"$UKLONJENE_DOMENE") \e[39muklonjene domene ..."

echo -e "Generisanje je zavrseno: \e[95m$(wc -l <"$TMP_LISTA") domena je u blocklisti!"
sed -e 's/^/0.0.0.0\ /' "$TMP_LISTA" >"$FNL_LISTA_HOSTS"
mv -f "$TMP_LISTA" "$FNL_LISTA"

```

Da bi skripta funkcionisala, trebate joj dati dopuštenje za pokretanje:

```sh
chmod +x generator.sh
```
te pored nje potrebno je da, u istoj datoteci, imate kreirane i sljedeće fajlove:

- `lista.txt`, gdje ćete unjeti linkove od lista adresa po želji:
  ```
  https://adaway.org/hosts.txt
  https://www.dshield.org/feeds/suspiciousdomains_High.txt
  https://pgl.yoyo.org/adservers/serverlist.php?hostformat=hosts&showintro=0&mimetype=plaintext
  ```
- `moj_blacklist.txt`, gdje ćete unjeti dodatne domene koje želite blokirati, npr:
  ```
  facebook.com
  m.facebook.com
  ```
- `moj_whitelist.txt`, gdje ćete unjeti domene kojima želite imati pristup, npr:
  ```
  youtube.com
  monkeyshub.space
  ```

Radnu datoteku, kao i sve ostalo, promjenite po želji.
Rezultat - blocklista će se generisati u `/izvoz/blocklista.txt`, dok lista spremna za korištenje unutar `/etc/hosts` fajla će biti u `/izvoz/blocklista_za_hosts.txt` i jednostavno kopirajte sadržaj liste u `hosts` kako bi blokirali generisanu listu adresa.
