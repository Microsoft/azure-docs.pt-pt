---
title: Dados distribuídos na base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização)
description: As tabelas e partições horizontais distribuídas no grupo de servidor.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077339"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Dados distribuídos na base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização)

Este artigo descreve os tipos de tabela de três em Hiperescala (Citus).
Ele mostra tabelas distribuídas como são armazenados como partições horizontais e a forma como as partições horizontais são colocadas em nós.

## <a name="table-types"></a>Tipos de tabela

Existem três tipos de tabelas num grupo de servidores de Hiperescala, cada um utilizado para diferentes fins.

### <a name="type-1-distributed-tables"></a>Tipo 1: tabelas distribuídas

O primeiro tipo e o mais comum, é *distribuída* tabelas. Eles parecem ser das tabelas normais para instruções SQL, mas são horizontalmente *particionada* em nós de trabalho. Isso significa que as linhas da tabela são armazenadas em nós diferentes, nas tabelas de fragmento, chamadas *partições horizontais*.

SQL não só é executado de Hiperescala mas instruções DDL ao longo de um cluster, por isso, alterar o esquema de uma tabela distribuída cascades para atualizar as partições horizontais da tabela em funções de trabalho.

#### <a name="distribution-column"></a>Coluna de distribuição

Hiperescala utiliza fragmentação algorítmica para atribuir linhas para as partições horizontais. A atribuição é feita determinística com base no valor de uma coluna de tabela chamada a *coluna de distribuição.* O administrador de cluster tem de designar nesta coluna ao distribuir uma tabela.
É importante para o desempenho e a funcionalidade a fazer a escolha certa.

### <a name="type-2-reference-tables"></a>Tipo 2: tabelas de referência

Uma tabela de referência é um tipo de tabela distribuída cujo conteúdo todo está concentrado numa única partição horizontal. A partição horizontal é replicada em cada função de trabalho, para que consultas em qualquer trabalho podem acessar as informações de referência localmente, sem a sobrecarga de rede de pedir linhas de outro nó. Tabelas de referência não tem nenhuma coluna de distribuição porque não é necessário para distinguir as partições horizontais separadas por linha.

As tabelas de referência são geralmente pequenas e são utilizadas para armazenar dados que são relevantes para as consultas em execução em qualquer nó de trabalho. Por exemplo, enumerado valores, como os Estados de ordem ou categorias de produtos.

### <a name="type-3-local-tables"></a>Tipo de 3: tabelas de locais

Quando usa a Hiperescala, o nó coordenador que se liga a é uma base de dados do PostgreSQL regular. Pode criar tabelas comuns sobre o coordenador e optar por não partição horizontal-los.

Um bom candidato para tabelas locais seriam pequenas tabelas administrativas que não participam de consultas de junção. Por exemplo, uma tabela de utilizadores para autenticação e início de sessão do aplicativo.

## <a name="shards"></a>Partições horizontais

A secção anterior tabelas distribuídas como descritas são armazenadas como partições horizontais em nós de trabalho. Esta secção é mais os detalhes técnicos.

O `pg_dist_shard` tabela de metadados sobre o coordenador contém uma linha para cada partição horizontal de cada tabela distribuída no sistema. A linha corresponde a um ID de partição horizontal com um intervalo de números inteiros num espaço de hash (shardminvalue, shardmaxvalue):

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Se o nó coordenador quer determinar quais as partições horizontais contém uma linha de `github_events`, ele codifica o valor da coluna de distribuição na linha e verifica quais as partições horizontais\'intervalo s contém o valor de hash. (Os intervalos são definidos para que a imagem da função de hash seja seus union não contíguo.)

### <a name="shard-placements"></a>Posicionamentos de partição horizontal

Suponha que essa partição horizontal 102027 está associado com a linha em questão. A linha será lido ou escrita numa tabela chamada `github_events_102027` em um dos operadores. A função de trabalho? Que é determinada completamente pelas tabelas de metadados e o mapeamento de partição horizontal para o trabalho é conhecido como a partição horizontal *colocação*.

O nó coordenador reescreve consultas em fragmentos que se referem às tabelas específicas, como `github_events_102027`, e executa esses fragmentos em funções de trabalho apropriadas. Eis um exemplo de uma consulta executada em segundo plano para localizar o nó que contém a partição horizontal 102027 de ID.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>Passos Seguintes
- Aprenda a [escolha uma coluna de distribuição](concepts-hyperscale-choose-distribution-column.md) para tabelas distribuídas
