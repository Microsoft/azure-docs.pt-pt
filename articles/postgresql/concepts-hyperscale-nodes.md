---
title: Nós – Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Saiba mais sobre os tipos de nós e tabelas num grupo de servidores na Base de Dados Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2019
ms.openlocfilehash: b3eda2c8de8319552f32938f20ff98af0e0a49fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91314832"
---
# <a name="nodes-and-tables-in-azure-database-for-postgresql--hyperscale-citus"></a>Nós e tabelas na Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

## <a name="nodes"></a>Nós

O tipo de hospedagem Hyperscale (Citus) permite que a Base de Dados Azure para servidores PostgreSQL (chamados nós) coordene uns com os outros numa arquitetura "nada partilhada". Os nós de um grupo de servidores possuem coletivamente mais dados e utilizam mais núcleos de CPU do que seria possível num único servidor. A arquitetura também permite que a base de dados se dimensione adicionando mais nós ao grupo de servidores.

### <a name="coordinator-and-workers"></a>Coordenador e trabalhadores

Cada grupo de servidores tem um nó coordenador e vários trabalhadores. As candidaturas enviam as suas consultas ao nó coordenador, que o transmite aos trabalhadores relevantes e acumula os seus resultados. As aplicações não podem ligar-se diretamente aos trabalhadores.

A Hiperescala (Citus) permite ao administrador da base de dados *distribuir* tabelas, armazenando diferentes linhas em diferentes nós de trabalhadores. As tabelas distribuídas são a chave para o desempenho da Hyperscale (Citus). Não distribuir mesas deixa-as inteiramente no nó coordenador e não pode tirar partido do paralelismo entre máquinas.

Para cada consulta em tabelas distribuídas, o coordenador ou o encaminha para um único nó de trabalhador, ou o paralelo em vários dependendo se os dados necessários vivem num único nó ou múltiplo. O coordenador decide o que fazer consultando tabelas de metadados. Estas tabelas acompanham os nomes do DNS e a saúde dos nós dos trabalhadores, e a distribuição de dados através dos nós.

## <a name="table-types"></a>Tipos de tabelas

Existem três tipos de tabelas num grupo de servidores Hyperscale (Citus), cada uma armazenada de forma diferente nos nós e usada para diferentes finalidades.

### <a name="type-1-distributed-tables"></a>Tipo 1: Mesas distribuídas

O primeiro tipo, e mais comum, são as mesas distribuídas. Parecem ser mesas normais para declarações sql, mas são horizontalmente divididas através de nós operários. O que isto significa é que as linhas da mesa são armazenadas em diferentes nós, em mesas de fragmentos chamadas fragmentos.

A hiperescala (Citus) executa não só as declarações de SQL mas DDL em todo um cluster.
Alterar o esquema de uma mesa distribuída cascatas para atualizar todos os fragmentos da mesa entre os trabalhadores.

#### <a name="distribution-column"></a>Coluna de distribuição

A hiperescala (Citus) usa fragmentos algorítmicos para atribuir linhas a fragmentos. A atribuição é feita deterministicamente com base no valor de uma coluna de tabela chamada coluna de distribuição. O administrador de agrupamento deve designar esta coluna ao distribuir uma tabela.
Fazer a escolha certa é importante para o desempenho e funcionalidade.

### <a name="type-2-reference-tables"></a>Tipo 2: Tabelas de referência

Uma tabela de referência é um tipo de tabela distribuída cujo conteúdo inteiro está concentrado num único fragmento. O fragmento é replicado em todos os trabalhadores. As consultas sobre qualquer trabalhador podem aceder à informação de referência localmente, sem a rede aérea de solicitar filas de outro nó. As tabelas de referência não têm coluna de distribuição porque não há necessidade de distinguir fragmentos separados por linha.

As tabelas de referência são tipicamente pequenas e são usadas para armazenar dados relevantes para consultas em qualquer nó de trabalhador. Um exemplo são valores enumerados como o estado da ordem ou categorias de produtos.

### <a name="type-3-local-tables"></a>Tipo 3: Mesas locais

Quando utiliza Hyperscale (Citus), o nó coordenador a que se conecta é uma base de dados postgreSQL regular. Pode criar tabelas comuns no coordenador e optar por não as ensacaê-las.

Um bom candidato para as mesas locais seriam pequenas mesas administrativas que não participam em consultas de junção. Um exemplo é uma tabela de utilizadores para a inscrição e autenticação da aplicação.

## <a name="shards"></a>Fragmentos

A secção anterior descreveu como as tabelas distribuídas são armazenadas como fragmentos nos nós dos trabalhadores. Esta secção discute mais detalhes técnicos.

A `pg_dist_shard` tabela de metadados do coordenador contém uma linha para cada fragmento de cada tabela distribuída no sistema. A linha combina com um ID de fragmento com uma gama de inteiros num espaço de haxixe (shardminvalue, shardmaxvalue).

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

Se o nó coordenador quiser determinar qual o fragmento que detém uma linha `github_events` de, tem o valor da coluna de distribuição na linha. Em seguida, o nó verifica qual \' o intervalo do fragmento contém o valor do hashed. As gamas são definidas de modo a que a imagem da função hash seja a sua união desarticulada.

### <a name="shard-placements"></a>Colocações de fragmentos

Suponha que o fragmento 102027 está associado à linha em questão. A fila é lida ou escrita numa mesa chamada `github_events_102027` num dos trabalhadores. Qual trabalhador? Isso é determinado inteiramente pelas tabelas de metadados. O mapeamento do fragmento ao trabalhador é conhecido como a colocação de fragmentos.

O nó coordenador reescreve as consultas em fragmentos que se referem às tabelas específicas como `github_events_102027` e executa esses fragmentos nos trabalhadores apropriados. Aqui está um exemplo de uma consulta nos bastidores para encontrar o nó segurando o ID 102027.

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

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>Passos seguintes

- [Determine o tipo da sua aplicação](concepts-hyperscale-app-type.md) para se preparar para modelação de dados
