---
title: Tabelas de sistemas - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Metadados para execução de consulta distribuída
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 74403365fe48584fa5d1db0e349c9dfc3772d874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652861"
---
# <a name="system-tables-and-views"></a>Tabelas e vistas do sistema

A Hyperscale (Citus) cria e mantém tabelas especiais que contêm informações sobre dados distribuídos no grupo de servidores. O nó coordenador consulta estas tabelas quando planeia como fazer consultas através dos nós dos trabalhadores.

## <a name="coordinator-metadata"></a>Metadados coordenador

A hiperescala (Citus) divide cada mesa distribuída em múltiplos fragmentos lógicos baseados na coluna de distribuição. O coordenador mantém então tabelas de metadados para rastrear estatísticas e informações sobre a saúde e localização destes fragmentos.

Nesta secção, descrevemos cada uma destas tabelas de metadados e o seu esquema.
Pode visualizar e consultar estas tabelas utilizando o SQL depois de iniciar sessão no nó coordenador.

> [!NOTE]
>
> Os grupos de servidores de hiperescala (Citus) que executam versões mais antigas do Citus Engine podem não oferecer todas as tabelas listadas abaixo.

### <a name="partition-table"></a>Mesa de partição

A tabela de partição pg \_ dist \_ armazena metadados sobre quais as tabelas na base de dados distribuídas. Para cada tabela distribuída, também armazena informações sobre o método de distribuição e informações detalhadas sobre a coluna de distribuição.

| Nome         | Tipo     | Description                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclasse | Tabela distribuída à qual esta linha corresponde. Este valor refere a coluna relfilenode na tabela de catálogos de sistemas pg_class.                                                                                                                   |
| partmethod   | char     | O método utilizado para a partilha/distribuição. Os valores desta coluna correspondentes a diferentes métodos de distribuição são apêndice: 'a', haxixe: 'h', tabela de referência: 'n'                                                                          |
| partkey      | texto     | Informações detalhadas sobre a coluna de distribuição, incluindo o número da coluna, o tipo e outras informações relevantes.                                                                                                                                      |
| co-tionid | número inteiro  | Grupo de colocação a que esta mesa pertence. As tabelas do mesmo grupo permitem a junção e rollups distribuídos entre outras otimizações. Este valor refere-se à coluna cosid na tabela pg_dist_colocation.                      |
| repmodel     | char     | O método usado para a replicação de dados. Os valores desta coluna correspondentes a diferentes métodos de replicação são: Replicação baseada em declaração de Citus: 'c', replicação de streaming pós-quadrado: 's', compromisso em duas fases (para tabelas de referência): 't' |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>Mesa de caco

A \_ tabela de fragmentos pg dist \_ armazena metadados sobre fragmentos individuais de uma tabela. Pg_dist_shard tem informações sobre quais os fragmentos de mesa distribuídos pertencentes, e estatísticas sobre a coluna de distribuição de fragmentos.
Para tabelas distribuídas de apêndice, estas estatísticas correspondem a valores min/max da coluna de distribuição. Para as mesas distribuídas com haxixe, são gamas de fichas de haxixe atribuídas a esse fragmento. Estas estatísticas são usadas para podar fragmentos não relacionados durante consultas SELECIONADAs.

| Nome          | Tipo     | Description                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclasse | Tabela distribuída à qual esta linha corresponde. Este valor refere a coluna relfilenode na tabela de catálogos de sistemas pg_class.                                                          |
| shardid       | bigint   | Identificador globalmente único atribuído a este fragmento.                                                                                                                                           |
| shardstorage  | char     | Tipo de armazenamento utilizado para este fragmento. Diferentes tipos de armazenamento são discutidos na tabela abaixo.                                                                                               |
| shardminvalue | texto     | Para tabelas distribuídas de apêndice, valor mínimo da coluna de distribuição neste fragmento (inclusive). Para tabelas distribuídas de haxixe, valor mínimo de hash token atribuído a esse fragmento (inclusivo). |
| shardmaxvalue | texto     | Para tabelas distribuídas de apêndice, valor máximo da coluna de distribuição neste fragmento (inclusive). Para tabelas distribuídas com haxixe, valor máximo de hash token atribuído a esse fragmento (inclusivo). |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>Tipos de armazenamento de fragmentos

A coluna de fragmentos em fragmentos de pg \_ dist \_ indica o tipo de armazenamento utilizado para o fragmento. Uma breve visão geral de diferentes tipos de armazenamento de fragmentos e sua representação está abaixo.

