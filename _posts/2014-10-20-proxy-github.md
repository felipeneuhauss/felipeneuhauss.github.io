---
layout: post
title: Proxy - Github
description: "Neste post, vamos ver como alterar as configurações de proxy no github"
modified: 2014-14-10
tags: [proxy, github]
image:
  feature: abstract-3.jpg
comments: true
share: true
---
## Configurando proxy no github

Para configurar proxys no github é bem simples. Isso é útil para aqueles que foram impedidos de utilizá-lo
nas empresas, por limitações do proxy.

Eu mesmo tive que fazê-lo para postar esse post.rs

{% highlight php %}
$ git config --global http.proxy http://proxy:8080
$ git config --global https.proxy https://proxy:8080
{% endhighlight %}

## Para cancelar o proxy
{% highlight php %}
$ git config --global --unset http.proxy
{% endhighlight %}

Espero que esses comandos ajudem.

Aquele abraço!
