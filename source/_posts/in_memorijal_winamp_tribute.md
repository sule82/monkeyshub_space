---
title: Winamp Tribute
date: 2020-06-10 00:00:00
updated: 2020-06-10 00:00:00
categories:
  - Projects
tags:
  - hosted
  - opensource
  - muzika
---
<a href="/in_memorijal_winamp_tribute" aria-label="pročitaj" title="Winamp Tribute" data-pjax-state="">{% img /covers/webamp.webp '"Winamp Tribute" "Winamp Tribute"' %}</a>

<!--more-->

# Webamp

Dobri stari Winamp integrirajte u svoj web sajt API pozivom:

```js
<script src="https://unpkg.com/webamp@1.4.0/built/webamp.bundle.min.js"></script>
```

Konstruišite playlist i rendajte skriptu:

```js
const Webamp = window.Webamp;
       new Webamp({
        initialTracks: [{
          metaData: {
             artist: "Jain",
              title: "Makeba"
          },
          url: "https://static.monkeyshub.space/soul/beat/makeba.opus",
          },
          {
          metaData: {
            artist: "None",
            title: "Badunkadunk"
          },
          url: "https://static.monkeyshub.space/soul/beat/badunkadunk.opus",
          }
        ],
      }).
    renderWhenReady(document.getElementById('app'));
```

Unutar html koda pozovite element po id-u:

```html
<div id="app" style="height: 50vh">
  !-- Webamp will attempt to center itself within this div -->
</div>
```
{% note info no-icon %}
U Winamp možete učitati playlist / muziku sa vašeg diska.
{% endnote%}
{% note danger no-icon %}
U slučaju da ne vidite player omogućite javaskript u browseru i refrešujte sa <kbd>F5</kbd>.
{% endnote %}

Webamp verziju pogledajte i koristite na linku:

<div class="centar">{% button https://static.monkeyshub.space/apps/amp/, &nbsp;Webamp, icon fa fa-headphones fa-fw, Webamp %}</div>

# Webamp .svg logo

**Krug + Diferenciajalni ZAP**:

<div class="centar">
<svg version="1.0" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="krug" width="100px" height="100px" viewBox="0 0 100 100">
<circle r="50" cx="50" cy="50" fill="#ea8500" />
</svg>
+
<svg version="1.0" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="zapOff" width="100px" height="100px" viewBox="0 0 100 100">
<path d="M 49.97 8 A 42 42 0 0 0 8 50 A 42 42 0 0 0 50 92 A 42 42 0 0 0 92 50 A 42 42 0 0 0 50 8 A 42 42 0 0 0 49.97 8 z M 78.21 20.51 L 57.98 45.28 L 71.33 45.73 L 23.85 78.43 L 43.82 55.25 L 27.34 52.57 L 78.21 20.51 z " fill="#383838" />
</svg>
</div>

```html Webamp logo
<svg version="1.0" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="webamp" width="100px" height="100px" viewBox="0 0 100 100">
<circle r="50" cx="50" cy="50" fill="#ea8500" />
<path d="M 49.97 8 A 42 42 0 0 0 8 50 A 42 42 0 0 0 50 92 A 42 42 0 0 0 92 50 A 42 42 0 0 0 50 8 A 42 42 0 0 0 49.97 8 z M 78.21 20.51 L 57.98 45.28 L 71.33 45.73 L 23.85 78.43 L 43.82 55.25 L 27.34 52.57 L 78.21 20.51 z " fill="#383838" />
</svg>
```

---

<div class="centar">
<svg version="1.0" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="webamp" width="100px" height="100px" viewBox="0 0 100 100">
<circle r="50" cx="50" cy="50" fill="#ea8500" />
<path d="M 49.97 8 A 42 42 0 0 0 8 50 A 42 42 0 0 0 50 92 A 42 42 0 0 0 92 50 A 42 42 0 0 0 50 8 A 42 42 0 0 0 49.97 8 z M 78.21 20.51 L 57.98 45.28 L 71.33 45.73 L 23.85 78.43 L 43.82 55.25 L 27.34 52.57 L 78.21 20.51 z " fill="#383838" />
</div>

---

**Krug outline + Integralni ZAP**:

<div class="centar">
<svg version="1.0" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="webamp" width="100px" height="100px" viewBox="0 0 100 100">
<circle r="50" cx="50" cy="50" fill="#383838" stroke="#ea8500" stroke-width="6" stroke-opacity="1" transform="translate(3,97) scale(0.94,-0.94)" />
</svg>
+
<svg version="1.0" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="webamp" width="100px" height="100px" viewBox="0 0 100 100">
<path d="M 78,21 57.53,45.38 71.04,45.82 23,78 43.21,55.19 26.53,52.55 Z" fill="#ea8500" />
</svg>
</div>

```html Webamp logo v2
<svg version="1.0" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="webamp" width="100px" height="100px" viewBox="0 0 100 100">
<circle r="50" cx="50" cy="50" fill="#383838" stroke="#ea8500" stroke-width="6" stroke-opacity="1" transform="translate(3,97) scale(0.94,-0.94)" />
<path d="M 78,21 57.53,45.38 71.04,45.82 23,78 43.21,55.19 26.53,52.55 Z" fill="#ea8500" />
</svg>
```

---

<div class="centar">
<svg version="1.0" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="webamp" width="100px" height="100px" viewBox="0 0 100 100">
<circle r="50" cx="50" cy="50" fill="#383838" stroke="#ea8500" stroke-width="6" stroke-opacity="1" transform="translate(3,97) scale(0.94,-0.94)" />
<path d="M 78,21 57.53,45.38 71.04,45.82 23,78 43.21,55.19 26.53,52.55 Z" fill="#ea8500" />
</svg>
</div>

---

{% cq %}
読んでくれてありがとう
{% endcq %}