| Tipo de armazenamento | Valor da shardstorage | Description                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| MESA        | 't'                | Indica que o fragmento armazena dados pertencentes a uma tabela distribuída regular.         |
| COLUMNAR     | 'c'                | Indica que o fragmento armazena dados colunar. (Usado por cstore_fdw tabelas distribuídas) |
| EXTERNO      | 'f'                | Indica que o fragmento armazena dados estrangeiros. (Usado por tabelas distribuídas file_fdw)    |

### <a name="shard-placement-table"></a>Tabela de colocação de fragmentos

A \_ tabela de colocação pg dist \_ rastreia a localização de réplicas de fragmentos em nós de trabalhadores. Cada réplica de um fragmento atribuído a um nó específico é chamada de colocação de fragmentos. Esta tabela armazena informações sobre a saúde e localização de cada colocação de fragmentos.

| Nome        | Tipo   | Description                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | bigint | Identificador de fragmentos associado a esta colocação. Este valor refere-se à coluna shardid na tabela de catálogos pg_dist_shard.             |
| estado de caco  | int    | Descreve o estado desta colocação. Diferentes estados de fragmentos são discutidos na secção abaixo.                                         |
| shardlength | bigint | Para tabelas distribuídas de apêndice, o tamanho da colocação de fragmentos no nó do trabalhador em bytes. Para mesas distribuídas de haxixe, zero.            |
| placementid | bigint | Identificador autogerado único para cada colocação individual.                                                                           |
| groupid     | int    | Denota um grupo de um servidor primário e servidores zero ou mais secundários quando o modelo de replicação de streaming é usado. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>Estados de colocação de fragmentos

A hiperescala (Citus) gere a saúde dos fragmentos numa base por colocação. Se uma colocação colocar o sistema num estado inconsistente, Citus automaticamente o assinala como indisponível. O estado de colocação é registado na tabela pg_dist_shard_placement, dentro da coluna do estado de fragmento. Aqui está uma breve visão geral de diferentes estados de colocação de fragmentos:

| Nome do Estado | Valor do estado de shardstate | Description                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| FINALIZADO  | 1                | Os novos fragmentos do Estado são criados. As colocações de fragmentos neste estado são consideradas atualizadas e são usadas no planeamento e execução de consultas.                                                                                                                                                                                                                                                                                 |
| INATIVO   | 3                | As colocações de fragmentos neste estado são consideradas inativas devido a estar fora de sincronização com outras réplicas do mesmo fragmento. O estado pode ocorrer quando um apêndice, modificação (INSERIR, ATUALIZAR, EXCLUIR) ou uma operação DDL falha para esta colocação. O planejador de consultas ignorará as colocações neste estado durante o planeamento e execução. Os utilizadores podem sincronizar os dados nestes fragmentos com uma réplica finalizada como uma atividade de fundo. |
| TO_DELETE  | 4                | Se Citus tentar deixar cair uma colocação de fragmentos em resposta a uma chamada master_apply_delete_command e falhar, a colocação é movida para este estado. Os utilizadores podem então eliminar estes fragmentos como uma atividade de fundo subsequente.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>Mesa de nó de trabalhador

A tabela de nó de nó dist pg \_ contém \_ informações sobre os nós dos trabalhadores no cluster.

| Nome             | Tipo    | Description                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nodeid           | int     | Identificador autogerado para um nó individual.                                                                                                                                          |
| groupid          | int     | O identificador utilizado para denotar um grupo de um servidor primário e servidores zero ou mais secundários, quando o modelo de replicação de streaming é usado. Por defeito, é o mesmo que o nódid.         |
| nodename         | texto    | Nome de anfitrião ou endereço IP do nó de trabalhador pós-SQL.                                                                                                                                     |
| nodeport         | int     | Número da porta no qual o nó do trabalhador PostgreSQL está a ouvir.                                                                                                                              |
| noderack         | texto    | (Opcional) Informação de colocação de cremalheira para o nó do trabalhador.                                                                                                                                 |
| hasmetadata      | boolean | Reservado para uso interno.                                                                                                                                                                 |
| isactivo         | boolean | Se o nó está ativo aceitando colocações de fragmentos.                                                                                                                                     |
| noderole         | texto    | Se o nó é primário ou secundário                                                                                                                                                 |
| nodecluster      | texto    | O nome do cluster que contém este nó                                                                                                                                               |
| shouldhaveshards | boolean | Se falsos, os fragmentos serão retirados do nó (drenado) ao reequilibrá-lo, nem os fragmentos de novas mesas distribuídas serão colocados no nó, a menos que sejam colocados com fragmentos já lá |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>Tabela de objetos distribuídos

A \_ tabela de objetos dist citus.pg \_ contém uma lista de objetos como tipos e funções que foram criados no nó coordenador e propagados aos nós dos trabalhadores. Quando um administrador adiciona novos nós de trabalhador ao cluster, a Hyperscale (Citus) cria automaticamente cópias dos objetos distribuídos nos novos nós (na ordem correta para satisfazer as dependências dos objetos).

