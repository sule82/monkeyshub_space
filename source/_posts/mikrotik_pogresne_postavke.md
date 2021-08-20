---
title: Pogrešne postavke na Mikrotik ruteru
date: 2020-08-20 12:00:00
updated: 2020-08-20 12:00:00
categories:
  - Research
tags:
  - mikrotik
  - terminal
  - pretraga
---

<a href="mikrotik_pogresne_postavke" aria-label="pročitaj" title="Googlanje">{% img /covers/jungle.webp '"Googlanje""Googlanje"' %}</a>

<!--more-->

Nekoliko uobičajenih postavki / scenarija na koje sam naišao googlanjem, a koja su pogrešna ili nisu optimalna, te vode do opterećenja procesora, i drugih nepravilnosti u radu Mikrotik rutera. Svaka postavka je sa primjerima (preuzetih sa foruma i sl.) i rješenjem problema.

Sama rješenja, naravno, mogu služiti kao reference za konfiguraciju datih postavki.

# Layer7 protokol

{% note default no-icon %}
Inače kada se radi o RegEX rijetko možete pronaći konkretna rješenja - postavke na netu, i nabasati ćete na moguću postavku blokiranja npr youtube i facebook-a po sedmom lajeru koja izgleda ovako otprilike:

<ul class="fa-ul">
  <li><span class="fa-li"><i class="fas fa-cog"></i></span>
  firewall layer7-protocol regex input:
  {% codeblock lang:routeros firewall layer7-protocol https://wiki.mikrotik.com/wiki/Manual:IP/Firewall/L7 Manual:IP/Firewall/L7 %}
  /ip firewall layer7-protocol
  add name=youtube regexp="^.+(youtube).*\$"
  add name=facebook regexp="^.+(facebook).*\$"
  {% endcodeblock %}
  </li>
  <li><span class="fa-li"><i class="fas fa-cog"></i></span>
  firewall filter drop input:
  {% codeblock lang:routeros firewall filter https://wiki.mikrotik.com/wiki/Manual:IP/Firewall Manual:IP/Firewall %}
  /ip firewall filter
  add action=drop chain=forward layer7-protocol=facebook
  add action=drop chain=forward layer7-protocol=youtube
  {% endcodeblock %}
  </li>
</ul>

{% endnote %}

{% note danger no-icon %}

### Problem

Visoki **CPU** load, povećana latencija, gubitak paketa, jitter, youtube i facebook **nisu** blokirani, iz sljedećih razloga:

<ul class="fa-ul">
  <li><span class="fa-li pink"><i class="fas fa-poll"></i></span>
  Svaka konekcija se provjerava svaki puta nanovo
  </li>
  <li><span class="fa-li pink"><i class="fas fa-poll"></i></span>
  Layer7 se provjerava na pogrešnom mijestu i naspram ukupnog prometa
  </li>
</ul>

Dijagnozu možete napraviti sa komandom `/tool profile`.

Layer7 je protokol koji pretražuje šablone u ICMP/TCP/UDP stream-ovima. Na ovaj način pokrenut skuplja slijedećih 10 paketa ili 2KB konkecije i traži šablon u prikupljenim podacima, te svi Layer7 šabloni koje možete pronaći na netu su upravo dizajnirani da rade samo sa tih prvih 10 paketa odnosno 2KB konekcije.

{% endnote %}

{% note success no-icon %}

### Rješenje

Problem rješavamo prerutiranjem markiranih konekcija unutar firewall mangle konfiguracije:

<ul class="fa-ul">
  <li><span class="fa-li green"><i class="fas fa-cog"></i></span>
  {% codeblock lang:routeros firewall mangle https://wiki.mikrotik.com/wiki/Manual:IP/Firewall Manual:IP/Firewall %}
  /ip firewall mangle
  add action=mark-connection chain=prerouting protocol=udp dst-port=53 connection-mark=no-mark layer7-protocol=youtube new-connection-mark=youtube_konekcija passthrough=yes
  add action=mark-packet chain=prerouting connection-mark=youtube_konekcija new-packet-mark=youtube_paket
  {% endcodeblock %}
  </li>
  <li><span class="fa-li green"><i class="fas fa-cog"></i></span>
  i dodamo firewall filter:
  {% codeblock lang:routeros firewall filter https://wiki.mikrotik.com/wiki/Manual:IP/Firewall Manual:IP/Firewall %}
  /ip firewall filter
  add action=drop chain=forward packet-mark=youtube_paket
  add action=drop chain=input packet-mark=youtube_paket
  {% endcodeblock %}
  </li>
</ul>

Iste postavke ponovimo i za *facebook* jer smo youtube i facebook uzeli za primjer :speak_no_evil:.

{% endnote %}

# Queues ne funkcioniše pravilno

{% note default no-icon %}

Česta i pogrešna postavka ograničenja prometa:

{% codeblock lang:routeros queues https://wiki.mikrotik.com/wiki/Manual:Queue Manual:Queue %}
  /ip address
  add address=10.0.0.1/24 interface=ether-prvi
  add address=10.0.1.1/24 interface=ether-drugi

  /ip firewall filter
  add chain=forward action=fasttrack-connection connection-state=established,related
  add chain=forward action=accept connection-state=established,related

  /queue simple
  add max-limit=10M/10M dst=10.0.0.2/32
  add max-limit=10M/10M dst=10.0.0.3/32
  add max-limit=10M/10M dst=10.0.0.4/32
{% endcodeblock %}

{% endnote %}

{% note danger no-icon %}

### Problem

Queues rade sa uključenim `/tool torch` ili ako je fasttrack isključen, ali i tada dohvata samo download promet. Promet unutar lokalne mreže je također ograničen.
Analiza preko queues brojača sa fasttrack-connection pravilom.

<ul class="fa-ul">
  <li><span class="fa-li pink"><i class="fas fa-poll"></i></span>
  Fasttrack pravilo je specificiran za sav promet
  </li>
  <li><span class="fa-li pink"><i class="fas fa-poll"></i></span>
  Simple queue `target` mora biti specificiran (postavka cilja je jedina koja određuje simple queue direkciju)
  </li>
  <ul class="fa-ul tekst-77-7">
  <li><span class="fa-li pink"><i class="fa fa-plus-square"></i></span>
  ako `target` nije određen (ako je 0.0.0.0/0) sav promet će biti dohvaćen u download sekciji
  </li>
  <li><span class="fa-li pink"><i class="fa fa-plus-square"></i></span>
  simple queue `dst` postavka je samo dodatni filter, ne određuje direkciju
  </li>
  </ul>
</ul>

{% endnote %}

{% note success no-icon %}

### Rješenje

Sljedeća konfiguracija otklanja navedeni problem:

{% codeblock lang:routeros queues https://wiki.mikrotik.com/wiki/Manual:Queue Manual:Queue %}
/ip firewall filter
add chain=forward action=fasttrack-connection connection-state=established,related in-interface=ether-prvi out-interface=ether-drugi
add chain=forward action=fasttrack-connection connection-state=established,related in-interface=ether-drugi out-interface=ether-prvi
add chain=forward action=accept connection-state=established,related

/queue simple
add max-limit=10M/10M target=10.0.0.2/32
add max-limit=10M/10M target=10.0.0.3/32
add max-limit=10M/10M target=10.0.0.4/32
{% endcodeblock %}

{% endnote %}

# Opterećenje na PPPoE serveru 1

{% note danger no-icon %}

<ul class="fa-ul">
  <li><span class="fa-li"><i class="fas fa-cog"></i></span>
  3000 pppoe-klijenata u 10.0.0.0/20 mreži
  </li>
  <li>
  konkektovani kroz 172.16.x.0/24 mreže do drugih servera sa 10.x.0.0/20 PPPoE klijent mrežom.
  </li>
  <li>
  Svi PPPoE serveri i gateway u istoj backbone regiji sa redistribuiranim konektovanim rutama
  </li>
</ul>

{% codeblock lang:routeros OSPF https://wiki.mikrotik.com/wiki/Manual:Routing/OSPF Manual:Routing/OSPF %}
/routing ospf network
add network=172.16.1.0/24 area=backbone
add network=10.0.0.0/20 area=backbone
{% endcodeblock %}

{% endnote %}

{% note danger no-icon %}

### Problem

CPU operećen, PPPoE klijenti se diskonektuju, klijenti ne mogu dosegnuti predviđenu brzinu interneta, ponekada se ne mogu niti konektovati.

Dijagnoza sa `/tool profile` prikazuje “routing” proces zakucan, na jednoj CPU jezgri, do 100% cijelo vrijeme, i ostale jezgre dostižu 100% sa ppp i networking procesima.

<ul class="fa-ul">
  <li><span class="fa-li pink"><i class="fas fa-poll"></i></span>
  OSPF je spamovan sa PPPoE klijent /32 route update
  </li>
</ul>

<span class="tekst-77-7">
Za bolje shvatanje problema evo par natuknica odnosa OSPF i PPPoE:
<ul><li>Svi dinamički routing protokoli (update routing tabela i protokol kalkulacije) su limitirani na jednu jezgru</li>
<li>Svaki put kada se pppoe-client konektuje / diskonektuje kreira ili briše /32 rutu, te ako je ona dio OSPF mreže inicira OSPF update</li>
<li>Svaki put kada se pppoe-client konektuje / diskonektuje pppoe-interface se dodaje ili briše sa OSPF interfejsa, ovo također inicira OSPF update</li></ul>
</span>

{% endnote %}

{% note success no-icon %}

### Rješenje

Pasivni OSPF interfejs i STUB regioni.

{% codeblock lang:routeros OSPF https://wiki.mikrotik.com/wiki/Manual:Routing/OSPF Manual:Routing/OSPF %}
/routing ospf area
add area-id=0.0.0.1 authentication=none name=pppoe1 type=stub

/routing ospf network
add area=pppoe1 network=10.0.0.0/20

/routing ospf area range
add advertise=yes area=pppoe1 range=10.0.0.0/20

/routing ospf interface
add interface=all passive=yes
{% endcodeblock %}

{% endnote %}

# Opterećenje na PPPoE serveru 2

{% note default no-icon %}

<ul class="fa-ul">
  <li><span class="fa-li pink"><i class="fas fa-poll"></i></span>
  3000 pppoe-klijenata u 10.0.0.0/20 mreži
  </li>
  <li><span class="fa-li pink"><i class="fas fa-poll"></i></span>
  Statička javna IP adresa na javnom interfejsu
  </li>
  <li><span class="fa-li pink"><i class="fas fa-poll"></i></span>
  Masquerade pravilo
  </li>
  <li><span class="fa-li pink"><i class="fas fa-poll"></i></span>
  Nema drugog firewall-a
  </li>
</ul>

{% endnote %}

{% note default no-icon %}

### Problem

CPU operećen, PPPoE klijenti se diskonektuju, klijenti ne mogu dosegnuti predviđenu brzinu interneta, ponekada se ne mogu niti konektovati.

Dijagnoza sa `/tool profile` prikazuje dominaciju firewall procesa.

Razlog je nepravilno korištenje `action=masquerade`.
Firewall NAT `action=masquerade` je jedinatvena podverzija `action=srcnat`, i dizajnirana je za korištenje u situacijama gdje imamo nasumične promjene javne IP adrese, odnosno ako je javna IP dinamička. Svaki put kada se interfejs diskonektuje i / ili je adresa promjenjena ruter će tražiti i čistiti tracking konekcije konekcija relativnih za taj interfejs kako bi popravio vrijeme oporavka.

{% endnote %}

{% note success no-icon %}

### Rješenje

{% codeblock lang:routeros NAT https://wiki.mikrotik.com/wiki/Manual:IP/Firewall/NAT Manual:IP/Firewall/NAT %}
/ip firewall nat
add action=src-nat chain=srcnat out-interface=<Javni> to-addresses=<Javna_IP>
{% endcodeblock %}

Kada smo već kod `action=masquerade` evo još jedan scenarij:

{% endnote %}

# Leak lokalne IP adrese u javnu mrežu

{% note default no-icon %}

<ul class="fa-ul">
  <li><span class="fa-li"><i class="fas fa-cog"></i></span>
  Multi gateway uređaj sa policy routing i failover
  </li>
  <li><span class="fa-li"><i class="fas fa-cog"></i></span>
  Statička javna IP adresa na javnom interfejsu
  </li>
  <li><span class="fa-li"><i class="fas fa-cog"></i></span>
  `action=masquerade` firewall filter pravilo na svakom javnom interfejsu
  </li>
</ul>

{% endnote %}

{% note danger no-icon %}

### Problem

Nakon failover paketi sa privatnom adresom kao izvornom cure u javnu mrežu.
Dijagnoza sa `/tool sniffer`

<ul class="fa-ul">
  <li><span class="fa-li pink"><i class="fas fa-poll"></i></span>
  Nepravilno korištenje`action=masquerade` ili nedovoljan broj safeguard.
  </li>
</ul>

Kako smo već spomenuli, na diskonekciji svi relativni connection tracking unosi se brišu, i slijedeći paket iz svake očišćene konekcije doći će u firewall kao novi `connection-state=new`, pa se paketi rutiraju kroz alternativne rute kreirajući nove konekcije. Kada se primarni link vrati rutiranje se oporavlja na primarnom linku te paketi koji pripadaju postojećoj konekciji se šalju preko primarnog interfejsa bez maskiranja.

{% endnote %}

{% note success no-icon %}

### Rješenje

<ul class="fa-ul">
  <li><span class="fa-li green"><i class="fas fa-cog"></i></span>
  `action=src-nat` umjesto `action=masquerade` kada god je to moguće
  </li>
  <li><span class="fa-li green"><i class="fas fa-cog"></i></span>
  Dropanjem `connection-state=invalid` paketa
  </li>
  <li><span class="fa-li green"><i class="fas fa-cog"></i></span>
  Dropanjem `connection-state=new` paketa sa javnog interfejsa koji nemaju destinaciju `connection-nat-state=!dstnat`
  </li>
  <li><span class="fa-li green"><i class="fas fa-cog"></i></span>
  Kreiranje backup blackhole rute za svaki `routing-mark`
  </li>
</ul>

{% endnote %}

# cache DNS

{% note default no-icon %}

Javni DNS server, javna adresa na Internet interfejsu.

{% codeblock lang:routeros dns https://wiki.mikrotik.com/wiki/Manual:IP/DNS Manual:IP/DNS %}
/ip dns
set allow-remote-requests=yes servers=8.8.8.8

/ip firewall nat
add action=masquerade chain=srcnat out-interface=Internet

/ip firewall filter
add action=fasttrack-connection chain=forward connection-state=established,related
{% endcodeblock %}

{% endnote %}

{% note danger no-icon %}

### Problem

Visok CPU load, velika količina nepoznatog prometa na javnom interfejsu.

Dijagnoza sa `/tool torch` i `/tool profile`

<ul class="fa-ul">
  <li><span class="fa-li pink"><i class="fas fa-poll"></i></span>
  Ruter koristi javni otvoreni DNS resolver. Odgovara na rekursivne upite za hostove izvan svoje domene i koristi se u DNS amplifikacijskim napadima.
  </li>
</ul>

{% endnote %}

{% note success no-icon %}

### Rješenje

{% codeblock lang:routeros firewall filter https://wiki.mikrotik.com/wiki/Manual:IP/Firewall Manual:IP/Firewall %}
/ip firewall filter
add action=reject chain=input dst-port=53
protocol=udp reject-with=icmp-port-unreachable
add action=reject chain=input dst-port=53
protocol=tcp reject-with=icmp-port-unreachable
{% endcodeblock %}

{% endnote %}
