---
title: Mikrotik Decode
date: 2020-05-04 20:25:00
updated: 2020-05-04 20:25:00
categories:
  - Research
tags:
  - mikrotik
  - python
  - decode
---

<a href="/mikrotik_decode_scripts" aria-label="pročitaj" title="Mikrotik Decode" data-pjax-state="">{% img /covers/supout.webp 'Mikrotik Decode" "Mikrotik Decode"' %}</a>

<!--more-->

# Intro

RouterOS ekosistem:

{% img /images/routeros_ecosystem.png '"ros ekosistem""ros shema"' %}

Skripte koje se koriste za dekodiranje mikrotikovih paketa poput `.npk` `.rif` `.backup`:
- decode_backup.py
- decode_blank.py
- decode_user.py {% label danger@^depricated! za ^6.41 %}
- decode_supout.py

 Skripte su u `python2` kod formatu, te zahtjevaju da ista verzija Python-a bude instalisana. Ako iz nekog razloga isključivo koristite `python3` skripte možete "prevesti" sa `2to3` komandom [lib2to3](https://docs.python.org/3/library/2to3.html#module-lib2to3), koja se isto nalazi u Python root datoteci `/Tools/scripts`.

# Dekodiranje Backup fajla

Skripta dekodira `.backup` fajl kreiran u mikrotik uređaju: `/system backup save name=fajl.backup` u folder koji sadrzi `.dat` i `.idx` fajlove komplet konfiguracije.

{% note success no-icon decode_backup.py skripta %}
```python
#!/usr/bin/python2

import sys,os,re
from struct import unpack

if len(sys.argv) > 1:
	if len(sys.argv) > 2:
		dir = sys.argv[2]
	else:
		dir = sys.argv[1]+"_contents/"
else:
	raise Exception("Koristi: "+sys.argv[0]+" <RouterOS.backup> [datoteka]")


if not os.access(sys.argv[1], os.R_OK):
	raise Exception("Ne mogu otvoriti fajl "+sys.argv[1])

if not os.path.exists(dir):
    os.makedirs(dir)

if not os.access(dir, os.W_OK):
	raise Exception("Datoteka "+dir+" ne dopušta uređivanje")

if os.listdir(dir)!=[]:
	raise Exception("Datoteka "+dir+" nije prazna")

realsize=os.path.getsize(sys.argv[1])

with open(sys.argv[1], 'rb') as backup:
	hdr = backup.read(4)

	if realsize>=8 and hdr == "\xEF\xA8\x91\x72":
		raise Exception("Enkriptovani RouterOS backup fajlovi nisu podržani.")

	if realsize<8 or hdr != "\x88\xAC\xA1\xB1":
		raise Exception("Nije RouterOS backup fajl.")

	matchsize, = unpack("<I",backup.read(4))
	if matchsize != realsize:
		raise Exception("Fajl je oštećen.")

	while backup.tell()+4 < matchsize:

		file_name = backup.read(unpack("<I",backup.read(4))[0])
		index_cont = backup.read(unpack("<I",backup.read(4))[0])
		data_cont = backup.read(unpack("<I",backup.read(4))[0])
		print '%3d entries, %8d bytes,  ./%s' % (len(index_cont)/4, len(data_cont),file_name)

		file_name=re.sub('\.{2,}','_',file_name);

		try:
			os.makedirs(dir+"/"+os.path.dirname(file_name))
		except OSError as exc:
			if exc.errno == 17:
				pass

		fo = open(dir+"/"+file_name+".idx", "wb")
		fo.write(index_cont);
		fo.close()

		fo = open(dir+"/"+file_name+".dat", "wb")
		fo.write(data_cont);
		fo.close()
```
{% endnote %}

# Dekodiranje .dat i .idx fajlova iz backup-a

Šablon za dekodiranje / čitanje binarnih fajlova iz datoteke fajlova formiranih > `decode_backup.py` skriptom:

{% note default no-icon decode_blank.py %}
```python decode_blank
#!/usr/bin/python

from mt_dat_decoder import MTConfig

def onlyPrintable (s):
	 return s if all(c in map(chr, range(32, 127)) for c in s) else "!{non-printable string of len %i}" % len(s)

dir="."
database="dhcp/client"

conf = MTConfig(dir+"/"+database+".dat",dir+"/"+database+".idx")

#conf.returnTypes = True
conf.mapBlockNames( {
	"_be" : "Use Peer NTP"
	} )

conf.addFilter (0, lambda x: "No" ) # addFilter() radi sa tipovima blokova ili data tipovima
conf.addFilter (1, lambda x: "Yes" )
conf.addFilter (str, onlyPrintable)
conf.addParser (0x12, lambda x: "0x%x" % x ) # addParser() radi sa id blokova
#conf.addParser ("_u2", lambda x: x )

for record in conf:
	print(record)
```
{% endnote %}

Primjer skripte za dekodiranje / čitanje `user.dat` fajla:

{% note success no-icon decode_user.py %}
```python
#!/usr/bin/python

import sys,md5,datetime
from socket import inet_ntop,AF_INET,AF_INET6

from mt_dat_decoder import MTConfig

def xor(data, key):
    return str(bytearray(a^b for a, b in zip(*map(bytearray, [data, key])))).split("\x00",2)[0]

def parseIPv4(data):
	return inet_ntop(AF_INET, chr(data & 0xFF) + chr(data >> 8 & 0xFF)+ chr(data >> 16 & 0xFF)+ chr(data >> 24 & 0xFF))

def parseIPv6(data):
	return inet_ntop(AF_INET6,''.join(map(chr,data)))

def parseAddressNet(data):
	ip = None
	netmask = None

	for blocktype in data:
		if blocktype == "_u1" or blocktype == "_u5" :
			ip = parseIPv4(data[blocktype])
		elif blocktype =="_a3":
				ip = parseIPv6(data[blocktype])
		elif blocktype =="_u4":
			netmask = str(data[blocktype])
		elif blocktype == "_u2" or blocktype == "_u6" :
			netmask = str(bin(data[blocktype]).count("1"))

	if ip and netmask:
		return ip+"/"+netmask


def parseMTdate(data):
	return datetime.datetime.fromtimestamp(data).strftime('%b/%d/%Y %H:%M:%S')

def parseMTusergroup(data):
	# ovisi o group.dat ;)
	groups = ["read","write","full"]
	if data>=0 and data<len(groups):
		return groups[data]


if len(sys.argv) > 1:
	dir = sys.argv[1]
else:
	dir = "."

database="user"

conf = MTConfig(dir+"/"+database+".dat",dir+"/"+database+".idx")

conf.mapBlockNames( {0xb:"permissions", 0x1f:"last_login", 0x1c:"password_set",
					0x1:"username",0x11:"password",0x2:"group",0x12:"groupname",
					0x10:"allowed_addresses",0x5:"allowed_ip4", 0x6:"allowed_net4" })

conf.addParser (0x1f, parseMTdate)
conf.addParser (0x12, parseMTusergroup)
conf.addParser (0xb, lambda x: "%x" % x)
conf.addParser (0x5, parseIPv4)
conf.addParser (0x6, parseIPv4)
conf.addParser (0x10, parseAddressNet)


for record in conf:
	if "username" in record and "password" in record:
		record["#key"]=md5.new(record["username"]+"283i4jfkai3389").digest()
		record["password"]=xor(record["password"],record["#key"]*16)
		record["#key"]=" ".join("{:02x}".format(ord(c)) for c in record["#key"])

	print(record)
```
{% endnote %}

{% note warning %}
- `decode_user.py` ne prikazuje password sa i nakon verzije *6.41*
  - Password sada koristi SHA256 & ECC.
{% endnote %}

# Dekodiranje Supout.rif fajla

supout.rif je original Mikrotik suport fajl koji generišemo unutar rutera komandom: `/system sup-output name=supout.rif`, ili klikom na istoimenu tipku unutar winbox-a. nakon čega se nalazi u fajlovima odakle ga možemo preuzeti ili poslati u mikrotik support.

Izgled izvana:

<picture>
  <source srcset="/images/supout_out.webp" media="(max-width: 100%)" type="image/webp" />
  <source srcset="/images/supout_out.png" media="(max-width: 100%)" type="image/png" />
  <img src="/images/supout_out.webp" title="Supout.rif" label="Supout.rif" />
</picture>

Dekodirati ga možemo i na [mikrotik.com](https://mikrotik.com) odnosno pregledati u supout.rif viewer-u.

<picture>
  <source srcset="/images/supout_mt.webp" media="(max-width: 100%)" type="image/webp" />
  <source srcset="/images/supout_mt.png" media="(max-width: 100%)" type="image/png" />
  <img src="/images/supout_mt.webp" title="Supout.rif" label="Supout.rif" />
</picture>

Dekodiranje skriptom:

{% note success no-icon decode_supout.py %}
```python
#!/usr/bin/python

import sys,os,base64,zlib,re

#           111111 11112222
#01234567 89012345 67890123
#cdefgh56 78abGH12 34ABCDEF  (from)
#abcdefgh 12345678 ABCDEFGH  (to)
#revtribitmap=[2,3,4,5,6,7,12,13,14,15,0,1,22,23,8,9,10,11,16,17,18,19,20,21]
tribitmap=[10,11,0,1,2,3,4,5,14,15,16,17,6,7,8,9,18,19,20,21,22,23,12,13]

def tribit(content):
	#origlen=len(content)
	#while len(content)%3:
	#	content= content + "\x00"

	result=""
	for i in xrange(0, len(content) - 1,3):
		goodtribit=0
		badtribit=ord(content[i])*0x10000+ord(content[i+1])*0x100+ord(content[i+2])
		for mangle in tribitmap:
			goodtribit = (goodtribit<<1) + (1 if ((badtribit & (0x800000>>mangle))>0) else 0)

		for move in [16,8,0]:
			result=result+chr((goodtribit >> move)& 0xff)

	return result
	#return result[0:origlen]

if len(sys.argv) > 1:
	if len(sys.argv) > 2:
		dir = sys.argv[2]
	else:
		dir = sys.argv[1]+"_content/"
else:
	raise Exception("Koristi: "+sys.argv[0]+" <supout.rif> [datoteku za ispis]")

if not os.access(sys.argv[1], os.R_OK):
	raise Exception("Ne mogu očitati fajl "+sys.argv[1])

if not os.path.exists(dir):
    os.makedirs(dir)

if not os.access(dir, os.W_OK):
	raise Exception("Datoteka "+dir+" nije pristupačna")

if os.listdir(dir)!=[]:
	raise Exception("Datoteka "+dir+" nije prazna")

i=0
with open(sys.argv[1], 'r') as my_file:
    sections=my_file.read().replace("--BEGIN ROUTEROS SUPOUT SECTION\r\n",":").replace("--END ROUTEROS SUPOUT SECTION\r\n",":").replace("::",":").split(":")
    for sect in sections:
			if len(sect.strip())>0:
				i= i + 1

				out = tribit(base64.b64decode(sect.replace("=","A")))

				[name,zipped]=out.split("\x00",1);
				print '%02d' % i,name.ljust(23),
				if not i%3:
					print

				res=zlib.decompress(zipped)
				fo = open(dir+"/"+str(i).zfill(2)+"_"+re.sub('[^a-z0-9\.-]','_',name), "wb")
				fo.write(res);
				fo.close()
```
{% endnote %}

Sadržaj datoteke u koju smo "otpakovali":

<picture>
  <source srcset="/images/supout_list.webp" media="(max-width: 100%)" type="image/webp" />
  <source srcset="/images/supout_list.png" media="(max-width: 100%)" type="image/png" />
  <img src="/images/supout_list.webp" title="Supout.rif" label="Supout.rif" />
</picture>

Šta se nalazi u `supout.rif`?

- tvoja kompletna konfiguracija
- /proc/ datoteka
- adrese memorije
- tvoj log
- i više
