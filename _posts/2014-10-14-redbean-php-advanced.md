---
layout: post
title: Redbean ORM PHP - Uso avançado
description: "Nesse post, vou mostrar aspectos avançados do RedBean ORM"
modified: 2014-14-10
tags: [php, RedBean PHP, ORM]
image:
  feature: abstract-3.jpg
comments: true
share: true
---
Dando continuidade ao post anterior, vamos iniciar pelos relacionamentos entre entidades no <a href="http://redbeanphp.com">http://redbeanphp.com</a>.

## Relacioanamentos no RedBeanPHP

### Um-pra-muitos (One-to-many)

Fazer o relacionamento de one-to-many é muito simples:

{% highlight php %}
<?php
    $store   = R::dispense( 'store' );
    $product = R::dispense( 'product' );
    $product->price = 25;
    $store->ownProductList[] = $product
    R::store( $store );

{% endhighlight %}

Note que para se fazer o relacionamento, foi setado o array `ownProductList`. Sua nomeclatura inicia-se com `own` + `Nomedaentidade` + `List`. Esse é o padrão do RedBeanPHP

Outro exemplo:

{% highlight php %}
<?php
    $product   = R::find( 'product', ' title LIKE ? ', array('Leite%')  );
    $image     = R::dispense( 'image' );
    $image->file_name = 25;
    $product->ownImageList[] = $image
    R::store( $product );

{% endhighlight %}

### Muitos-pra-muitos (Many-to-many)

{% highlight php %}
<?php
    list($leite, $queijo) = R::dispense('product', 2);

    $tag = R::dispense( 'tag' );
    $tag->name = 'laticíneos';

    //creates product_tag table!
    $leite->sharedTagList[] = $tag;
    $queijo->sharedTagList[] = $tag;
    R::storeAll( array($leite, $queijo) );

{% endhighlight %}

Ambos os produtos, `leite` e `queijo`, fazem parte do grupo `laticíneos`.

Nesse caso o RedBeanPHP cria uma tabela relacional chamada `product_tag`. Fácil não?

Uma outra forma de se criar um relacionamento `many-to-many` é essa:

{% highlight php %}
<?php
    $participant = R::dispense('participant');
    $participante->project = $project;
    $participant->employee = $felipe;
    $participant->role     = 'developer';
    R::store( $participant );

{% endhighlight %}

Agora, baseado no exemplo acima, se eu quiser pegar "Todos os employees (empregados) via participants (participantes) de um determinado projeto", como eu faço?

{% highlight php %}
<?php

    // Se o bean nao existir, uma nova entidade sera retornada com o ID 0.
    $employees = $project
            ->via( 'participant' )
            ->sharedEmployeeList;

{% endhighlight %}

Note o nome `sharedEmployeeList`. Assim como o `ownProductList`, para se pegar os `beans` compartilhados, faz-se o uso do
prefixo `shared` + `NomeDaEntidade` + `List`.

## Importa beans

O RedbeanPHP oferece vários recursos para carregar beans com dados.

{% highlight php %}
<?php

     $book->import($_POST);

     // ou
     // Carregue o bean apenas com os dados representados na string do 2º parametro
     $book->import($_POST, 'title,subtitle,summary,price');

{% endhighlight %}

## Exportar beans

Fazer o export de beans é muito útil para aplicações REST, ou aquelas, cuja comunicação se dá via ajax e cujos dados
são normalmente estruturados em JSON.

{% highlight php %}
<?php

    // Exporta para array
    $array = $product->export();

    // Outra forma para um array de beans
    $arrays = R::exportAll( $products );

    // Exporta os beans para array inclusive as entidades pais e dependentes de forma recursiva.
    $arrays = R::exportAll( $products, TRUE );

{% endhighlight %}

O segundo parâmetro de `R::exportAll($beans, $parent = true)`, sendo 'true', traz inclusive, de forma recursiva, as entidades `pais`
das entidades `pais`, e as  `pais` das entidades dependentes.

Nem sempre você quer trazer todas as informações relacionadas ao `bean`, pois muitas vezes,
em beans com alta dependência implica diretamente na queda da performance, uma vez que o RedBeanPHP
vai navegar por todas as entidades.

Nesse caso, use o terceiro parâmetro dessa função para fazer um filtro dos relacionamentos que você deseja trazer. Exemplo:

{% highlight php %}
<?php

    // Traga os produtos e apenas a categoria (category) do produto. Nao serao trazidos as imagens dos produtos
    $array = R::exportAll($products, true, array('category'));
{% endhighlight %}

Você pode também limitar quais os `beans` das entidades pais você quer filtrar.

{% highlight php %}
<?php
    /*
        Nesse caso, para cada entidade $user, alem de trazer os beans da entidade
        relacional role_user com os registros [user_id, role_id], pega-se apenas a entidade
        role com os registros [id, name, registeredAt].
     */
    return R::exportAll($users, true, array('role_user' => 'role'));
{% endhighlight %}

## Produção

Você não vai querer que as informações inputadas por seus usuários, altere as características do seu banco.
Você pode fazer o uso da função `R::freeze( TRUE );`. Ela irá congelar o seu banco, impedindo alterações
nas características de seus campos e tabelas.

## Debug

Você pode debugar o SQL gerado pelo RedBeanPHP. Basta colocar a função `R::debug( TRUE );` em qualquer trecho do seu código
para que você veja as `querys` que estão sendo executadas. Você verá algo semelhante ao trecho abaixo:

<div class='notice'>
INSERT INTO "event" ( id, "name" ) VALUES
( NULL, ? )
Array

(
[0] => party
)
</div>

## Considerações finais

Outras funcionalidades como `count`, `snippets`, `joins` dentre outras, não foram abordados nesse post.
Mas você pode aprender de forma fácil no próprio site e documentação da RedBean PHP. Mais pra frente
devo postar outra coisas interessantes do RedBean.

Aquele abraço!
