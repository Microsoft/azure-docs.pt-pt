---
title: Dados distribuídos – Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Saiba mais sobre tabelas distribuídas, tabelas de referência, tabelas locais e fragmentos na Base de Dados Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363440"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Dados distribuídos na Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

Este artigo descreve os três tipos de tabelas na Base de Dados Azure para PostgreSQL – Hyperscale (Citus).
Mostra como as mesas distribuídas são armazenadas como fragmentos, e a forma como os fragmentos são colocados em nós.

## <a name="table-types"></a>Tipos de tabelas

Existem três tipos de tabelas num grupo de servidores de hiperescala (Citus), cada uma usada para diferentes fins.

### <a name="type-1-distributed-tables"></a>Tipo 1: Mesas distribuídas

O primeiro tipo, e mais comum, é distribuído por mesas. Parecem ser mesas normais para declarações de SQL, mas são horizontalmente divididas em nós de trabalhadores. O que isto significa é que as filas da mesa são armazenadas em diferentes nós, em mesas de fragmentos chamadas fragmentos.

A hiperescala (Citus) executa não só declarações SQL mas DDL em todo um cluster.
Alterando o esquema de cascatas de mesa distribuídas para atualizar todos os fragmentos da mesa entre os trabalhadores.

#### <a name="distribution-column"></a>Coluna de distribuição

A hiperescala (Citus) usa sharding algorítmico para atribuir linhas a fragmentos. A atribuição é feita determinicamente com base no valor de uma coluna de tabela chamada coluna de distribuição. O administrador do cluster deve designar esta coluna na distribuição de uma tabela.
Fazer a escolha certa é importante para o desempenho e funcionalidade.

### <a name="type-2-reference-tables"></a>Tipo 2: Tabelas de referência

Uma tabela de referência é um tipo de tabela distribuída cujo conteúdo inteiro está concentrado num único fragmento. O fragmento é replicado em todos os trabalhadores. As consultas sobre qualquer trabalhador podem aceder à informação de referência localmente, sem a rede de requisitar as filas de outro nó. As tabelas de referência não têm coluna de distribuição porque não há necessidade de distinguir fragmentos separados por linha.

As tabelas de referência são tipicamente pequenas e são usadas para armazenar dados relevantes para consultas em qualquer nó de trabalhador. Um exemplo são valores enumerados como estados de encomenda ou categorias de produtos.

### <a name="type-3-local-tables"></a>Tipo 3: Mesas locais

Quando utiliza a Hyperscale (Citus), o nó coordenador a que se liga é uma base de dados postgresqL regular. Pode criar mesas comuns sobre o coordenador e optar por não as estoirar.

Um bom candidato para mesas locais seriam pequenas mesas administrativas que não participam em consultas de adesão. Um exemplo é uma tabela de utilizadores para inscrição e autenticação de aplicações.

## <a name="shards"></a>Fragmentos

A secção anterior descrevia como as mesas distribuídas são armazenadas como fragmentos em nódosos operários. Esta secção discute mais detalhes técnicos.

A tabela de metadados `pg_dist_shard` no coordenador contém uma linha para cada fragmento de cada tabela distribuída no sistema. A linha combina com um id de fragmento com uma gama de inteiros num espaço de hash (shardminvalue, shardmaxvalue).

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

Se o nó coordenador quiser determinar qual o fragmento que detém uma fileira de `github_events`, tem o valor da coluna de distribuição na linha. Em seguida, o nó verifica qual o fragmento\'gama contém o valor hashed. As gamas são definidas de modo que a imagem da função hash é a sua união desarticulada.

### <a name="shard-placements"></a>Colocações de fragmentos

Suponha que o fragmento 102027 esteja associado à fila em questão. A fila é lida ou escrita numa mesa chamada `github_events_102027` num dos trabalhadores. Que trabalhador? Isso é determinado inteiramente pelas tabelas de metadados. O mapeamento do fragmento para o trabalhador é conhecido como a colocação do fragmento.

O nó coordenador reescreve consultas em fragmentos que se referem às tabelas específicas como `github_events_102027` e executa esses fragmentos nos trabalhadores apropriados. Aqui está um exemplo de uma consulta nos bastidores para encontrar o nó segurando o shard ID 102027.

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

## <a name="next-steps"></a>Passos seguintes
- Aprenda a [escolher uma coluna](concepts-hyperscale-choose-distribution-column.md) de distribuição para tabelas distribuídas.
