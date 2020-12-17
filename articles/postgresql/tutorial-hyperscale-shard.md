---
title: 'Tutorial: Dados fragmentos sobre nós de trabalhadores - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL'
description: Este tutorial mostra como criar tabelas distribuídas e visualizar a sua distribuição de dados com a Base de Dados Azure para a Hiperescala Pós-SQL (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/16/2020
ms.openlocfilehash: bc93c3643e329879e5118d1cfb61a356442df808
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97622253"
---
# <a name="tutorial-shard-data-on-worker-nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Tutorial: Dados fragmentos sobre nós de trabalhadores na Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

Neste tutorial, você usa a Base de Dados Azure para PostgreSQL - Hiperescala (Citus) para aprender a:

> [!div class="checklist"]
> * Criar fragmentos distribuídos por haxixe
> * Veja onde são colocados fragmentos de mesa
> * Identificar distribuição distorcida
> * Criar constrangimentos em tabelas distribuídas
> * Executar consultas sobre dados distribuídos

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um grupo de servidores hyperscale (Citus) em execução com dois nós de trabalhadores. Se não tiver um grupo de servidor em execução, siga o tutorial do [grupo de servidores criar](tutorial-hyperscale-server-group.md) e, em seguida, volte a este.

## <a name="hash-distributed-data"></a>Dados distribuídos por haxixe

Distribuir linhas de mesa em vários servidores PostgreSQL é uma técnica chave para consultas escaláveis em Hiperescala (Citus). Juntos, vários nós podem conter mais dados do que uma base de dados tradicional, e em muitos casos podem usar CPUs operários em paralelo para executar consultas.

Na secção pré-requisitos, criámos um grupo de servidores Hyperscale (Citus) com dois nós de trabalhadores.

![coordenador e dois trabalhadores](tutorial-hyperscale-shard/nodes.png)

As tabelas de metadados do nó coordenador rastreiam os trabalhadores e distribuídos dados. Podemos verificar os trabalhadores ativos na [mesa pg_dist_node.](reference-hyperscale-metadata.md#worker-node-table)

```sql
select nodeid from pg_dist_node where isactive;
```
```
 nodeid | nodename
--------+-----------
      1 | 10.0.0.21
      2 | 10.0.0.23
```

> [!NOTE]
> Nodenames em Hyperscale (Citus) são endereços IP internos numa rede virtual, e os endereços reais que vê podem diferir.

### <a name="rows-shards-and-placements"></a>Fileiras, fragmentos e colocações

Para utilizar o CPU e os recursos de armazenamento de nós de trabalhadores, temos de distribuir dados de tabelas em todo o grupo de servidores.  Distribuir uma mesa atribui cada linha a um grupo lógico chamado *fragmento.* Vamos criar uma mesa e distribuí-la:

```sql
-- create a table on the coordinator
create table users ( email text primary key, bday date not null );

-- distribute it into shards on workers
select create_distributed_table('users', 'email');
```

A Hiperescala (Citus) atribui cada linha a um fragmento baseado no valor da coluna de *distribuição,* que, no nosso caso, especificámos `email` ser. Cada linha estará exatamente num fragmento, e cada fragmento pode conter várias linhas.

![tabela de utilizadores com linhas apontando para fragmentos](tutorial-hyperscale-shard/table.png)

Por `create_distributed_table()` predefinição, faz 32 fragmentos, como podemos ver contando na tabela de metadados [pg_dist_shard:](reference-hyperscale-metadata.md#shard-table)

```sql
select logicalrelid, count(shardid)
  from pg_dist_shard
 group by logicalrelid;
```
```
 logicalrelid | count
--------------+-------
 users        |    32
```

A Hiperescala (Citus) utiliza a `pg_dist_shard` tabela para atribuir linhas a fragmentos, com base num haxixe do valor na coluna de distribuição. Os detalhes do hashing não são importantes para este tutorial. O que importa é que podemos consultar para ver quais valores mapear para que iDs fragmentos:

```sql
-- Where would a row containing hi@test.com be stored?
-- (The value doesn't have to actually be present in users, the mapping
-- is a mathematical operation consulting pg_dist_shard.)
select get_shard_id_for_distribution_column('users', 'hi@test.com');
```
```
 get_shard_id_for_distribution_column
--------------------------------------
                               102008
```

O mapeamento de linhas para fragmentos é puramente lógico. Os fragmentos devem ser atribuídos a nós de trabalhador específicos para armazenamento, naquilo a que a Hyperscale (Citus) chama *de colocação de fragmentos*.

![fragmentos atribuídos aos trabalhadores](tutorial-hyperscale-shard/shard-placement.png)

Podemos ver as colocações de fragmentos em [pg_dist_placement.](reference-hyperscale-metadata.md#shard-placement-table)
Juntando-o às outras tabelas de metadados que vimos mostra onde cada fragmento vive.

```sql
-- limit the output to the first five placements

select
    shard.logicalrelid as table,
    placement.shardid as shard,
    node.nodename as host
from
    pg_dist_placement placement,
    pg_dist_node node,
    pg_dist_shard shard
where placement.groupid = node.groupid
  and shard.shardid = placement.shardid
order by shard
limit 5;
```
```
 table | shard  |    host
-------+--------+------------
 users | 102008 | 10.0.0.21
 users | 102009 | 10.0.0.23
 users | 102010 | 10.0.0.21
 users | 102011 | 10.0.0.23
 users | 102012 | 10.0.0.21
```

### <a name="data-skew"></a>Distorção de dados

Um grupo de servidores funciona de forma mais eficiente quando coloca dados uniformemente nos nós dos trabalhadores, e quando coloca dados relacionados juntos nos mesmos trabalhadores. Nesta secção vamos focar-nos na primeira parte, na uniformidade da colocação.

Para demonstrar, vamos criar dados de amostra para a nossa `users` tabela:

```sql
-- load sample data
insert into users
select
    md5(random()::text) || '@test.com',
    date_trunc('day', now() - random()*'100 years'::interval)
from generate_series(1, 1000);
```

Para ver tamanhos de fragmentos, podemos executar [funções](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE) de tamanho de mesa nos fragmentos.

```sql
-- sizes of the first five shards
select *
from
    run_command_on_shards('users', $cmd$
      select pg_size_pretty(pg_table_size('%1$s'));
    $cmd$)
order by shardid
limit 5;
```
```
 shardid | success | result
---------+---------+--------
  102008 | t       | 16 kB
  102009 | t       | 16 kB
  102010 | t       | 16 kB
  102011 | t       | 16 kB
  102012 | t       | 16 kB
```

Podemos ver que os fragmentos são de tamanho igual. Já vimos que as colocações são distribuídas uniformemente entre os trabalhadores, pelo que podemos inferir que os nós dos trabalhadores têm um número aproximadamente igual de filas.

As linhas do nosso `users` exemplo distribuídas uniformemente porque as propriedades da coluna de distribuição, `email` .

1. O número de endereços de e-mail foi maior ou igual ao número de fragmentos
2. O número de filas por endereço de e-mail era semelhante (no nosso caso, exatamente uma linha por endereço porque declaramos e-mail uma chave)

Qualquer escolha de mesa e coluna de distribuição onde qualquer uma das propriedades falha acabará com o tamanho de dados desigual nos trabalhadores, ou seja, *distorcer dados.*

### <a name="add-constraints-to-distributed-data"></a>Adicionar restrições aos dados distribuídos

A utilização da Hyperscale (Citus) permite-lhe continuar a usufruir da segurança de uma base de dados relacional, incluindo [restrições de base de dados.](https://www.postgresql.org/docs/current/ddl-constraints.html)
No entanto, há uma limitação. Devido à natureza dos sistemas distribuídos, a Hyperscale (Citus) não cruzará os constrangimentos de singularidade ou a integridade referencial entre nós operários.

Vamos considerar o exemplo da nossa `users` mesa com uma mesa relacionada.

```sql
-- books that users own
create table books (
    owner_email text references users (email),
    isbn text not null,
    title text not null
);

-- distribute it
select create_distributed_table('books', 'owner_email');
```

Para eficiência, distribuímos `books` da mesma forma `users` que: pelo endereço de e-mail do proprietário. A distribuição por valores de coluna semelhantes é chamada [de co-colocação.](concepts-hyperscale-colocation.md)

Não tivemos problemas em distribuir livros com uma chave estrangeira para os utilizadores, porque a chave estava numa coluna de distribuição. No entanto, teríamos dificuldade em fazer `isbn` uma chave:

```sql
-- will not work
alter table books add constraint books_isbn unique (isbn);
```
```
ERROR:  cannot create constraint on "books"
DETAIL: Distributed relations cannot have UNIQUE, EXCLUDE, or
        PRIMARY KEY constraints that do not include the partition column
        (with an equality operator if EXCLUDE).
```

Numa tabela distribuída o melhor que podemos fazer é fazer com que as colunas façam um modulo único na coluna de distribuição:

```sql
-- a weaker constraint is allowed
alter table books add constraint books_isbn unique (owner_email, isbn);
```

A restrição acima apenas torna o isbn único por utilizador. Outra opção é fazer dos livros uma tabela de [referência](howto-hyperscale-modify-distributed-tables.md#reference-tables) em vez de uma tabela distribuída, e criar uma mesa distribuída separada que associa livros com os utilizadores.

## <a name="query-distributed-tables"></a>Tabelas distribuídas de consulta

Nas secções anteriores, vimos como as filas de mesa distribuídas são colocadas em fragmentos nos nós dos trabalhadores. A maior parte do tempo não precisa de saber como ou onde os dados são armazenados num grupo de servidores. A Hyperscale (Citus) tem um executor de consulta distribuído que divide automaticamente as consultas regulares de SQL. Executa-os em paralelo com os nós dos trabalhadores perto dos dados.

Por exemplo, podemos fazer uma consulta para encontrar a idade média dos utilizadores, tratando a tabela distribuída `users` como se fosse uma mesa normal sobre o coordenador.

```sql
select avg(current_date - bday) as avg_days_old from users;
```
```
    avg_days_old
--------------------
 17926.348000000000
```

![consulta vai para fragmentos via coordenador](tutorial-hyperscale-shard/query-fragments.png)

Nos bastidores, o executor de Hiperescala (Citus) cria uma consulta separada para cada fragmento, executa-os nos trabalhadores, e combina o resultado. Pode vê-lo se utilizar o comando PostgreSQL EXPLAIN:

```sql
explain select avg(current_date - bday) from users;
```
```
                                  QUERY PLAN
----------------------------------------------------------------------------------
 Aggregate  (cost=500.00..500.02 rows=1 width=32)
   ->  Custom Scan (Citus Adaptive)  (cost=0.00..0.00 rows=100000 width=16)
     Task Count: 32
     Tasks Shown: One of 32
     ->  Task
       Node: host=10.0.0.21 port=5432 dbname=citus
       ->  Aggregate  (cost=41.75..41.76 rows=1 width=16)
         ->  Seq Scan on users_102040 users  (cost=0.00..22.70 rows=1270 width=4)
```

A saída mostra um exemplo de um plano de execução para um fragmento de *consulta* em execução no fragmento 102040 (a tabela sobre o `users_102040` trabalhador 10.0.0.21). Os outros fragmentos não são mostrados porque são semelhantes. Podemos ver que o nó do trabalhador digitaliza as tabelas de fragmentos e aplica o agregado. O nó coordenador combina agregados para o resultado final.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criámos uma mesa distribuída, e aprendemos sobre os seus fragmentos e colocações. Vimos um desafio de usar a singularidade e os principais constrangimentos de chaves estrangeiras, e finalmente vimos como as consultas distribuídas funcionam a um nível elevado.

* Leia mais sobre tipos de [mesa](concepts-hyperscale-nodes.md) Hyperscale (Citus)
* Obtenha mais dicas sobre [a escolha de uma coluna de distribuição](concepts-hyperscale-choose-distribution-column.md)
* Conheça os benefícios da [colocação](concepts-hyperscale-colocation.md) de mesa
