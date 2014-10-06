---
layout: post
title: SDO_GEOMETRY(wkt CLOB, srid NUMBER DEFAULT NULL) + ORACLE + PHP (oci).
description: "Nesse post, vou mostrar como salvar mais de 999 registros em um campo SDO_GEOMETRY usando o PHP e as funções de OCI."
modified: 2014-05-10
tags: [php, oci, sdo_geometry, coordenadas, oci_new_descriptor, CLOB]
image:
  feature: abstract-3.jpg
comments: true
share: true
---

### Problema:
<div class="info">
Alterar um sistema que criava campos do tipo SDO_GEOMETRY, para que recebessem mais de 100 mil coordenadas.
</div>

A princípio a função em PHP gerava um script em ORACLE semelhante a esse:

<div class="notice">
SDO_GEOMETRY(2003, NULL, NULL,
 SDO_ELEM_INFO_ARRAY(1, 1003, 1),
 SDO_ORDINATE_ARRAY(146,66, 148,66, 148,68, 146,68, 146,66))
</div>


Pra quem não sabe, existem `N` formas de se instanciar um campo do tipo SDO_GEOMETRY. Algumas formas são:


`SDO_GEOMETRY(wkt CLOB, srid NUMBER DEFAULT NULL);`
`SDO_GEOMETRY(wkt VARCHAR2, srid NUMBER DEFAULT NULL);`
`SDO_GEOMETRY(wkb BLOB, srid NUMBER DEFAULT NULL);`
`SDO_GEOMETRY(2003, NULL, NULL,
    SDO_ELEM_INFO_ARRAY(1, 1003, 1),
    SDO_ORDINATE_ARRAY(x,y...,x,y))`

Na última forma de se instanciar apresentada acima, o `SDO_ORDINATE_ARRAY` tem o limite de 999 coordenadas. Logo não poderemos usar esse construtor.
Então, usei o construtor `SDO_GEOMETRY(wkt CLOB, srid NUMBER DEFAULT NULL);`.

O ORACLE tem limitações também na quantidade de caracteres nos parâmetro passados em funções como `INSERT INTO`. Por padrão são 4000 bytes. Para
corrigir isso, é preciso dar um `bind` em variáveis que podem ultrapassar esse limite. No código abaixo, está uma classe que criei, a fim de driblar esses
problemas.

Observe os comentários. Neles dou exemplos de como são os parâmetros que passei para a função `insert($sql, $largeText)`


{% highlight php %}
<?php
/**
 * Classe que salva no SDO_GEOMETRY varias coordenadas em CLOB, usando o bind do PHP
 * User: felipe.neuhauss
 * Date: 01/10/14
 * Time: 11:03
 */

class OracleOciSdoGeometry {
    private $_host; # Servidor
    private $_db; 	# Banco de dados
    private $_user; # Usuario
    private $_pass; # Senha
    private $_conn;

    public function __construct($host="", $db="", $user="", $pass="")
    {

        $config = new Zend_Config_Ini(APPLICATION_PATH.'/configs/application.ini', 'production');

        $this->_host = $host != ''   ? $host : $config->resources->multidb->db1->dbname;
        $this->_user = $user != ''  ? $user : $config->resources->multidb->db1->username;
        $this->_pass = $pass != '' ? $pass : $config->resources->multidb->db1->password;

        $this->_conn = @oci_pconnect($this->_user, $this->_pass, $this->_host);

        if (!$this->_conn) {
            $e = oci_error();
            trigger_error(htmlentities($e['message'], ENT_QUOTES), E_USER_ERROR);
        }
    }

    public function close(){
       @oci_close($this->_conn);
    }


    public function insert($sql, $largeText) {

        try{
            // Valor do $sql passado como parametro. Observe a criacao da variavel coordinates
            /*
             $sql = "DECLARE
                  coordinates CLOB := :clob_var;
                BEGIN
                  INSERT INTO MY_TABLE(id, sdo_util.from_wktgeometry(:clob_var)) VALUES (1, coordinates);
                END;";
            */
            // Valor de $text passado como parametro
            /*
                -- Mais informacoes sobre formatos primitivos em wkt acess http://en.wikipedia.org/wiki/Well-known_text
                $text = " LINESTRING (30 10, 10 30, 40 40, ... ) ";
            */

            $stmt = oci_parse($this->_conn, $sql);

            $objClob = oci_new_descriptor($this->_conn, OCI_D_LOB);

            oci_bind_by_name($stmt, ":clob_var", $objClob, -1, OCI_B_CLOB);
            $objClob->writetemporary($largeText);

            $tst = oci_execute($stmt, OCI_DEFAULT);

            oci_commit($this->_conn);
            oci_free_statement($stmt);
            $objBlob->free();

            if ($tst != null){
                return true;
            } else {
                return false;
            }

        }
        catch(Exception $e){;

            return false;
        }

    }
}

{% endhighlight %}

Outros formatos em wkt você pode ver em <a href='http://en.wikipedia.org/wiki/Well-known_text'> http://en.wikipedia.org/wiki/Well-known_text </a>. Alguns exemplos abaixo:

`POINT (30 10)`, `LINESTRING (30 10, 10 30, 40 40)`, `POLYGON ((30 10, 40 40, 20 40, 10 20, 30 10))`,
`POLYGON ((35 10, 45 45, 15 40, 10 20, 35 10),(20 30, 35 35, 30 20, 20 30))`

Bom pessoa, qualquer problema, só comentar.