| Nome                        | Tipo    | Description                                          |
|-----------------------------|---------|------------------------------------------------------|
| classid                     | oid     | Classe do objeto distribuído                      |
| objid                       | oid     | ID do objeto distribuído                  |
| objsubid                    | número inteiro | ID do objeto do objeto distribuído, por exemplo, attnum |
| tipo                        | texto    | Parte do endereço estável usado durante as atualizações do PG   |
| object_names                | texto[]  | Parte do endereço estável usado durante as atualizações do PG   |
| object_args                 | texto[]  | Parte do endereço estável usado durante as atualizações do PG   |
| distribution_argument_index | número inteiro | Válido apenas para funções/procedimentos distribuídos      |
| co-tionid                | número inteiro | Válido apenas para funções/procedimentos distribuídos      |

\"Os endereços estáveis \" identificam objetos de forma única independentemente de um servidor específico. A Hiperescala (Citus) rastreia objetos durante uma atualização PostgreSQL utilizando endereços estáveis criados com o [pg identificar o objeto como \_ \_ \_ \_ endereço().](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE)

Aqui \' está um exemplo de como adiciona entradas à `create_distributed_function()` `citus.pg_dist_object` tabela:

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>Tabela de grupo de colocação

A tabela de colocação pg \_ dist \_ contém informações sobre quais as mesas que \' devem ser colocadas juntas, ou [colocadas](concepts-hyperscale-colocation.md).
Quando duas tabelas estão no mesmo grupo de colocação, a Hyperscale (Citus) garante que os fragmentos com os mesmos valores de partição serão colocados nos mesmos nós de trabalhadores.
A colocação permite a junção de otimizações, certos rollups distribuídos e suporte de chaves estrangeiras. A colocação de fragmentos é deduzida quando o fragmento conta, os fatores de replicação e os tipos de colunas de partição combinam entre duas tabelas; no entanto, um grupo de colocação personalizado pode ser especificado ao criar uma tabela distribuída, se assim o desejar.

| Nome                   | Tipo | Description                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| co-tionid           | int  | Identificador único para o grupo de colocação esta linha corresponde.          |
| shardcount             | int  | Contagem de fragmentos para todas as tabelas neste grupo de colocação                          |
| replicationfactor      | int  | Fator de replicação para todas as tabelas deste grupo de colocação.                  |
| tipo de distribuição | oid  | O tipo de coluna de distribuição para todas as tabelas deste grupo de cosm de cosão. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Tabela de estratégia do reequilíbrio

Esta tabela define estratégias que [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) podem usar para determinar onde mover fragmentos.

| Nome                           | Tipo    | Description                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | Se rebalance_table_shards deve escolher esta estratégia por defeito. Utilize citus_set_default_rebalance_strategy para atualizar esta coluna             |
| shard_cost_function            | regproc | Identificador para uma função de custo, que deve tomar um shardid como bigint, e devolver a sua noção de um custo, como tipo real                                |
| node_capacity_function         | regproc | Identificador para uma função de capacidade, que deve tomar um nodeid como int, e devolver a sua noção de capacidade de nó como tipo real                          |
| shard_allowed_on_node_function | regproc | Identificador para uma função que deu bigint shardid, e nodeidarg int, retorna boolean para se Citus pode armazenar o fragmento no nó |
| default_threshold              | flutuar4  | Limiar para considerar um nó demasiado cheio ou demasiado vazio, que determina quando o rebalance_table_shards deve tentar mover fragmentos                    |
| minimum_threshold              | flutuar4  | Uma salvaguarda para evitar que o argumento limiar da rebalance_table_shards() seja definido demasiado baixo                                                  |

Uma instalação de hiperescala (Citus) com estas estratégias na tabela:

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

A estratégia `by_shard_count` padrão, atribui a cada fragmento o mesmo custo. O seu efeito é igualar a contagem de fragmentos entre nós. A outra estratégia predefinida, `by_disk_size` atribui um custo a cada fragmento que corresponde ao seu tamanho de disco em bytes mais o dos fragmentos que estão colocados com ele. O tamanho do disco é calculado `pg_total_relation_size` utilizando, por isso inclui índices. Esta estratégia tenta alcançar o mesmo espaço em disco em cada nó. Note o limiar de 0.1-evita movimentos de fragmentos desnecessários causados por diferenças insignificantes no espaço do disco.

#### <a name="creating-custom-rebalancer-strategies"></a>Criação de estratégias de reequilíbrio personalizado

