---
#
# By default, content added below the "---" mark will appear in the home page
# between the top bar and the list of recent posts.
# To change the home page layout, edit the _layouts/home.html file.
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
#
layout: default
---
<div id="nomeAnimado" style="font-size: 2em; animation: moverNome 5s infinite;">
  Winicius B. Faquieri
</div>

<style>
  @keyframes moverNome {
    0% { transform: translateX(0); }
    50% { transform: translateX(100px); }
    100% { transform: translateX(0); }
  }
</style>
