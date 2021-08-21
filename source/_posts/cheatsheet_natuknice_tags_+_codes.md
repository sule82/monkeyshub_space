---
title: Tags + Codes Natuknice
date: 2020-04-05 22:11:20
updated: 2020-04-05 22:11:20
categories:
  - Cheatsheets
tags:
  - tags
  - natuknice
  - cli
---

<div class="centar tekst-150">
<span role="img" aria-label="giticons">:cd: :dvd: :floppy_disk: :camera: :video_camera: :movie_camera: :computer: :tv: :iphone: :phone: :telephone: :telephone_receiver: :pager: :fax: :minidisc: :vhs: :sound: :speaker: :mute: :loudspeaker: :mega: :hourglass: :hourglass_flowing_sand: :alarm_clock: :watch: :radio: :satellite: :loop: :mag: :mag_right: :unlock: :lock: :lock_with_ink_pen: :closed_lock_with_key: :key: :bulb: :flashlight: :high_brightness: :low_brightness: :electric_plug: :battery: :calling: :email: :mailbox: :postbox: :bath: :bathtub: :shower: :toilet: :wrench: :nut_and_bolt: :hammer: :seat: :moneybag: :yen: :dollar: :pound: :euro: :credit_card: :money_with_wings:</span><br/>↓<br/><span class="green" role="img" aria-label="Trash"><i class="fa fa-trash"></i></span><br/>↓<br/><span role="img" aria-label="Godzilla">🐟 🐦 🐕 🐈</span>
</div>

<!--more-->

# Universal Icons & img role


```jinja copy-paste icons https://www.w3schools.com/charsets/ref_utf_symbols.asp w3schools.com
🐟 🐦 🐕 🐈

❤ 👪 👓 ⛄ 🌜 🌍 🌎 🌏 ⭐ ☀ ⛅ ☁ ⚡ ☔ ❄ ⛄

💻 📷 📹 💿 ☎ 📟 📺 📻 🎓 🔊 🔉 🔈 🔇 ⏳ ⌛ ⌚ 🔓 🔒 🔍 💣 💰 💳

✉ 📥 📤 📫 📪 📬 📭 📦 📊 📈 📉 📋 ✂ ✒ ✏ 📚

🎧 🎬 🎮 🃏 🀄 ⚽ ⚾ ⛳ 🏆 🏂 🏊 🏄 🍸

🎭 🏠 ⛪ ⛺ 🏭 ⛲ ⛵ ⚓ ✈ 🚇 🚍 🚘 🚔 🚑 🚲 ⚠ ⛽ ♨

➡ ⬅ ⬆ ⬇ ↗ ↖ ↘ ↙ ↔ ↕ ◀ ▶ ↩ ↪ ℹ ⏪ ⏩ ⤵ ⤴

♻ 🚹 🚺 🚼 🅿 ♿ 🚭 Ⓜ ㊙ ㊗ ⛔ ✳ ❇ ✴ 🅰 🅱 🆎 🅾

⭕ 💹 © ® ™ ‼ ⁉ ❗ ❓ 🔃 ✖ ♠ ♥ ♣ ♦ ✔ ☑ 〰 〽 ◼ ◻ ◾ ◽ ▪ ▫ ⚫ ⚪ ⬜ ⬛

🕛 🕧 🕐 🕜 🕑 🕝 🕒 🕞 🕓 🕟 🕔 🕠 🕕 🕖 🕗 🕘 🕙 🕚 🕡 🕢 🕣 🕤 🕥 🕦
```

```jinja aria-label & img role https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/Role_Img developer.mozilla.org

# Za opis grupe slika na stranici
<div role="img" aria-label="Opis slike">
  <img src="slika1.png" alt="">
  <img src="slika2.png">
</div>

# Za opis grupe simbola
<div role="img" aria-label="hahahaha Macka">
  <p>
    &#x1F408; &#x1F602;
  </p>
</div>

# Za opis embed svg-a
<svg role="img" aria-label="monkeyshub logo">
<path d="..."/>
</svg>
```

# HTML Entities & Symbols

| entity | entity naziv | entity broj |
| :---   |         ---: |        ---: |
| _      | `&nbsp;`     | `&#160;`    |
| &      | `&amp;`      | `&#38;`     |
| ©      | `&copy;`     | `&#169;`    |
| ®      | `&reg;`      | `&#174;`    |
| ←      | `&larr;`     | `&#8592;`   |
| ↑      | `&uarr;`     | `&#8593;`   |
| →      | `&rarr;`     | `&#8594;`   |
| ↓      | `&darr;`     | `&#8595;`   |
| ♠      | `&spades;`   | `&#9824;`   |
| ♣      | `&clubs;`    | `&#9827;`   |
| <span style="color:#cc6c6c;">♥</span>      | `&hearts;`   | `&#9829;`   |
| ♦      | `&diams;`    | `&#9830;`   |
| &#129421; | `&#129421;`  | `&#1F98D;`  |
| <span style="color:#cccc6c;"> &#128053;</span> | `&#128053;`  | `&#1F435;`  |

