---
layout: post
title: Proxy - Github
description: "Neste post, vou mostrar como instalar o plugin dbase no PHP, no Mac OS Yosemite"
modified: 2014-14-10
tags: [proxy, github]
image:
  feature: abstract-3.jpg
comments: true
share: true
---
## Instalação da extensão dbase.so no PHP - Mac OS 10.5 > 10.*

<iframe src="//giphy.com/embed/jXBRQHjXsFVYY" width="480" height="370" frameBorder="0" style="max-width: 100%" class="giphy-embed" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe>

Um tempo atrás apanhei pra instalar a extensão dbase para trabalhar com arquivos `shapes` no PHP.

Depois descobri que é muito fácil instalar essas extensões. Segue o passo a passo.

Faça o download do arquivo <a href="https://pecl.php.net/get/dbase-5.1.0.tgz">https://pecl.php.net/get/dbase-5.1.0.tgz</a>. No meu caso ele foi parar na pasta "Downloads"

Descompacte o arquivo dbase-5.0.1.tgz

Agora no terminal:

{% highlight bash %}
$ cd Downloads/dbase-5.0.1/dbase-5.0.1
$ phpize
$ ./configure
$ make
$ make install
$ echo extension=dbase.so >> /etc/php.ini
$ sudo apachectl restart
$ php -m # Listar as extensões instaladas

{% endhighlight %}

O comando `# php -m` lista as extensões instaladas no PHP. Veja se a extensão `dbase` foi listada.

## Outra forma de ver as extensões instaladas no PHP ##

Você pode ver se a extensão foi instalada de uma forma mais complicada. Basta criar um arquivo `index.php`, colocar o código:

{% highlight php %}
<?php
    phpinfo();
?>
{% endhighlight %}

Rode a "aplicação" com o servidor embutido do php:

{% highlight bash %}
$ php -S localhost:7777
{% endhighlight %}

Depois só executá-lo no browser `http://localhost:7777/index.php`. Ai você pode pesquisar por `extension_dir` para descobrir
o diretório de onde são instaladas as extensões PHP. Se tiver o arquvo `dbase.so` lá, provavelmente está instalado.

Aquele abraço!
