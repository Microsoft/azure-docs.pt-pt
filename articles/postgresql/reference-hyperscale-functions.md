---
title: Funções SQL - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Funções na Hiperescala (Citus) SQL API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 16c3a45e0d88a0546772b3fdc855c90f2e450d14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250336"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Funções na Hiperescala (Citus) SQL API

Esta secção contém informações de referência para as funções definidas pelo utilizador fornecidas pela Hyperscale (Citus). Estas funções ajudam a fornecer funcionalidades distribuídas adicionais à Hyperscale (Citus) que não os comandos SQL padrão.

> [!NOTE]
>
> Os grupos de servidores de hiperescala (Citus) que executam versões mais antigas do Citus Engine podem não oferecer todas as funções listadas abaixo.

## <a name="table-and-shard-ddl"></a>Mesa e Shard DDL

### <a name="create_distributed_table"></a>criar \_ tabela distribuída \_

A função de tabela distribuída de criar \_ é usada para definir uma tabela distribuída e criar os seus \_ fragmentos se for uma tabela distribuída por haxixe. Esta função requer um nome de mesa, a coluna de distribuição e um método de distribuição opcional e insere metadados apropriados para marcar a tabela como distribuída. A função falha na distribuição de 'haxixe' se não for especificado nenhum método de distribuição. Se a tabela for distribuída por haxixe, a função também cria fragmentos de trabalhador com base nos valores de configuração do fator de replicação de fragmentos e fragmentos. Se a tabela contiver linhas, são distribuídas automaticamente para os nós dos trabalhadores.

Esta função substitui o uso do mestre \_ criar \_ tabela \_ distribuída() seguido por mestre \_ criar \_ \_ fragmentos de trabalhador().

#### <a name="arguments"></a>Argumentos

nome da ** \_ tabela:** Nome da tabela que precisa de ser distribuída.

** \_ coluna de distribuição:** A coluna sobre a qual a tabela deve ser distribuída.

** \_ tipo de distribuição:** (Opcional) O método segundo o qual a tabela deve ser distribuída. Os valores admissíveis são anexas ou haxixe, com um valor padrão de 'haxixe'.

**O lugar \_ com:** (Opcional) inclui tabela atual no grupo de colocação de outra tabela. Por predefinição, as tabelas são estassedadas quando são distribuídas por colunas do mesmo tipo, têm a mesma contagem de fragmentos e têm o mesmo fator de replicação. Os valores possíveis para `colocate_with` `default` são, `none` para iniciar um novo grupo de colocação, ou o nome de outra tabela para apresentar com essa tabela.  (Ver [colocação de mesa](concepts-hyperscale-colocation.md).)

