---
title: Colocalização de mesa - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Como armazenar informações relacionadas em conjunto para consultas mais rápidas
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967342"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Colocalização de mesa na Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

Colocation significa armazenar informações relacionadas nos mesmos nódosos. As consultas podem ser rápidas quando todos os dados necessários estão disponíveis sem qualquer tráfego de rede. A colocação de dados relacionados em diferentes nós permite que as consultas sejam executadas de forma eficiente em paralelo em cada nó.

## <a name="data-colocation-for-hash-distributed-tables"></a>Colocalização de dados para tabelas distribuídas por hash

Na Base de Dados Azure para PostgreSQL – Hyperscale (Citus), uma linha é armazenada num fragmento se o haxixe do valor na coluna de distribuição se enquadrar na gama de hash do fragmento. Fragmentos com a mesma gama de hash são sempre colocados no mesmo nó. Fileiras com valores de coluna de distribuição iguais estão sempre no mesmo nó entre mesas.

![Fragmentos](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Um exemplo prático de co-localização

Considere as seguintes tabelas que podem fazer parte de uma análise web multi-inquilino SaaS:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Agora queremos responder a perguntas que podem ser emitidas por um painel de instrumentos virado para o cliente. Um exemplo de consulta é "Devolver o número de visitas na semana passada para todas as páginas começando com '/blog' no inquilino seis."

Se os nossos dados estivessem na opção de implementação do Single-Server, poderíamos facilmente expressar a nossa consulta usando o conjunto rico de operações relacionais oferecidas pela SQL:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Enquanto o conjunto de [trabalho](https://en.wikipedia.org/wiki/Working_set) para esta consulta se encaixar na memória, uma tabela de um servidor único é uma solução adequada. Vamos considerar as oportunidades de escalar o modelo de dados com a opção de implementação de Hiperescala (Citus).

### <a name="distribute-tables-by-id"></a>Distribuir tabelas por ID

As consultas de um servidor começam a abrandar à medida que o número de inquilinos e os dados armazenados para cada inquilino crescem. O conjunto de trabalho deixa de se encaixar na memória e a CPU torna-se um estrangulamento.

Neste caso, podemos fragmentos de dados em muitos nós usando Hyperscale (Citus). A primeira e mais importante escolha que temos de fazer quando decidirmos esfarejá-lo é a coluna de distribuição. Vamos começar com uma escolha ingénua de `event_id` usar `page_id` para `page` a mesa de eventos e para a mesa:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Quando os dados são dispersos por diferentes trabalhadores, não podemos realizar uma adesão como fazíamos num único nó PostgreSQL. Em vez disso, temos de emitir duas perguntas:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Posteriormente, os resultados dos dois passos devem ser combinados pela aplicação.

A execução das consultas deve consultar os dados em fragmentos espalhados por nó.

![Consultas ineficientes](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

Neste caso, a distribuição de dados cria inconvenientes substanciais:

-   Sobrecarga de consulta de cada fragmento e execução de múltiplas consultas.
-   Sobrecarga do Q1 devolvendo muitas filas ao cliente.
-   O Q2 torna-se grande.
-   A necessidade de escrever consultas em várias etapas requer alterações na aplicação.

Os dados são dispersos, para que as consultas possam ser paralelizadas. Só é benéfico se a quantidade de trabalho que a consulta faz é substancialmente maior do que a sobrecarga de consulta de muitos fragmentos.

### <a name="distribute-tables-by-tenant"></a>Distribua mesas por inquilino

Em Hiperescala (Citus), as filas com o mesmo valor de coluna de distribuição são garantidas para estarem no mesmo nó. Começando de novo, podemos `tenant_id` criar as nossas tabelas com a coluna de distribuição.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Agora a Hiperescala (Citus) pode responder à consulta original do servidor único sem modificação (Q1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Devido ao filtro e junte-se a tenant_id, a Hyperscale (Citus) sabe que toda a consulta pode ser respondida utilizando o conjunto de fragmentos colocalizados que contêm os dados para esse inquilino em particular. Um único nó PostgreSQL pode responder à consulta num único passo.

![Melhor consulta](media/concepts-hyperscale-colocation/colocation-better-query.png)

Em alguns casos, as consultas e os esquemas de mesa devem ser alterados para incluir o ID do inquilino em constrangimentos únicos e condições de adesão. Esta mudança é geralmente simples.

## <a name="next-steps"></a>Passos seguintes

- Veja como os dados dos inquilinos são colocalizados no [tutorial multi-inquilino.](tutorial-design-database-hyperscale-multi-tenant.md)
