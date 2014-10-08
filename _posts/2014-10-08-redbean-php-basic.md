---
layout: post
title: Redbean ORM PHP - Uso básico
description: "Neste post vou apresentar um pouco do RedBeanPHP, um ORM poderoso para aplicações rápidas."
modified: 2014-08-10
tags: [php, RedBean PHP, ORM]
image:
  feature: abstract-3.jpg
comments: true
share: true
---
O RedBean PHP é um ORM que tem me consquistado. Não só pela facilidade de se trabalhar com ele, mas por ser poderoso
na construção de aplicativos de forma muito rápida. Nesse post vou mostrar como utilizá-lo. Mais informações acesse
<a href="http://redbeanphp.com">http://redbeanphp.com</a>.

Por enquanto esqueça modelagem de dados. Talvez você use apenas um mapa mental pra mostrar os relacionamentos entre as entidades e saber
como seu projeto está crescrendo.

<div style='width:300px; position:relative; margin: 0 auto;'>
    <iframe src="//giphy.com/embed/D0EjguuQzYr9m" width="250" height="166.5" frameBorder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe>
</div>

Depois que você fizer o download insira no seu projeto:

{% highlight php %}
<?php
    require 'rb.php';

{% endhighlight %}

A única coisa que você vai precisar criar é o banco. Agora faça a configuração do banco.

{% highlight php %}
<?php
    require 'rb.php';

    R::setup('mysql:host=localhost;dbname=mydatabase',
            'user','password'); // MySQL connection

{% endhighlight %}

### CRUD no RedBean PHP

#### Insert
{% highlight php %}
<?php
    require 'rb.php';

    R::setup('mysql:host=localhost;dbname=mydatabase',
            'user','password'); //for both mysql
    // Cria uma nova instancia pra entidade livro
    $book = R::dispense( 'book' );

    // Adicione algumas propriedades
    $book->title = 'Learn to Program';
    $book->rating = 10;

    // Voce pode usar a notacao em forma de array tambem
    $book['price'] = 29.99

    // ..e... salve no banco
    $id = R::store( $book );
{% endhighlight %}


Automaticamente, o RedBean criará a tabela `book`, com os campos no formato que você inseriu. Isso é lindo!

Infelizmente, na versão atual em que escrevo este post (RedBean - 4.1), o RedBean possui a limitação de se trabalhar com `SCHEMAS`.

Por convensão, o nome das tabelas deve seguir a seguintes regras:

{% highlight php %}
<?php
    $page = R::dispense('page'); //Valido
    $page = R::dispense( 'Page' ); //Invalido: uppercase
    $page = R::dispense( 'cms_page' ); //Invalido: _ Obs: O redbean usa underscore para tabelas relacionais
    $page = R::dispense( '@#!' ); // Invalido
{% endhighlight %}

### Update

Para atualizar um registro, é muito simples. Veja:

{% highlight php %}
<?php

    // Se o bean nao existir, uma nova entidade sera retornada com o ID 0.
    $book = R::load( 'book', $id );

    $book->title = 'Learn to fly';
    $book->rating = 'good';
    $book->published = '2015-02-15';

    // Pronto!
    R::store( $book );

{% endhighlight %}

O RedBean também administra bem as alterações dos tipos de campos no banco de dados.

Por exemplo, você inseriu `$book->page = '2'` e no update, você enviou `$book->page = 3`.

O RedBean se encarrega de atualizar o tipo de campo no banco de dados. Isso serve para todos os tipos de dados.

Para salvar datas, você pode usar as seguintes notações:

{% highlight php %}
<?php
    $meeting->registeredAt = R::isoDate(); // Funcao nativa do redbean que gera um registro no tipo datetime
    $meeting->when = '1995-12-05'; //Date
    $photo->created = '1995-12-05 19:00:00'; //Date time
{% endhighlight %}

### Delete

Para excluir um bean também é muito fácil:

{% highlight php %}
<?php
    $book = R::load( 'book', $id );
    R::trash( $book ); //para um bean

    // ou
    // $ids = array(1,2,3,4)
    $books = R::loadAll( 'book', $ids );
    R::trashAll( $books ); //para multiplos beans
    
{% endhighlight %}

Bom, por enquanto é isso. Espero que tenha gostado da flexibilidade desse ORM.

Aproveita e dá uma olhada na documentação e já ir avançando nos estudos.

No próximo post, vou abordar características avançados do RedBean, mas muito comuns como: relacionamentos entre entidades, lazy load, performance, segurança e etc.

Aquele abraço!