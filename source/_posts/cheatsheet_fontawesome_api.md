---
title: Font Awesome Cheatsheet & API
date: 2020-06-23 11:09:00
updated: 2020-06-23 11:09:00
categories:
  - Cheatsheets
tags:
  - css
  - ikone
  - api
---

<a href="/cheatsheet_fontawesome_api" aria-label="pročitaj" title="Font Awesome Cheatsheet" data-pjax-state="">{% img /covers/font-awesome.webp '"Font Awesome Cheatsheet" "Font Awesome Cheatsheet"' %}</a>

<!--more-->

# Cheatsheet

Kompilirana pregledna lista Font Awesome ikona, popularnog, možda i najviše korištenog seta; *cheatsheet* za {% label success@free %}, `5.14` verziju se nalazi na linku:

<div class="centar">
{% btn https://static.monkeyshub.space/apps/fontawesome/, &nbsp;Font Awesome Cheatsheet, fa fa-calculator fa-fw, Font Awesome Cheatsheet %}
{% btn https://fontawesome.com/, &nbsp;Font Awesome Oficijelni sajt, fab fa-font-awesome fa-fw, Font Awesome Oficijelni sajt %}
</div>

# API & Upotreba

Ikone možeš pozvati sa ovog servera, primjer linkanja unutar `html`-a:

```html <link href="" rel="stylesheet">

  <head>
  <!-- all.min.css -->
  <link href="//static.monkeyshub.space/apps/fontawesome/css/all.min.css" rel="stylesheet">
  <style type="text/css">
    <!-- primjer dodatnog stajlinga svih ikona -->
    i.fas,
    i.fab {
      border: 1px solid #cc6cc9;
    }
    <!--dodatni stajling jedne ikone -->
    .fa-fish {
      color: #4F53CD;
    }
    .fa-frog {
      color: #39631B;
    }
    .fa-user-ninja.ispario {
      opacity: 0.0;
    }
    .fa-facebook {
      color: rgb(35, 100, 180);
    }
  </style>
  </head>
  <body>
  <i class="fas fa-fish"></i>
  <i class="fas fa-frog"></i>
  <i class="fas fa-user-ninja ispario"></i>
  <i class="fab fa-facebook"></i>
  </body>
```

**Promjena veličine ikona**:

```html
<div style="font-size: 24px;">
  <i class="fas fa-camera fa-xs"></i>
  <i class="fas fa-camera fa-sm"></i>
  <i class="fas fa-camera fa-lg"></i>
  <i class="fas fa-camera fa-2x"></i>
  <i class="fas fa-camera fa-3x"></i>
  <i class="fas fa-camera fa-5x"></i>
  <i class="fas fa-camera fa-7x"></i>
  <i class="fas fa-camera fa-10x"></i>
</div>
```
**Fiksna veličina ikone**:

```html
<div style="font-size: 1rem;">
  <div><i class="fas fa-skating fa-fw" style="background:DodgerBlue"></i> Skating</div>
  <div><i class="fas fa-skiing fa-fw" style="background:SkyBlue"></i> Skiing</div>
  <div><i class="fas fa-skiing-nordic fa-fw" style="background:DodgerBlue"></i> Nordic Skiing</div>
  <div><i class="fas fa-snowboarding fa-fw" style="background:SkyBlue"></i> Snowboarding</div>
  <div><i class="fas fa-snowplow fa-fw" style="background:DodgerBlue"></i> Snowplow</div>
</div>
```

**Kako koristiti ikone u listama `ul` `ol`**:
```html
<ul class="fa-ul">
  <li><span class="fa-li"><i class="fas fa-check-square"></i></span>List icons can</li>
  <li><span class="fa-li"><i class="fas fa-check-square"></i></span>be used to</li>
  <li><span class="fa-li"><i class="fas fa-spinner fa-pulse"></i></span>replace bullets</li>
  <li><span class="fa-li"><i class="far fa-square"></i></span>in lists</li>
</ul>
```

**Par primjera sa `pull`, `stack`, `rotatate`, `pulse` & `spin` opcijama**:

| i | class | komentar |
| --- | :--- | ---: |
| <span style="color:#93349A;" aria-label="awesome-ikona" role="img" ><i class="fa fa-horse fa-rotate-90 fa-flip-both fa-2x fa-fw" ></i></span> | `fa-rotate-{90,180,270}` `fa-flip-{horizontal, vertical, both}` | za rotaciju. |
| <span style="color:#BA4D7B;" aria-label="awesome-ikona" role="img" ><i class="fas fa-spinner fa-pulse fa-2x" ></i> <br> <i class="fa fa-spider fa-spin fa-2x" ></i></span> | `fa-spin` `fa-puls` | za animaciju pri `display: block;`|

<hr>

<div>
 <span aria-label="awesome-ikona" role="img" >
  <i class="fas fa-angle-double-right fa-2x fa-pull-right fa-border"></i>
 </span>
 fa-pull-{right, left} & fa-border za blokove i sl.
 </div>

<hr>

<div>
 <span class="fa-stack fa-2x" aria-label="awesome-ikona" role="img" >
 <i class="fa fa-square fa-stack-2x" ></i>
 <i style="color:#BA4D7B;" class="fa fa-terminal fa-stack-1x fa-inverse" ></i>
 </span>
 fa-stack za grupisanje.
</div>

<hr>

## Screenshots

{% gp 2-1 %}
<img src="https://static.monkeyshub.space/fragments/emoji/fa1.jpg" title="fa_ikone" label="fa_ikone" />
<img src="https://static.monkeyshub.space/fragments/emoji/fa2.jpg" title="fa_ikone" label="fa_ikone" />
{% endgp %}
