---
title: Mikrotik RouterOS DNS-over-HTTPS
date: 2020-07-26 12:01:00
updated: 2020-07-26 12:01:00
categories:
  - Research
tags:
  - mikrotik
  - network
  - doh
---

<a href="/mikrotik_dns_over_https" aria-label="pročitaj" title="RouterOS DNS-over-HTTPS" data-pjax-state="">{% img /covers/dns-mt.webp '"RouterOS DNS-over-HTTPS" "RouterOS DNS-over-HTTPS"' %}</a>

<!--more-->

# Info

Sa RouterOS verzijom v6.47<span style="color:#4A595D;" role="img" aria-label="up"><i class="fa fa-angle-up fa-xs"></i></span> dolazi dugo čekana DNS-over-HTTPS (*DoH*) integracija u Mikrotik ruterima.

DoH inače koristi HTTPS protokol pri slanju i primanju DNS zahtjeva za bolju data integraciju i eliminira "man in the middle attacks" (MITM). Nije kompatibilan sa FWD statičkim unosima.

Minimalno tri stvari / informacije su nam potrebne za konfiguraciju:

- root CA certifikat DOH servera
- hostname DoH servera
- jedan DNS server

# CA Certifikat

Do certifikata možemo doći na više načina, npr. koristeći Firefox Browser: `Page Info > Security > View Certificate`, gdje možete direktno skinuti, snimiti certifikat.  Nakon toga certifikat upload-ujete u Mikrotik ruter preko ftp-a ili koristeći winbox ili webfig komandom: `file > upload`.

{% img /covers/dns/cacert.png '"CA Certifikat" "CA Certifikat"' %}

Ako koristite openssl do certifikata možete doći ovako:

```sh
$openssl s_client -showcerts -servername server -connect server:443 > cacert.pem
```

## Importovanje CA certifikata u Mikrotik ruter

{% note warning no-icon %}
Uvijek, odnosno preporučuje se, koristiti web stranicu izdavača certifikata za preuzimanje: `fetch` / `curl` root CA certifikata, u nastavku primjer imortovanja Let's Encrypt certifikata koji koristi OpenDNS.
{% endnote %}

```routeros tool > fetch https://wiki.mikrotik.com/wiki/Manual:Tools/Fetch Manual:Tools/Fetch
/tool fetch url="https://letsencrypt.org/certs/lets-encrypt-x3-cross-signed.pem"
/certificate import file-name=lets-encrypt-x3-cross-signed.pem
```

Ili ako smatrate zgodnim, možete skinuti sve certifikate eksportovane iz Mozilla:

```sh curl https://curl.se/ca/ curl.se
$curl https://curl.se/ca/cacert.pem > cacert.pem
```
```routeros tool > fetch https://curl.se/ca/ curl.se
/tool fetch url="https://curl.se/ca/cacert.pem"
/certificate import file-name="cacert.pem"
```

# DNS konfiguracija na Mikrotik ruteru

Set-ovanje DNS-over-HTTPS server hostname-a, u ovom primjeru konfigurišemo prema {% label info@NextDNS %} parametrima:

```routeros ip > dns https://wiki.mikrotik.com/wiki/Manual:IP/DNS Manual:IP/DNS
/ip dns set use-doh-server=https://dns.nextdns.io/cecaf6 verify-doh-cert=yes
```
I naravno, potrebne su nam DNS IP adrese od provajdera:

```routeros ip > dns > static https://wiki.mikrotik.com/wiki/Manual:IP/DNS Manual:IP/DNS
/ip dns static
add address=45.90.28.0 disabled=no name=dns.nextdns.io ttl=1d
add address=45.90.30.0 disabled=no name=dns.nextdns.io ttl=1d
add address=2a07:a8c0:: disabled=no name=dns.nextdns.io ttl=1d \
    type=AAAA
add address=2a07:a8c1:: disabled=no name=dns.nextdns.io ttl=1d \
    type=AAAA
```

Postavke dinamičkih naspram gore navedenih statičkih IP adrese:

```routeros ip > dns https://wiki.mikrotik.com/wiki/Manual:IP/DNS Manual:IP/DNS
/ip dns
set servers="8.8.8.8, 8.4.4.8" \
allow-remote-requests=yes cache-max-ttl=1w cache-size=2048KiB \
max-concurrent-queries=100 max-concurrent-tcp-sessions=20 \
max-udp-packet-size=4096 query-server-timeout=2s \
query-total-timeout=10s
```

**Nakon set-ovanja DNS-over-HTTPS**:

```routeros
/ip dns cache flush
```
Za čišćenje postojećeg keša :zap:. Potrebno je i par minuta pričekati kako bi nove DNS postavke prodisale, i naravno vrijeme na ruteru treba da je usklađeno:
```routeros
/system ntp client
set enabled=yes server-dns-names=time.cloudflare.com.
```

# Debug

**Ispis grešaka u log**:

```routeros system > logging
/system logging
add topics=dns prefix=DNS404 action=memory
```

**Praćenje log-a**:

```routeros log >
/log
print follow where topics~"dns"
  # po želji dodajte error u topics za praćenje samo grešaka
```

**Riješenje za par problema**:

<ul class="fa-ul">
  <li><span class="fa-li"><i class="fas fa-exclamation-circle"></i></span>
  Certifikat ili nije importovan ili je neispravan
  ```
  connection error: SSL: handshake failed
  ```
  </li>
  <li><span class="fa-li"><i class="fas fa-exclamation-circle"></i></span>
  DoH server nije upisan valjano
  ```
  connection error: resolving error
  ```
  </li>
</ul>

**Debug pomoću `torch` alata**:

 Pokrenite Torch na WAN-u i gledajte da nema UDP i TCP konekcija na port 53.

**DNS leak test**:

```
www.dnsleaktest.com
```

# Cloudflare DOH primjer

**Konfiguracija DNS-a na Mikrotik ruteru**:

```routeros ip > dns
/ip dns
set allow-remote-requests=yes cache-max-ttl=1d cache-size=8192KiB max-concurrent-queries=100 max-concurrent-tcp-sessions=20 \
 max-udp-packet-size=4096 query-server-timeout=3s query-total-timeout=13s servers="" use-doh-server=\
 https://cloudflare-dns.com/dns-query verify-doh-cert=yes

/ip dns static
add address=104.16.248.249 disabled=no name=cloudflare-dns.com ttl=1d type=A
add address=104.16.249.249 disabled=no name=cloudflare-dns.com ttl=1d type=A
```
**Provjera konekcije na `1.1.1.1`**:

```sh link
https://1.1.1.1/help
```

**Rezultat koji trebate dobiti**:

{% img /covers/dns/1111.png '"oneoneoneone""oneoneoneone"' %}

```html share link
https://1.1.1.1/help#eyJpc0NmIjoiWWVzIiwiaXNEb3QiOiJObyIsImlzRG9oIjoiWWVzIiwicmVzb2x2ZXJJcC0xLjEuMS4xIjoiWWVzIiwicmVzb2x2ZXJJcC0xLjAuMC4xIjoiWWVzIiwicmVzb2x2ZXJJcC0yNjA2OjQ3MDA6NDcwMDo6MTExMSI6Ik5vIiwicmVzb2x2ZXJJcC0yNjA2OjQ3MDA6NDcwMDo6MTAwMSI6Ik5vIiwiZGF0YWNlbnRlckxvY2F0aW9uIjoiQlVEIiwiaXNXYXJwIjoiTm8iLCJpc3BOYW1lIjoiQ2xvdWRmbGFyZSIsImlzcEFzbiI6IjEzMzM1In0=
```