[lista karaktera & simbola](https://www.w3schools.com/charsets/ref_html_symbols.asp), [lista misc simbola](https://www.w3schools.com/charsets/ref_utf_symbols.asp), [lista emoji ikona](https://www.w3schools.com/charsets/ref_emoji.asp), [lista g. tijela](https://www.w3schools.com/charsets/ref_utf_geometric.asp) &#8674;

```jinja simbol & stil
# simbol █ + style = paleta boja, primjer:
<p><span style="color:#cc6c6c;border:2px solid #999"> &#9608;</span> - Peachy Monkey</p>
```

| <span style="color:#cc6c6c;border:2px solid #999"> &#9608;</span> - Peachy Monkey</p> | <p><span style="color:#d46464;border:2px solid #999"> &#9608;</span> - Hi Peachy Monkey</p> | <p><span style="color:#3a3636;border:2px solid #999"> &#9608;</span> - Lo Peachy Monkey</p> |
| :--- | :--- | :--- |

# hexo.js tags

```jinja citati - block quotes
{% blockquote Monkey https://monkeyshub.space Book of Space Banana %}
Majmun je Banana
{% endblockquote %}
# centrirana:
{% cq %}Majmun je Banana{% endcq %}
```
```jinja video direktni link
{% video https://monkeyshub.space/covers/fering2009_zoom.webm %}
```
```jinja youtube
{% video Y6PIvoJqNrw %}
```
```jinja Button
<div class="centar">
{% btn https://github.com, GitHub, fab fa-github fa-fw fa-lg, GitHub %}
</div>
```
```jinja caniuse https://caniuse.com/ caniuse.com
{% caniuse feature @ [periods] %}
```
```jinja image
{% img centar /images/slika1.png [width] [height] '"slika""slika"' %}
```
```jinja group picture - gallery
{% grouppicture 2-2 %}{% endgrouppicture %}
<!-- Tag Alias -->
{% gp 9-7 %}{% endgp %}
```
{% grouppicture 2-2 %}
![](/images/group-picture-1.png)
![](/images/group-picture-2.png)
{% endgrouppicture %}

```jinja labels
{% label [class]@text %}
# [class] = default | primary | success | info | warning | danger
```
```jinja notes
{% note [class] [no-icon] [summary] %}
Bilo šta (inline tags također).
{% endnote %}
```
{% note default no-icon Skraćeni sadržaj - klik za detaljno %}
### Detaljno
Natuknica sa pod detaljno: `note default no-icon Skraćeni sadržaj - klik za detaljno`
{% endnote %}

```jinja code block https://hexo.io/docs/tag-plugins#Code-Block hexo.io
{% codeblock [title] [lang:language] [url] [link text] [additional options] %}
code snippet
{% endcodeblock %}
{% codeblock code block lang:jinja https://hexo.io/docs/tag-plugins#Code-Block hexo.io %}
# line_number:false mark: 5 warp:true highlight:true first_line:1
{%endcodeblock%}
```
```jinja pull quote
{% pullquote [class] %}
content
{% endpullquote %}
```
```jinja iframe
{% iframe url [width] [height] %}
```
```jinja link & link 2 post
{% link text url [external] [title] %}

{% post_path filename %}
{% post_link filename [title] [escape] %}
```
```jinja include code
{% include_code [title] [lang:language] [from:line] [to:line] path/to/file %}
```
```jinja tabs
{% tabs Unique name, [index] %}
<!-- tab [Tab caption] [@icon] -->
Any content (support inline tags too).
<!-- endtab -->
{% endtabs %}
```
```jinja fontawesome
yarn add hexo-tag-fontawesome

{% fa [icon-name] [options…] %}
  {% fa refresh spin %}
  {% fa home fw %}
```
{% codeblock test wrap lang:jinja https://hexo.io/docs/tag-plugins#Examples-1 hexo.io > examples wrap:true %}
ffmpeg -i <input_video> \
    -acodec  copy       \
    -vcodec  ffv1       \
    -level   1          \
    -coder   1          \
    -context 1          \
    -g       1          \
    <output_video>
###    
codeblock test wrap lang:jinja https://hexo.io/docs/tag-plugins#Examples-1 hexo.io > examples wrap:false mark:1,8 line_number:true
{% endcodeblock %}