Tenha em mente que o valor padrão de `colocate_with` colocação implícita. [A colocação](concepts-hyperscale-colocation.md) pode ser uma grande coisa quando as mesas estão relacionadas ou serão unidas.  No entanto, quando duas tabelas não estão relacionadas, mas que por acaso utilizam o mesmo tipo de dados para as suas colunas de distribuição, a sua colocação acidental pode diminuir o desempenho durante [o reequilíbrio do fragmento](howto-hyperscale-scaling.md#rebalance-shards).  Os fragmentos de mesa serão movidos juntos desnecessariamente numa \" cascata.\"

Se uma nova tabela distribuída não estiver relacionada com outras tabelas, o melhor é `colocate_with => 'none'` especificar.

#### <a name="return-value"></a>Devolver Valor

N/D

#### <a name="example"></a>Exemplo

Este exemplo informa a base de dados de que a tabela de eventos do Github \_ deve ser distribuída por haxixe na coluna de repo \_ id.

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>criar \_ tabela de referência \_

A \_ função de tabela de referência de \_ criação é utilizada para definir uma pequena tabela de referência ou dimensão. Esta função toma um nome de mesa, e cria uma mesa distribuída com apenas um fragmento, replicado a cada nó de trabalhador.

#### <a name="arguments"></a>Argumentos

nome da ** \_ tabela:** Nome da pequena dimensão ou mesa de referência que precisa de ser distribuída.

#### <a name="return-value"></a>Devolver Valor

N/D

#### <a name="example"></a>Exemplo

Este exemplo informa a base de dados de que a tabela nacional deve ser definida como uma tabela de referência

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>upgrade \_ para tabela de \_ referência \_

A função de upgrade \_ para \_ tabela de \_ referência() requer uma tabela distribuída existente que tem uma contagem de fragmentos de um, e atualiza-a para ser uma tabela de referência reconhecida. Depois de chamar esta função, a mesa será como se tivesse sido criada com [create_reference_table](#create_reference_table).

#### <a name="arguments"></a>Argumentos

nome da ** \_ tabela:** Nome da tabela distribuída (tendo contagem de fragmentos = 1) que será distribuída como tabela de referência.

#### <a name="return-value"></a>Devolver Valor

N/D

#### <a name="example"></a>Exemplo

Este exemplo informa a base de dados de que a tabela nacional deve ser definida como uma tabela de referência

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>tabelas de marca \_ \_ cotado

A \_ função de tabelas \_ de marca() requer uma tabela distribuída (a fonte) e uma lista de outros (os alvos) e coloca os alvos no mesmo grupo de colocação que a fonte. Se a fonte ainda não estiver num grupo, esta função cria uma, e atribui-lhe a origem e os alvos.

As tabelas de colocação devem ser feitas à mesa através do `colocate_with` parâmetro de [create_distributed_table,](#create_distributed_table)mas `mark_tables_colocated` podem tratar disso mais tarde, se necessário.

#### <a name="arguments"></a>Argumentos

**nome da tabela de \_ \_ fontes:** Nome da tabela distribuída cujo grupo de colocação os alvos serão atribuídos para corresponder.

**nomes \_ de \_ tabelas-alvo:** Conjunto de nomes das tabelas-alvo distribuídas, deve não estar vazio. Estas tabelas distribuídas devem corresponder à tabela de origem em:

> -   método de distribuição
> -   tipo de coluna de distribuição
> -   tipo de replicação
> -   contagem de fragmentos

Se nenhuma das anteriores se aplicar, a Hiperescala (Citus) levantará um erro. Por exemplo, a tentativa de colocação de tabelas `apples` e cujos tipos de `oranges` colunas de distribuição diferem em resultados:

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>Devolver Valor

N/D

#### <a name="example"></a>Exemplo

Este exemplo coloca `products` e no mesmo grupo de `line_items` colocação que `stores` . O exemplo pressupõe que estas tabelas são todas distribuídas numa coluna com o tipo correspondente, provavelmente uma \" identificação de loja.\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>criar \_ \_ função distribuída

Propaga uma função do nó coordenador aos trabalhadores, e marca-a para execução distribuída. Quando uma função distribuída é chamada ao coordenador, a Hyperscale (Citus) utiliza o valor do argumento de \" distribuição para escolher um nó de trabalhador para executar a \" função. A execução da função nos trabalhadores aumenta o paralelismo, e pode aproximar o código dos dados em fragmentos para uma menor latência.

O caminho de pesquisa postgres não é propagado do coordenador para os trabalhadores durante a execução de funções distribuídas, pelo que o código de função distribuído deve qualificar plenamente os nomes dos objetos da base de dados. Também os avisos emitidos pelas funções não serão apresentados ao utilizador.

#### <a name="arguments"></a>Argumentos

nome da ** \_ função:** Nome da função a distribuir. O nome deve incluir os tipos de parâmetros da função em parênteses, porque várias funções podem ter o mesmo nome em PostgreSQL. Por exemplo, `'foo(int)'` é diferente de `'foo(int, text)'` .

**distribuição \_ arg \_ nome:** (Opcional) O nome do argumento para distribuir. Por conveniência (ou se os argumentos de função não tiverem nomes), é permitido um espaço reservado de posição, tais como `'$1'` . Se este parâmetro não for especificado, então a função nomeada `function_name` é apenas criada nos trabalhadores. Se os nós dos trabalhadores forem adicionados no futuro, a função será automaticamente criada lá também.

**Coloque \_ com:** (Opcional) Quando a função distribuída ler ou escrever para uma mesa distribuída (ou, de uma forma mais geral, grupo de colocação), certifique-se de que nomeará essa tabela utilizando o `colocate_with` parâmetro. Em seguida, cada invocação da função funcionará no nó do trabalhador contendo fragmentos relevantes.

#### <a name="return-value"></a>Devolver Valor

N/D

#### <a name="example"></a>Exemplo

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>Metadados / Informações de Configuração

### <a name="master_get_table_metadata"></a>mestre \_ obter \_ \_ metadados de mesa

A \_ função master get \_ \_ metadados de tabela() pode ser usada para devolver metadados relacionados com a distribuição para uma tabela distribuída. Estes metadados incluem o ID da relação, o tipo de armazenamento, o método de distribuição, a coluna de distribuição, a contagem de replicação, o tamanho máximo do fragmento e a política de colocação de fragmentos para a tabela. Atrás das coberturas, esta função consulta tabelas de metadados hiperescalos (Citus) para obter as informações necessárias e concatena-a em um tuple antes de devolvê-la ao utilizador.

#### <a name="arguments"></a>Argumentos

nome da ** \_ tabela:** Nome da tabela distribuída para a qual pretende obter metadados.

#### <a name="return-value"></a>Devolver Valor

Uma tuple contendo as seguintes informações:

** \_ relídeo lógico:** Oid da tabela distribuída. Refere a coluna relfilenode na tabela do \_ catálogo do sistema pg.

** \_ tipo de armazenamento de \_ parte:** Tipo de armazenamento utilizado para a mesa. Pode ser 't' (tabela padrão), 'f' (mesa estrangeira) ou 'c' (tabela colunaar).

** \_ método de parte:** Método de distribuição utilizado para a tabela. Pode ser 'a' (apêndice), ou 'h' (haxixe).

** \_ chave de parte:** Coluna de distribuição para a tabela.

** \_ contagem \_ de réplicas de parte:** Contagem de replicação de fragmentos de corrente.

tamanho máximo da ** \_ \_ parte:** Tamanho máximo de fragmentos em bytes.

** \_ política de colocação de \_ partes:** Política de colocação de fragmentos utilizado para a colocação dos fragmentos da mesa. Pode ser 1 (local-nó-primeiro) ou 2 (rodada-robin).

#### <a name="example"></a>Exemplo

O exemplo abaixo procura e exibe os metadados de tabela para a \_ tabela de eventos github.

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>obter \_ id fragmento \_ para coluna \_ de \_ distribuição \_

A Hiperescala (Citus) atribui cada linha de uma tabela distribuída a um fragmento com base no valor da coluna de distribuição da linha e no método de distribuição da tabela. Na maioria dos casos, o mapeamento preciso é um detalhe de baixo nível que o administrador da base de dados pode ignorar. No entanto, pode ser útil determinar o fragmento de uma linha, seja para tarefas manuais de manutenção da base de dados ou apenas para satisfazer a curiosidade. A `get_shard_id_for_distribution_column` função fornece esta informação para tabelas distribuídas por haxixe e gama, bem como tabelas de referência. Não funciona para a distribuição do apêndice.

#### <a name="arguments"></a>Argumentos

** \_ nome da tabela:** A tabela distribuída.

**valor de \_ distribuição:** O valor da coluna de distribuição.

#### <a name="return-value"></a>Devolver Valor

O fragmento ID Hyperscale (Citus) associa-se ao valor da coluna de distribuição para a tabela dada.

#### <a name="example"></a>Exemplo

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>coluna \_ para nome de \_ coluna \_

Traduz a `partkey` coluna de um nome de coluna `pg_dist_partition` textual. A tradução é útil para determinar a coluna de distribuição de uma tabela distribuída.

Para uma discussão mais detalhada, consulte [a escolha de uma coluna de distribuição.](concepts-hyperscale-choose-distribution-column.md)

#### <a name="arguments"></a>Argumentos

** \_ nome da tabela:** A tabela distribuída.

** \_ texto var \_ coluna:** O valor da `partkey` `pg_dist_partition` tabela.

#### <a name="return-value"></a>Devolver Valor

O nome da `table_name` coluna de distribuição.

#### <a name="example"></a>Exemplo

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Resultado:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>tamanho \_ da relação citus \_

Obtenha o espaço do disco utilizado por todos os fragmentos da tabela distribuída especificada.
O espaço do disco inclui o tamanho do \" garfo principal, \" mas exclui o mapa de visibilidade e o mapa espacial livre para os fragmentos.

#### <a name="arguments"></a>Argumentos

**logicalrelid:** o nome de uma mesa distribuída.

#### <a name="return-value"></a>Devolver Valor

Tamanho em bytes como um bigint.

#### <a name="example"></a>Exemplo

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>tamanho da mesa de coentro \_ \_

Obtenha o espaço do disco utilizado por todos os fragmentos da tabela distribuída especificada, excluindo índices (mas incluindo TORRADA, mapa espacial gratuito e mapa de visibilidade).

#### <a name="arguments"></a>Argumentos

**logicalrelid:** o nome de uma mesa distribuída.

#### <a name="return-value"></a>Devolver Valor

Tamanho em bytes como um bigint.

#### <a name="example"></a>Exemplo

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>tamanho \_ total \_ da relação citus \_

Obtenha o espaço total do disco utilizado por todos os fragmentos da tabela distribuída especificada, incluindo todos os índices e dados DO TOAST.

#### <a name="arguments"></a>Argumentos

**logicalrelid:** o nome de uma mesa distribuída.

#### <a name="return-value"></a>Devolver Valor

Tamanho em bytes como um bigint.

#### <a name="example"></a>Exemplo

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>declarações de stat citus \_ \_ \_ repostas

Remove todas as linhas de [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table).
Esta função funciona independentemente de `pg_stat_statements_reset()` . Para redefinir todas as estatísticas, ligue para ambas as funções.

#### <a name="arguments"></a>Argumentos

N/D

#### <a name="return-value"></a>Devolver Valor

Nenhum

## <a name="server-group-management-and-repair"></a>Gestão e reparação do grupo do servidor

### <a name="master_copy_shard_placement"></a>\_colocação de fragmento de cópia mestre \_ \_

Se uma colocação de fragmentos não for atualizada durante um comando de modificação ou uma operação DDL, então fica marcada como inativa. A \_ função de colocação de fragmentos de cópia principal \_ \_ pode então ser chamada para reparar uma colocação de fragmentos inativos utilizando dados de uma colocação saudável.

Para reparar um fragmento, a função primeiro deixa cair a colocação de fragmentos pouco saudáveis e recria-a utilizando o esquema no coordenador. Uma vez criada a colocação de fragmentos, a função copia os dados da colocação saudável e atualiza os metadados para marcar a nova colocação de fragmentos como saudáveis. Esta função garante que o fragmento será protegido contra eventuais modificações durante a reparação.

#### <a name="arguments"></a>Argumentos

**identificação do \_ fragmento:** ID do fragmento a ser reparado.

**nome do nó de origem: \_ \_ ** nome DNS do nó no qual está presente a colocação saudável do fragmento \" (nó de \" origem).

**porta \_ de nó de \_ origem:** A porta no nó do trabalhador de origem no qual o servidor da base de dados está a ouvir.

** \_ nome do nó-alvo: \_ ** nome DNS do nó no qual está presente a colocação inválida do fragmento \" \" (nó-alvo).

**porta \_ de nó-alvo: \_ ** A porta no nó do trabalhador-alvo no qual o servidor de base de dados está a ouvir.

#### <a name="return-value"></a>Devolver Valor

N/D

#### <a name="example"></a>Exemplo

O exemplo abaixo irá reparar uma colocação inativa de fragmentos de fragmento 12345, que está presente no servidor de base de dados em execução no 'mau \_ anfitrião' na porta 5432. Para repará-lo, utilizará dados de uma colocação de fragmentos saudáveis presentes no servidor em execução em 'bom \_ anfitrião' na porta
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>\_master move \_ \_ shard colocação

Esta função move um fragmento dado (e fragmentos com ele) de um nó para outro. É normalmente usado indiretamente durante o reequilíbrio de fragmentos em vez de ser chamado diretamente por um administrador de base de dados.

Há duas formas de mover os dados: bloquear ou não bloquear. A abordagem de bloqueio significa que durante o movimento todas as modificações ao fragmento são pausadas.
A segunda forma, que evita bloquear o fragmento, depende da replicação lógica de Postgres 10.

Após uma operação de movimento bem sucedida, os fragmentos no nó de origem são apagados. Se o movimento falhar em qualquer ponto, esta função lança um erro e deixa a fonte e os nós de destino inalterados.

#### <a name="arguments"></a>Argumentos

**identificação de \_ fragmento:** ID do fragmento a ser movido.

**nome do nó de origem: \_ \_ ** nome DNS do nó no qual está presente a colocação saudável do fragmento \" (nó de \" origem).

**porta \_ de nó de \_ origem:** A porta no nó do trabalhador de origem no qual o servidor da base de dados está a ouvir.

** \_ nome do nó-alvo: \_ ** nome DNS do nó no qual está presente a colocação inválida do fragmento \" \" (nó-alvo).

**porta \_ de nó-alvo: \_ ** A porta no nó do trabalhador-alvo no qual o servidor de base de dados está a ouvir.

**modo de transferência de \_ \_ fragmentos:** (Opcional) Especifique o método de replicação, quer utilize a replicação lógica postgreSQL ou um comando COPY de trabalhadores cruzados. Os valores possíveis são:

> -   `auto`: Exija a identidade da réplica se for possível a replicação lógica, caso contrário utilize o comportamento do legado (por exemplo, para reparação de fragmentos, PostgreSQL 9.6). Este é o valor predefinido.
> -   `force_logical`: Utilize a replicação lógica mesmo que a tabela não tenha uma identidade de réplica. Qualquer atualização/eliminação simultânea de declarações para a tabela falhará durante a replicação.
> -   `block_writes`: Utilize COPY (escritas de bloqueio) para tabelas sem chave primária ou identidade de réplica.

#### <a name="return-value"></a>Devolver Valor

N/D

#### <a name="example"></a>Exemplo

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>reequilibrar \_ \_ fragmentos de mesa

A função de reequilíbrio \_ da mesa \_ move fragmentos da tabela dada para os fazer distribuídos uniformemente entre os trabalhadores. A função calcula primeiro a lista de movimentos que precisa de fazer para garantir que o grupo de servidor está equilibrado dentro do limiar dado. Em seguida, move as colocações de fragmentos uma a uma do nó de origem para o nó de destino e atualiza os metadados de fragmentos correspondentes para refletir o movimento.

Cada fragmento é atribuído um custo para determinar se os fragmentos são \" distribuídos uniformemente. \" Por defeito, cada fragmento tem o mesmo custo (um valor de 1), pelo que distribuir para igualar o custo entre os trabalhadores é o mesmo que igualar o número de fragmentos em cada um. A estratégia de custos constantes é chamada \" por \_ conta de \_ fragmentos e é a estratégia \" de reequilíbrio padrão.

A estratégia de incumprimento é adequada nestas circunstâncias:

*  Os fragmentos têm aproximadamente o mesmo tamanho.
*  Os fragmentos recebem aproximadamente a mesma quantidade de tráfego
*  Os nós dos trabalhadores são todos do mesmo tamanho/tipo
*  Fragmentos não foram presos a trabalhadores particulares

Se algum destes pressupostos não se mantiver, então o reequilíbrio padrão pode resultar num mau plano. Neste caso, poderá personalizar a estratégia, utilizando o `rebalance_strategy` parâmetro.

É aconselhável [chamá get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) antes de executar os fragmentos de mesa de reequilíbrio, \_ para ver e verificar as \_ ações a serem executadas.

#### <a name="arguments"></a>Argumentos

** \_ nome da tabela:** (Opcional) O nome da tabela cujos fragmentos precisam de ser reequilibridos. Se o NU, reequilibra então todos os grupos de colocação existentes.

**limiar:** (Opcional) Um número de boia entre 0,0 e 1.0 que indica a relação de diferença máxima de utilização do nó da utilização média. Por exemplo, especificar 0.1 fará com que o reequilíbrio de fragmentos tente equilibrar todos os nós para manter o mesmo número de fragmentos ±10%.
Especificamente, o reequilíbrio de fragmentos tentará convergir a utilização de todos os nós dos trabalhadores para a \* utilização média (1 - limiar). \_ \. (1
+ limiar) \* intervalo de \_ utilização médio.

**movimentos de \_ fragmentos \_ máximos:** (Opcional) O número máximo de fragmentos a mover.

**Lista \_ de fragmentos \_ excluídos:** (Opcional) Identificadores de fragmentos que não devem ser movidos durante a operação de reequilíbrio.

**modo de transferência de \_ \_ fragmentos:** (Opcional) Especifique o método de replicação, quer utilize a replicação lógica postgreSQL ou um comando COPY de trabalhadores cruzados. Os valores possíveis são:

> -   `auto`: Exija a identidade da réplica se for possível a replicação lógica, caso contrário utilize o comportamento do legado (por exemplo, para reparação de fragmentos, PostgreSQL 9.6). Este é o valor predefinido.
> -   `force_logical`: Utilize a replicação lógica mesmo que a tabela não tenha uma identidade de réplica. Qualquer atualização/eliminação simultânea de declarações para a tabela falhará durante a replicação.
> -   `block_writes`: Utilize COPY (escritas de bloqueio) para tabelas sem chave primária ou identidade de réplica.

** \_ drenagem:** (Opcional) Quando for verdade, mova os fragmentos dos nós dos trabalhadores que `shouldhaveshards` se tenham definido como falsos em [pg_dist_node;](reference-hyperscale-metadata.md#worker-node-table)não mova outros fragmentos.

**estratégia de reequilíbrio: \_ ** (Opcional) o nome de uma estratégia em [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Se este argumento for omitido, a função escolhe a estratégia de predefinição, conforme indicado na tabela.

#### <a name="return-value"></a>Devolver Valor

N/D

#### <a name="example"></a>Exemplo

O exemplo abaixo tentará reequilibrar os fragmentos da tabela de eventos de Github \_ dentro do limiar padrão.

```postgresql
SELECT rebalance_table_shards('github_events');
```

Este exemplo tentará reequilibrar a tabela de eventos de Github \_ sem mover fragmentos com ID 1 e 2.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>obter \_ plano \_ de \_ reequilíbrios de mesa \_

Desemparem os movimentos de fragmentos planeados [de rebalance_table_shards](#rebalance_table_shards) sem os executar.
Embora seja improvável, obter \_ o plano de reequilíbrios \_ de mesa pode dar um plano \_ \_ ligeiramente diferente do que um reequilíbrio \_ de mesa chamado com os \_ mesmos argumentos fará. Não são executados ao mesmo tempo, pelo que os factos sobre o grupo de \- servidores - por exemplo, o espaço do disco \- - podem diferir entre as chamadas.

#### <a name="arguments"></a>Argumentos

Os mesmos argumentos que os fragmentos de mesa de reequilíbrio: \_ \_ relação, limiar, \_ movimentos de fragmentos \_ máximos, lista de fragmentos excluídos \_ e \_ drenagem \_ apenas. Consulte a documentação dessa função para o significado dos argumentos.

#### <a name="return-value"></a>Devolver Valor

Tuples contendo estas colunas:

-   ** \_ nome de mesa**: A mesa cujos fragmentos se moveriam
-   **shardid**: O fragmento em questão
-   ** \_ tamanho do fragmento**: Tamanho em bytes
-   **nome de fonte**: Nome de anfitrião do nó de origem
-   **fonte:** Porto do nó de origem
-   **nome alvo**: Nome de anfitrião do nó de destino
-   **targetport**: Porto do nó de destino

### <a name="get_rebalance_progress"></a>obter \_ progresso de reequilíbrio \_

Uma vez que um reequilíbrio de fragmentos começa, a `get_rebalance_progress()` função lista o progresso de cada fragmento envolvido. Monitoriza os movimentos planeados e executados `rebalance_table_shards()` por.

#### <a name="arguments"></a>Argumentos

N/D

#### <a name="return-value"></a>Devolver Valor

Tuples contendo estas colunas:

-   **sessionid**: Postgres PID do monitor de reequilíbrio
-   ** \_ nome da mesa**: A mesa cujos fragmentos estão se movendo
-   **shardid**: O fragmento em questão
-   ** \_ tamanho do fragmento**: Tamanho em bytes
-   **nome de fonte**: Nome de anfitrião do nó de origem
-   **fonte:** Porto do nó de origem
-   **nome alvo**: Nome de anfitrião do nó de destino
-   **targetport**: Porto do nó de destino
-   **progresso:** 0 = à espera de ser movido; 1 = movimento; 2 = completo

#### <a name="example"></a>Exemplo

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus \_ adicionar estratégia de \_ reequilíbrio \_

Apeciam uma linha para [pg_dist_rebalance_strategy.](reference-hyperscale-metadata.md?#rebalancer-strategy-table)

#### <a name="arguments"></a>Argumentos

Para mais informações sobre estes argumentos, consulte os valores correspondentes da coluna em `pg_dist_rebalance_strategy` .

**nome:** identificador para a nova estratégia

** \_ \_ função custo de fragmento:** identifica a função utilizada para determinar o \" custo de cada \" fragmento

** \_ função de capacidade do \_ nó:** identifica a função de medir a capacidade do nó

**fragmento \_ permitido \_ na \_ \_ função nódoa:** identifica a função que determina quais os fragmentos que podem ser colocados nos quais os nóns

** \_ limiar de incumprimento:** um limiar de ponto flutuante que afina a precisão do custo acumulado do fragmento deve ser equilibrado entre nós

** \_ limiar mínimo:** (Opcional) uma coluna de salvaguarda que detém o valor mínimo permitido para o argumento limiar dos fragmentos de mesa de reequilíbrio(). \_ \_ O seu valor predefinido é 0

#### <a name="return-value"></a>Devolver Valor

N/D

### <a name="citus_set_default_rebalance_strategy"></a>estratégia \_ de \_ \_ reequilíbrio padrão padrão citus \_

Atualizar a [tabela pg_dist_rebalance_strategy,](reference-hyperscale-metadata.md#rebalancer-strategy-table) alterando a estratégia nomeada pelo seu argumento para ser o padrão escolhido ao reequilibrá-lo.

#### <a name="arguments"></a>Argumentos

**nome:** o nome da estratégia na \_ estratégia de \_ reequilíbrio pg dist \_

#### <a name="return-value"></a>Devolver Valor

N/D

#### <a name="example"></a>Exemplo

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>estatísticas \_ de \_ ligação remota citus \_

A \_ função estatísticas de ligação remota \_ \_ citus mostra o número de ligações ativas a cada nó remoto.

#### <a name="arguments"></a>Argumentos

N/D

#### <a name="example"></a>Exemplo

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>\_nódoa de drenagem mestre \_

A \_ função do nó de drenagem \_ principal() move fragmentos do nó designado para outros nós que `shouldhaveshards` se definiram como verdadeiros em [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table). Ligue para a função antes de remover um nó do grupo do servidor e desligar o servidor físico do nó.

#### <a name="arguments"></a>Argumentos

**nodename:** O nome de anfitrião do nó a ser drenado.

**nodeport:** O número da porta do nó a drenar.

**modo de transferência de \_ \_ fragmentos:** (Opcional) Especifique o método de replicação, quer utilize a replicação lógica postgreSQL ou um comando COPY de trabalhadores cruzados. Os valores possíveis são:

> -   `auto`: Exija a identidade da réplica se for possível a replicação lógica, caso contrário utilize o comportamento do legado (por exemplo, para reparação de fragmentos, PostgreSQL 9.6). Este é o valor predefinido.
> -   `force_logical`: Utilize a replicação lógica mesmo que a tabela não tenha uma identidade de réplica. Qualquer atualização/eliminação simultânea de declarações para a tabela falhará durante a replicação.
> -   `block_writes`: Utilize COPY (escritas de bloqueio) para tabelas sem chave primária ou identidade de réplica.

**estratégia de reequilíbrio: \_ ** (Opcional) o nome de uma estratégia em [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Se este argumento for omitido, a função escolhe a estratégia de predefinição, conforme indicado na tabela.

#### <a name="return-value"></a>Devolver Valor

N/D

#### <a name="example"></a>Exemplo

Aqui estão os passos típicos para remover um único nó (por exemplo '10.0.0.1' numa porta padrão postgreSQL):

1.  Drene o nó.

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  Espere até que o comando termine.

3.  Retire o nó

Ao drenar vários nós, recomenda-se a utilização [rebalance_table_shards.](#rebalance_table_shards) Ao fazê-lo, a Hyperscale (Citus) planeia com antecedência e move fragmentos o número mínimo de vezes.

1.  Corra para cada nó que pretende remover:

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  Drene-os todos de uma vez com [rebalance_table_shards](#rebalance_table_shards)

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  Aguarde até que o reequilíbrio de drenagem termine

4.  Retire os nosmos

### <a name="replicate_table_shards"></a>replicar \_ \_ fragmentos de mesa

A \_ \_ função de fragmentos de mesa replicados() replica os fragmentos sub-replicados da tabela dada. A função calcula primeiro a lista de fragmentos e locais sub-replicados a partir dos quais podem ser recolhidos para replicação. Em seguida, a função copia sobre esses fragmentos e atualiza os metadados fragmentos correspondentes para refletir a cópia.

#### <a name="arguments"></a>Argumentos

** \_ nome da mesa:** O nome da tabela cujos fragmentos precisam de ser replicados.

**fator de replicação de \_ \_ fragmentos:** (Opcional) O fator de replicação desejado para alcançar para cada fragmento.

**cópias \_ de \_ fragmentos max:** (Opcional) Número máximo de fragmentos para copiar para atingir o fator de replicação pretendido.

**Lista \_ de fragmentos \_ excluídos:** (Opcional) Identificadores de fragmentos que não devem ser copiados durante a operação de replicação.

#### <a name="return-value"></a>Devolver Valor

N/D

#### <a name="examples"></a>Exemplos

O exemplo abaixo tentará replicar os fragmentos da tabela de eventos de Github \_ para o fator de replicação de \_ \_ fragmentos.

```postgresql
SELECT replicate_table_shards('github_events');
```

Este exemplo tentará trazer os fragmentos da tabela de eventos de Github \_ para o fator de replicação desejado com um máximo de 10 cópias de fragmentos. O reequilíbrio copiará um máximo de 10 fragmentos na sua tentativa de alcançar o fator de replicação pretendido.

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>isolar \_ inquilino \_ para novo \_ \_ fragmento

Esta função cria um novo fragmento para manter linhas com um único valor específico na coluna de distribuição. É especialmente útil para o multi-inquilino Hyperscale (Citus) usar o caso, onde um grande inquilino pode ser colocado sozinho em seu próprio fragmento e, em última análise, seu próprio nó físico.

#### <a name="arguments"></a>Argumentos

nome da ** \_ mesa:** O nome da mesa para obter um novo fragmento.

**id \_ inquilino:** O valor da coluna de distribuição que será atribuída ao novo fragmento.

** \_ opção cascata:** (Opcional) Quando definido para \" CASCADE, \" também isola um fragmento de todas as mesas do [grupo de colocação](concepts-hyperscale-colocation.md)da tabela atual.

#### <a name="return-value"></a>Devolver Valor

**fragmento \_ id:** A função devolve o ID único atribuído ao fragmento recém-criado.

#### <a name="examples"></a>Exemplos

Crie um novo fragmento para manter os lineitems para o inquilino 135:

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>Passos seguintes

* Muitas das funções neste artigo modificam [tabelas de metadados do](reference-hyperscale-metadata.md) sistema
* [Os parâmetros do servidor](reference-hyperscale-parameters.md) personalizam o comportamento de algumas funções
