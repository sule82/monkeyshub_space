---
title: Mikrotik Firewall > address-list update
date: 2020-05-04 22:22:00
updated: 2020-05-04 22:22:00
categories:
  - Research
tags:
  - mikrotik
  - python
  - firewall
---

```routeros terminal https://wiki.mikrotik.com/wiki/Manual:IP/Firewall/Address_list wiki.mikrotik.com
/tool fetch url="https://static.monkeyshub.space/soul/block/crna_lista.rsc"
import file-name:"crna_lista.rsc"

/firewall filter add action=drop chain=input src-address-list=crna_lista
...
```

<!--more-->

# Intro

Kao prvo, da bi koristili `address-list` moramo konfigurisati `/firewall filter` & opcionalno `/firewall mangle` ako želimo da ruter sam dodaje nove adrese u listu prema pravilima koje me odredimo.

```routeros firewall filter
/ip firewall filter add action=drop chain=input src-address-list=crna_lista
/ip firewall filter add action=drop chain=forward src-address-list=crna_lista
/ip firewall filter add action=drop chain=forward dst-address-list=crna_lista
```

```routeros firewall mangle
/ip firewall mangle add action=add-src-to-address-list address-list=crna_lista \
address-list-timeout=5m chain=prerouting dst-port=23 protocol=tcp
```

# Na Linux serveru

Kreiramo skriptu gen_crna_lista.sh, napravimo je pokretljivom sa `chmod +x`, testiramo, te dodamo / editujemo u crontab sa komandom `crontab -e`, tako da izgleda otprilike ovako: `0 */12 * * * /usr/local/bin/gen-crna_lista.sh`.

{% note info no-icon %}
U ovom primjeru koristimo **dshield block**, **spamhaus drop**, **spamhaus edrop** i **malc0de blocklist** liste:
{% endnote %}

## Gen Skripta

```bash gen_crna_lista.sh
saveTo=/etc/mikrotik-crna-lista
exportTo=/var/www
now=$(date);

rm -f $saveTo/dshield.txt $saveTo/spamhausdrop.txt $saveTo/spamhausedrop.txt $saveTo/malc0de.txt

wget -q -O - http://feeds.dshield.org/block.txt | awk --posix '/^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.0\t/ { print $1 "/24" ;}' > $saveTo/dshield.txt
wget -q -O - http://www.spamhaus.org/drop/drop.txt | awk --posix '/^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\// { print $1 ;}' > $saveTo/spamhausdrop.txt
wget -q -O - http://www.spamhaus.org/drop/edrop.txt | awk --posix '/^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\// { print $1 ;}' > $saveTo/spamhausedrop.txt
wget -q -O - http://malc0de.com/bl/IP_Blacklist.txt | awk --posix '/^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}/ { print $1 ;}' > $saveTo/malc0de.txt

cat $saveTo/dshield.txt $saveTo/spamhausdrop.txt $saveTo/spamhausedrop.txt $saveTo/malc0de.txt > $saveTo/sve.txt

cat $saveTo/sve.txt | /home/mono/Projects/Mikrotik/cidr-convert.bin > $saveTo/sve-minified.txt

echo "# Generisano $now" > $exportTo/crna_lista.rsc
echo "/ip firewall address-list" >> $exportTo/crna_lista.rsc
cat $saveTo/sve-minified.txt | awk --posix '/^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}/ { print ":do {add list=crna_lista=" $1 " timeout=3d} on-error={set [find list=crna_lista address=" $1 "] timeout=3d}";}' >> $exportTo/crna_lista.rsc
```

# Na Mikrotik ruteru:

## Kreiramo novu skriptu na ruteru

```routeros crna_lista_update
system script add name="crna_lista_update"
```

## Kopiramo sadržaj iz skripte

```routeros script set source
system script set name="crna_lista_update" source="[...]"
```

### Sadržaj skripte

```routeros crna_lista_update.rsc
:local crnalistaURL "https://static.monkeyshub.space/soul/block/crna_lista.rsc"

:local updateOk true
:local importOk true

:do {
    /log info "Crna lista: dohvatam update..."
    /tool fetch url="$crnalistaURL" output=file dst-path="crna_lista.rsc"
} on-error={
    /log error "Crna lista: fetching update nije uspjelo."
    :set updateOk false
    :set importOk false
}
:if ($updateOk) do={
    :do {
        /log info "Crna lista: uvozim novu listu..."
        /import file-name="crna_lista.rsc"
    } on-error={
        /log error "Crna lista: uvoz nove liste nije uspio."
        :set importOk false
    }
}

:if ($importOk) do={
    /log info "Blacklist: uvoz uspješno završen."
}
```
## Kreiramo novi Sheduler

Koji će pozvati **crna_lista_update** skriptu po preferiranom intervalu; preporuka je da to bude dnevno ili svaka dva dana:

```routeros crna_lista_1d
system scheduler \
add name=crna_lista_1d start-time=01:0`:01 on-event="/system script run crna_lista_update" interval=1d disabled=no
```
## Kreiramo firewall pravila

U `input` i `forward` chain-ovima koji će `tarpit`/`drop`/`reject` ako je izvor (`src-address-list`) ili destinacija (`dst-address-list`) u "crna_lista" listi adresa.


# Dodatak

Kako na Mikrotik ruteru očistit `address-list` unose i pojednostavljeno dodati nove:

## Uklanjanje liste unutar rutera

```routeros ukloni sve adrese iz liste
:foreach i in=[/ip firewall address-list find ] do={
    :if ( [/ip firewall address-list get $i list] = "crna_lista") do={
        /ip firewall address-list remove $i
     }
}
```

{% note success %}
Ako nemate server te ručno želite ažurirati listu, iz [gen_crna_lista.sh](#Gen-Skripta) uklonite `timeout` i `on_error=[...timeout]` dio, te dodajte ovaj dio možete dodati na početak [crna_lista_update.rsc](#Sadrzaj-skripte) skripte kako bi prvo očistili postojeće unose.
{% endnote %}

## Dodavanje liste unutar rutera

```routeros dodaj listu
/ip firewall address-list
add address="103.245.223.129" list="crna_lista" timeout=""
add address="103.245.223.131" list="crna_lista"
add address="103.245.223.192" list="crna_lista"
add address="103.245.223.194" list="crna_lista"
add address="...
```
```routeros dodaj listu verzija 2
/ip firewall address-list
:do {add list=crna_lista address=1.10.16.0/20 timeout=""} on-error={set [find list=crna_lista address=1.32.128.0/18] timeout=""}
:do {add list=crna_lista address=1.19.0.0/16}
:do {add list=crna_lista address=1.32.128.0/18}
:do {add list=crna_lista address=2.56.192.0/22}
add address="...
```