Aqui estão exemplos de funções que podem ser usadas dentro de novas estratégias de reequilíbrio de fragmentos, e registadas no [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) com a função [citus_add_rebalance_strategy.](reference-hyperscale-functions.md#citus_add_rebalance_strategy)

-   Definição de uma exceção da capacidade do nó pelo padrão de nome hospedeiro:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   Reequilíbrio pelo número de consultas que vão para um fragmento, medido pelo [citus_stat_statements:](reference-hyperscale-metadata.md#query-statistics-table)

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   Isolar um fragmento específico (10000) num nó (endereço \' 10.0.0.1): \'

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>Tabela de estatísticas de consulta

A hiperescala (Citus) fornece `citus_stat_statements` estatísticas sobre como as consultas estão a ser executadas, e para quem. \'É análogo a (e pode ser acompanhado) a visualização de [ \_ \_ declarações pg stat](https://www.postgresql.org/docs/current/static/pgstatstatements.html) em PostgreSQL, que rastreia estatísticas sobre a velocidade de consulta.

Esta vista pode traçar consultas aos inquilinos originários de uma aplicação multi-arrendatário, o que ajuda a decidir quando fazer o isolamento do inquilino.

| Nome          | Tipo   | Description                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| queryid       | bigint | identificador (bom para pg_stat_statements junta)                                   |
| userid        | oid    | utilizador que executou a consulta                                                           |
| dbid          | oid    | caso de base de dados de coordenador                                                 |
| consulta         | texto   | cadeia de consulta anonimizada                                                          |
| executor      | texto   | Executor citus utilizado: adaptável, em tempo real, rastreador de tarefas, router ou inserção-select |
| partition_key | texto   | valor da coluna de distribuição em consultas executadas por router, então NU               |
| chama         | bigint | número de vezes que a consulta foi executado                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

Resultados:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

Ressalvas:

-   Os dados estatísticos não são replicados, e \' não sobreviverão a falhas na base de dados ou falhas
-   Rastreia um número limitado de consultas, definidas pelo `pg_stat_statements.max` GUC (padrão 5000)
-   Para truncar a mesa, use a `citus_stat_statements_reset()` função

### <a name="distributed-query-activity"></a>Atividade de Consulta Distribuída

A Hiperescala (Citus) oferece vistas especiais para observar consultas e bloqueios em todo o cluster, incluindo consultas específicas de fragmentos usadas internamente para construir resultados para consultas distribuídas.

-   **citus \_ dist \_ stat \_ activity**: mostra as consultas distribuídas que estão executando em todos os nós. Um superconjunto `pg_stat_activity` de, utilizável onde quer que este último esteja.
-   **\_ \_ \_ citus atividade stat trabalhador**: mostra consultas sobre os trabalhadores, incluindo consultas de fragmentos contra fragmentos individuais.
-   **bloqueio citus \_ \_ espera:** Consultas bloqueadas em todo o cluster.

As duas primeiras vistas incluem todas as colunas de [atividade pg \_ stat \_ ](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) mais o hospedeiro/porto do trabalhador que iniciou a consulta e o anfitrião/porto do nó coordenador do cluster.

Por exemplo, considere contar as linhas numa tabela distribuída:

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

Podemos ver a consulta aparecer `citus_dist_stat_activity` em:

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

Esta consulta requer informações de todos os fragmentos. Algumas das informações estão em `users_table_102038` fragmento, que por acaso são armazenadas em `localhost:9700` . Podemos ver uma consulta que acede ao fragmento olhando para a `citus_worker_stat_activity` vista:

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

O `query` campo mostra que os dados estão a ser copiados do fragmento a serem contados.

> [!NOTE]
> Se uma consulta de router (por exemplo, inquilino único em uma aplicação multi-inquilino, 'SELECT
> * A partir da tabela ONDE tenant_id = X') é executado sem um bloco de transação, em seguida, o nome principal \_ do \_ anfitrião \_ da consulta e as colunas de porta de consulta principal \_ \_ \_ serão NU NA \_ atividade de stat operário \_ citus. \_

Para ver como `citus_lock_waits` funciona, podemos gerar uma situação de bloqueio manualmente. Primeiro, \' vamos preparar uma mesa de teste do coordenador:

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

Em seguida, usando duas sessões no coordenador, podemos executar esta sequência de declarações:

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

A `citus_lock_waits` vista mostra a situação.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

Neste exemplo, as consultas originaram-se no coordenador, mas a vista também pode listar bloqueios entre consultas originárias de trabalhadores (executados com Hyperscale (Citus) MX, por exemplo).

## <a name="next-steps"></a>Passos seguintes

* Saiba como algumas [funções de Hiperescala (Citus)](reference-hyperscale-functions.md) alteram as tabelas do sistema
* Reveja os conceitos de [nó e tabelas](concepts-hyperscale-nodes.md)
