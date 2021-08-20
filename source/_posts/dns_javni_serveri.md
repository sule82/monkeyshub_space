---
title: Javni DNS serveri
date: 2020-07-25 12:01:00
updated: 2020-07-25 12:01:00
categories:
  - Research
tags:
  - dns
  - network
  - doh
---

<a href="dns_javni_serveri" aria-label="pročitaj" title="DNS serveri">{% img /covers/dns-serveri.webp '"DNS serveri" "DNS serveri"' %}</a>

<!--more-->

# Info

Ovdje možete pronaći listu trenutno najboljih, potpuno besplatnih DNS servera, <span style="color:#B2A571;" role="img" aria-label="+"><i class="fa fa-plus fa-xs"></i></span> upute kako ih koristiti.

{% note info no-icon %}
Preskočite <span style="color:#B2A571;" role="img" aria-label="+"><i class="fa fa-info fa-xs"></i></span> uvod i idite na postavke servera:
[Google](#Google) | [Quad9](#Quad9) | [OpenDNS](#OpenDNS) | [Cloudflare](#Cloudflare) | [CleanBrowsing](#CleanBrowsing) | [AdGuard DNS](#AdGuard-DNS) | [NextDNS](#NextDNS)
Dodatni popis besplatnih DNS servera, kao i korisni linkovi se nalaze pri [dnu stranice](#Dodatni-DNS-serveri).
{% endnote %}

**Šta je DNS server?**

[DNS serveri](https://en.wikipedia.org/wiki/Name_server) prevode nama prisan naziv domene stranice koji upisujemo u browser, *npr: google.com*, u [javnu IP adresu](https://www.lifewire.com/what-is-a-public-ip-address-2625974) koja je zapravo potrebna kako bi uređaj koji koristite za surfanje komunicirao sa stranicom kojoj pristupate.

Vaš [ISP](https://www.lifewire.com/internet-service-provider-isp-2625924) automatski određuje DNS servere ali niste obavezni da koristite iste, postoji mnogo razloga zašto bi koristili neke druge imate problema sa trenutnim, želite bolju performansu, ne želite tragove vaših aktivnosti, želite blokirati određene stranice, privatnost i brzina su ono što ponajprije uočite promjenom.

Primarni DNS serveri *(preferirani)* i sekundarni DNS serveri *(alternativni)* se mogu kombinovati od više provajdera ali time gubite cjelokupno ponuđeni servis jednog od provajdera.

# Google

{% img /covers/dns/dns-google.png '"Google""Google"' %}

[Google Public DNS](https://developers.google.com/speed/public-dns/) nudi: brže surfanje, poboljšanu sigurnost, i precizne rezultate bez preusmjeravanja.

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 8.8.8.8 | 8.8.4.4 |
| 2001:4860:4860::8888 | 2001:4860:4860::8844 |

Ono što omugućava brzinu korištenjem google DNS su njihovi **široko rasprostranjeni** data serveri.

<ul class="fa-ul">
  <li><span class="fa-li"><i class="fas fa-dot-circle"></i></span>
  DoT
  ```
  kdig -d +noall +answer @dns.google example.com \
  +tls-ca +tls-hostname=dns.google # +tls-sni=dns.google
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-exclamation-circle"></i></span>
  DNS-over-TLS
  ```
  dns.google
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-user-circle"></i></span>
  DNS-over-HTTPS
  ```
  https://dns.google/dns-query
  https://dns.google/resolve
  ```
  </li>
</ul>

# Quad9

{% img /covers/dns/dns-quad9.png '"Quad9""Quad9"' %}

[Quad9](https://www.quad9.net/) ima besplatne DNS servere koji vas štite od cyber napada trenutnim i automatskim blokiranjem pristupa nesigurnim web stranicama bez skladištenja vaših [privatnih informacija](https://www.quad9.net/policy/).

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 9.9.9.9 | 149.112.112.112 |
| 2620:fe::fe | 2620:fe::9 |

Quad9 blokira domene koje su zaražene ili sadrže malware. Također, ima i [nesiguran IPv4 / IPv6 javni DNS server](https://www.quad9.net/faq/#Is_there_a_service_that_Quad9_offers_that_does_not_have_the_blocklist_or_other_security) na adresama: `9.9.9.10` & `2620:fe::10`.

<ul class="fa-ul">
  <li><span class="fa-li"><i class="fas fa-dot-circle"></i></span>
  DnsCrypt
  ```
  https://www.quad9.net/quad9-resolvers.toml
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-exclamation-circle"></i></span>
  DNS-over-TLS
  ```
  dns.quad9.net
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-user-circle"></i></span>
  DNS-over-HTTPS
  ```
  https://dns.quad9.net/dns-query
  ```
  </li>
</ul>

# OpenDNS

{% img /covers/dns/dns-open.png '"OpenDNS""OpenDNS"' %}

[OpenDNS](https://www.opendns.com/), Cisco javni DNS server, sa 100% pouzdanosti i up-time, te preko 90 miliona korisnika nudi dva seta besplatnih javnih DNS servera:

**OpenDNS Home**:

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 208.67.222.222 | 208.67.220.220 |
| 2620:119:35::35 | 2620:119:53::53 |

**OpenDNS Family Shield**:

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 08.67.222.123 | 208.67.220.123 |

Tehnologija koju koriste je [OpenDNS Innovations](https://www.opendns.com/about/innovations/).

<ul class="fa-ul">
  <li><span class="fa-li"><i class="fas fa-dot-circle"></i></span>
  DnsCrypt
  ```
  https://www.opendns.com/about/innovations/dnscrypt/#download
  ```
  </li>
</ul>

# Cloudflare

{% img /covers/dns/dns-cloudflare.png '"Cloudflare""Cloudflare"' %}

[Cloudflare](https://cloudflare.com/) je izgradio [1.1.1.1](https://1.1.1.1/) sa ciljem da bude:

> fastest DNS service in the world

i nikada ne pohranjuje tvoju IP adresu, ne prodaje tvoje podatke niti ih koristi kao mete reklama.

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 1.1.1.1 | 1.0.0.1 |
| 2606:4700:4700::1111 | 2606:4700:4700::1001 |

<ul class="fa-ul">
  <li><span class="fa-li"><i class="fab fa-twitter"></i></span>
  DNS-over-Twitter
  ```
  [@1111Resolver](https://twitter.com/1111Resolver)
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-exclamation-circle"></i></span>
  DNS-over-TLS
  ```
  kdig -d @1.1.1.1 +tls-ca +tls-host=cloudflare-dns.com example.com
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-user-circle"></i></span>
  DNS-over-HTTPS
  ```
  https://cloudflare-dns.com/dns-query
  ```
  </li>
</ul>

# CleanBrowsing

{% img /covers/dns/dns-clean.png '"CleanBrowsing""CleanBrowsing"' %}

[CleanBrowsing](https://cleanbrowsing.org) nudi 3 besplatna javna DNS servera, odnosno [3 filtera](https://cleanbrowsing.org/filters):

**Family Filter**:

Blokira pristup svim pornografskim i eksplicitnim stranicama. Blokira također i proksi i VPN domene koje se koriste za zaobilaženje filtera. Stranice miješanih sadržaja (kao što je Reddit) su također blokirane. Google, Bing i YouTube su postavljene na siguran način. Malware i Phishing domene su blokirane.

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 185.228.168.168 | 185.228.169.168 |
| 2a0d:2a00:1:: | 2a0d:2a00:2:: |

**Adult Filter**:

Blokira pristup svim pornografskim i eksplicitnim stranicama. Ne blokira proksi i VPN domene, niti stranice miješanih sadržaja. Google, Bing i YouTube su postavljene na siguran način. Malware i Phishing domene su blokirane.

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 185.228.168.10 | 185.228.169.11 |
| 2a0d:2a00:1::1 | 2a0d:2a00:2::1 |

**Security Filter**:

Blokira Spam, Malware, Phishing i zlonamjeran sadržaj. Ne blokira sadržaj za odrasle. Update baze se vrši svaki sat vremena i smatra se jednom među najboljim.

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 185.228.168.9 | 185.228.169.9 |
| 2a0d:2a00:1::2 | 2a0d:2a00:2::2 |

<ul class="fa-ul">
  <li><span class="fa-li"><i class="fas fa-dot-circle"></i></span>
  DNSCrypt
  ```
  sdns://AQMAAAAAAAAAFDE4NS4yMjguMTY4LjE2ODo4NDQzILysMvrVQ2kXHwgy1gdQJ8MgjO7w6OmflBjcd2Bl1I8pEWNsZWFuYnJvd3Npbmcub3Jn
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-exclamation-circle"></i></span>
  DNS-over-TLS
  ```
  security-filter-dns.cleanbrowsing.org
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-user-circle"></i></span>
  DNS-over-HTTPS
  ```
  https://doh.cleanbrowsing.org/doh/security-filter/
  ```
  </li>
</ul>

# AdGuard DNS

{% img /covers/dns/dns-adguard.png '"AdGuard DNS""AdGuard DNS"' %}

[AdGuard DNS](https://adguard.com/en/adguard-dns/overview.html) blokira video oglase i oglase u aplikacijama, preglednicima, igrama i na bilo kojoj web stranici i ne pohranjuje zapise DNS upita. Nudi tri opcije:

**Default**:

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 176.103.130.130 | 176.103.130.131 |
| 2a00:5a60::ad1:0ff | 2a00:5a60::ad2:0ff |

**Family protection**:

Pored reklama blokira i stranice sa sadržajem za odrasle.

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 176.103.130.132 | 176.103.130.134 |
| 2a00:5a60::bad1:0ff | 2a00:5a60::bad2:0ff |

**Non-filtering**:

Omogućuju sigurnu i stabilnu konekciju ali ne filtriraju sadržaj.

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 176.103.130.136 | 176.103.130.137 |
| 2a00:5a60::01:0ff | 2a00:5a60::02:0ff |

<ul class="fa-ul">
  <li><span class="fa-li"><i class="fab fa-twitter"></i></span>
  DNSCrypt
  ```jinja default
  sdns://AQIAAAAAAAAAFDE3Ni4xMDMuMTMwLjEzMDo1NDQzINErR_JS3PLCu_iZEIbq95zkSV2LFsigxDIuUso_OQhzIjIuZG5zY3J5cHQuZGVmYXVsdC5uczEuYWRndWFyZC5jb20
  ```
  ```jinja family protection
  sdns://AQIAAAAAAAAAFDE3Ni4xMDMuMTMwLjEzMjo1NDQzILgxXdexS27jIKRw3C7Wsao5jMnlhvhdRUXWuMm1AFq6ITIuZG5zY3J5cHQuZmFtaWx5Lm5zMS5hZGd1YXJkLmNvbQ
  ```
  ```jinja non-filtering
  sdns://AQcAAAAAAAAAFDE3Ni4xMDMuMTMwLjEzNjo1NDQzILXoRNa4Oj4-EmjraB--pw3jxfpo29aIFB2_LsBmstr6JTIuZG5zY3J5cHQudW5maWx0ZXJlZC5uczEuYWRndWFyZC5jb20
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-exclamation-circle"></i></span>
  DNS-over-TLS
  ```jinja default
  dns.adguard.com
  ```
  ```jinja family filter
  dns-family.adguard.com
  ```
  ```jinja non-filtering
  dns-unfiltered.adguard.com
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-user-circle"></i></span>
  DNS-over-HTTPS
  ```jinja default
  https://dns.adguard.com/dns-query
  ```
  ```jinja family filter
  https://dns-family.adguard.com/dns-query
  ```
  ```jinja non-filtering
  https://dns-unfiltered.adguard.com/dns-query
  ```
  </li>
</ul>

# NextDNS

{% img /covers/dns/dns-next.png '"NextDNS""NextDNS"' %}

[NextDNS](https://nextdns.io/) štiti od raznih sigurnosnih pretnji, blokira reklame i trakere sa web sajtova i unutar aplikacija i nudi, također siguran i praćen internet za dijecu - na svim uređajima i na svim mrežama. *Ovo je moj lični favorit kojeg trenutno koristim*.

| Primarni DNS | Sekundarni DNS |
| :--- | :--- |
| 45.90.28.188 | 45.90.30.188 |
| 2a07:a8c0::ce:caf6 | 2a07:a8c1::ce:caf6 |

<ul class="fa-ul">
  <li><span class="fa-li"><i class="fas fa-dot-circle"></i></span>
  DNSCrypt
  ```
  sdns://AgEAAAAAAAAACjQ1LjkwLjI4LjAADmRucy5uZXh0ZG5zLmlvBy9jZWNhZjY
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-exclamation-circle"></i></span>
  DNS-over-TLS
  ```
  cecaf6.dns.nextdns.io
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-user-circle"></i></span>
  DNS-over-HTTPS
  ```
  https://dns.nextdns.io/cecaf6
  ```
  </li>
</ul>

Ako slučajno ne možeš podesiti NextDNS koristeći njihovu aplikaciju, DNS-over-TLS, DNS-over-HTTPS itd, imaš [opciju](https://my.nextdns.io/cecaf6/setup) da preko njihovih servera {% label success@linkaš %} svoju IP adresu.

# Dodatni DNS serveri

| Provajder | Primarni DNS | Sekundarni DNS |
| :--- | ---: | ---: |
| [Verisign](https://www.verisign.com/en_US/security-services/public-dns/index.xhtml) | 64.6.64.6 | 64.6.65.6 |
| [Alternate DNS](https://alternate-dns.com/) | 23.253.163.53 | 98.101.242.72 |
| [DNS.WATCH](https://dns.watch/) | 84.200.69.80 | 84.200.70.40 |
| [Comodo Secure DNS](https://www.comodo.com/secure-dns/) | 8.26.56.26 | 8.20.247.20 |
| [FreeDNS](https://freedns.zone/en/) | 45.33.97.5 | 37.235.1.177 |
| [OpenNic](https://www.opennic.org/) | 192.71.245.208 | 94.247.43.254 |
| [SafeDNS](https://www.safedns.com/) | 195.46.39.39 | 195.46.39.40 |

Posjetite njihove web stranice <small>(klikom na naziv provajdera <span style="color:#B2A571;" role="img" aria-label="+"><i class="fa fa-external-link-alt fa-fw"></i></span> u tabeli)</small> za više informacija, te da provjerite opcije koje nude.

# Korisni linkovi

<div class="centar">
{% btn https://www.dnsperf.com/, &nbsp;DNSpref, fa fa-wrench fa-fw, DNSpref %}
{% btn https://speedtest.net/, &nbsp;Speedtest.net, fa fa-wrench fa-fw, Speedtest.net %}
{% btn https://www.dnsleaktest.com/, &nbsp;DNS leak test, fa fa-wrench fa-fw, DNS leak test %}
{% btn https://dnscrypt.info/, &nbsp;DNSCrypt, fa fa-wrench fa-fw, DNSCrypt %}
</div>
