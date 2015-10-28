---
layout: post
title: Atualizando PHP para o 5.6 e instalando o oci8 e dbase
description: "Neste post vou mostrar como atualiar o PHP para o 5.6 e instalar o oci8 do Oracle"
modified: 2015-28-10
tags: [oci8, mac os, php 5.6]
image:
  feature: abstract-3.jpg
comments: true
share: true
---
## Atualização do PHP no MAC OS X

A forma mais fácil de atualizar o PHP do MAC OS X é seguir o passo a passo do site <a href="http://php-osx.liip.ch/">http://php-osx.liip.ch/</a>

## Instalando o oci8

Faça o download dos arquivos do Oracle Client para MAC instantclient-basic-macosx-10.1.0.3.zip e instantclient-sdk-macosx-10.1.0.3.zip no link
<a href="http://www.oracle.com/technetwork/topics/macsoft-088229.html">http://www.oracle.com/technetwork/topics/macsoft-088229.html</a>.

Faça também o download do arquivo <a href="https://pecl.php.net/get/oci8-2.0.8.tgz">https://pecl.php.net/get/oci8-2.0.8.tgz</a>.

Descompacte os 3 arquivos no diretório `/opt/oracle/oci/`.

Serão gerados 2 arquivos `instantclient10_1` e `instantclient10_1 2`, além da pasta `oci8-2.0.8`. Copie o conteúdo da pasta `instantclient10_1 2` para a pasta `instantclient10_1`.

Agora no terminal vá até a pasta `/opt/oracle/oci/instantclient10_1/` via terminal:

{% highlight bash %}
$ cd /opt/oracle/oci/instantclient10_1/
$ ln -s libclntsh.so.10.1 libclntsh.so
$ ln -s libocci.so.10.1 libocci.so
$ ln -s libclntsh.dylib.10.1 libclntsh.dylib
$ cd ..
$ cd oci8-2.0.8/oci8-2.0.8/
$ phpize
$ export ORACLE_HOME=/opt/oracle/oci/instantclient10_1
$ ./configure --with-oci8=share,instantclient,/opt/oracle/oci/instantclient10_1
$ make
$ sudo make install # voce devera informar o password ao executar esse comando
$ echo extension=oci8.so >> /usr/local/php5/lib/php.ini # add a linha com a extensao no php.ini
$ sudo apachectl restart # restart o servidor apache
{% endhighlight %}

## Para instalar o dbase... ##

{% highlight bash %}
$ cd /usr/local/php5/bin/
$ sudo ./pecl install dbase
$ sudo apachectl restart # restart o servidor apache
$ php -m # para verificar se a extensao foi instalada
{% endhighlight %}

## Problema típico de instalar extensão via `pecl`

Algumas vezes ao instalar uma extensão via `pecl`, como a que foi usada acima para instalar o `dbase` ou o `oci8`, é informado a mensage:
`No releases available for package...`. Neste caso basta setar o proxy (Lembrando que http://proxy:8080 é o proxy da minha rede. O seu proxy deve ser outra coisa):
{% highlight bash %}
$ pear config-set http_proxy http://proxy:80
{% endhighlight %}

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
