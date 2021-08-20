---
title: Linux konzolne komande
date: 2020-05-29 14:01:20
updated: 2020-05-29 14:01:20
categories:
  - Resources
tags:
  - linux
  - cli
  - natuknice
---

```
/bin/zsh
omz ..
         __                                     __   
  ____  / /_     ____ ___  __  __   ____  _____/ /_  
 / __ \/ __ \   / __ `__ \/ / / /  /_  / / ___/ __ \
/ /_/ / / / /  / / / / / / /_/ /    / /_(__  ) / / /
\____/_/ /_/  /_/ /_/ /_/\__, /    /___/____/_/ /_/  
                        /____/                       
```

<div class="centar">
<kbd>z</kbd><kbd>TAB</kbd><kbd>ENTER</kbd>
</div>

<!--more-->

# sudo
```zsh ponavlja zadnju liniju kao sudo
sudo !!
```

# http server
```sh python 2
python -m SimpleHTTPServer
```
```sh python 3
python -m http.server
```

# mount
```sh tabelarni prikaz > column
column -t /proc/mounts
```
```sh tabelarni prikaz > mount
$mount | column -t
```

# wget
```sh full sajt dowload + konverzija linkova
wget -mkEpnp websajt.ba   # robots=off -U mozilla
```
```sh extract tarball bez lokalnog cuvanja
wget -qO - "http://www.tarball.ba/tarball.gz" | tar zxvf -
```

# ping
```sh ping svakih 60s sa audio dojavom kada adresa bude pingabilna
ping -i 60 -a IP_address
```

# procesi
```sh top 10 procesa sortiranih po crpljenju memorije
ps aux --sort -rss | head
```
```sh top 10 procesa sortiranih po crpljenju memorije
ps axo %mem,pid,euser,cmd | sort -nr | head -n 10
```
```sh grep procesa za minus sam grep
ps aux | grep [p]rocess-name
```
```sh trazenje aktivnog procesa bez kesiranja pretrage
ps -ef | awk '/process-name/ && !/awk/ {print}'   # print$2 lista samo pidove
```

# remove & delete
```sh brisanje svih fajlova u folderu osim !(x.y)
rm -f !(jedinifajlkojinecebitiobrisan.txt)
```
```sh brisanje svih fajlova u folderu osim
find . ! -name <FILENAME> -delete
```
```sh brisanje svih fajlova u folderu osim !(x.y)
rm -f rm !(*.txt|*.html|*.json|*.ico)
```

# skriptanje
```sh snimanje prethodne komande u skriptu (!!:q za komentiranje linija)
echo "!!" > foo.sh
```
```sh snimanje prethodne komande u skriptu
history -1 | sed -e 's/^\ [0-9]*\ *//g' > foo.sh  
```
# ls & ss
```sh prikaz aplikacija koje koriste internet trenutno
ss -p
```
```sh prikaz aplikacija koje koriste internet trenutno
lsof -P -i -n
```
```sh prikaz samo naziva aplikacija koje koriste internet trenutno
lsof -P -i -n | cut -f 1 -d " "| uniq | tail -n +2
```
```sh tree pregled subfoldera
ls -R | grep ":$" | sed -e 's/:$//' -e 's/[^-][^\/]*\//--/g' -e 's/^/ /' -e 's/-/|/'
```
```sh ko koristi ovaj port?
lsof -i tcp:8080
```

# kill
```sh kill fajla / skripte / procesa koji ti ide na zivce
fuser -k filename
```
```sh stop & suspend
killall -STOP -m firefox
killall -CONT -m firefox  #rezultira sa Zero cpu
```

# find
```sh pronalazi duple fajlove, prvo prema velicini, zatim po md5sum
find -not -empty -type f -printf "%-30s'\t\"%h/%f\"\n" | sort -rn -t$'\t' | uniq -w30 -D | cut -f 2 -d $'\t' | xargs md5sum | sort | uniq -w32 --all-repeated=separate
```

```sh brise prazne foldere unutar radnog
find . -type d -empty -delete
```
```sh pronadji fajlove koji su izmjenjeni u zadnjih 60min
find / -mmin 60 -type f
```

# mkdir & cd
```sh mkdir & cd u njega / portable
mkdir /home/banana/doc/monkey && cd $_
```
```sh sa nizom subfoldera + 4 foldera u zadnjem
mkdir -p duga/direktorij/putanja/{abc,cde,defc,efca}
```
```sh wordpress style
mkdir -p uploads/{2017..2020}/{01..12}/{1..31}
```
# održavanje konzolne
```sh ocisti ili resetuj
clear
reset
```
<kbd>Ctrl</kbd>+<kbd>l</kbd>
```sh izbrisi historiju
history -d [broj linije]
```
<kbd>Backspace</kbd>

# resolve host i drill domain
```sh resolve via getent
#resolve ~ getent
getent hosts monkeyshub.space
```
```sh drill domenu
#dig ~ drill
drill @nameserver TXT monkeyshub.space
#ako ne specificiramo dns server koristit ce one iz /etc/resolv.conf
```

# IP API (ipify.org)
```sh shell
ip=$(curl -s https://api.ipify.org)
echo "moja javna IP adresa je: $ip"
```
```python python
from requests import get

ip = get('https://api.ipify.org').text
print('moja javna IP adresa je: {}'.format(ip))
```
```javascript javascript
<script type="application/javascript">
  function getIP(json) {
    document.write("moja javna IP adresa je: ", json.ip);
</script>
<script type="application/javascript" src="https://api.ipify.org?format=jsonp&callback=getIP"></script>
```
```js node.js
var http = require('http');
http.get({'host': 'api.ipify.org', 'port': 80, 'path': '/'}, function(resp) {
  resp.on('data', function(ip) {
    console.log("moja javna IP adresa je: " + ip);
  });
});
```
