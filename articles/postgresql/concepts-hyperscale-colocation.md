---
title: Colocação de mesa - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Como armazenar informações relacionadas em conjunto para consultas mais rápidas
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 842563319e09a001fd6e85403d8aee6fb14690ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90884425"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Colocação de mesa na Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

Colocação significa armazenar informações relacionadas juntos nos mesmos nós. As consultas podem ser rápidas quando todos os dados necessários estão disponíveis sem qualquer tráfego de rede. A colocação de dados relacionados em diferentes nós permite que as consultas corram eficientemente em paralelo em cada nó.

## <a name="data-colocation-for-hash-distributed-tables"></a>Colocação de dados para tabelas distribuídas por haxixe

Na Base de Dados Azure para PostgreSQL – Hyperscale (Citus), uma linha é armazenada num fragmento se o haxixe do valor na coluna de distribuição se enquadrar na gama de haxixe do fragmento. Fragmentos com o mesmo intervalo de haxixe são sempre colocados no mesmo nó. As linhas com valores de coluna de distribuição iguais estão sempre no mesmo nó entre tabelas.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-shards.png" alt-text="Fragmentos":::

## <a name="a-practical-example-of-colocation"></a>Um exemplo prático de colocação

Considere as seguintes tabelas que podem fazer parte de um saaS de análise web multi-inquilino:

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

Agora queremos responder a consultas que podem ser emitidas por um dashboard virado para o cliente. Um exemplo de consulta é "Devolver o número de visitas na semana passada para todas as páginas começando com '/blog' no inquilino seis."

Se os nossos dados estivessem na opção de implementação Single-Server, poderíamos facilmente expressar a nossa consulta utilizando o rico conjunto de operações relacionais oferecidas pela SQL:

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

Enquanto o [conjunto de trabalho](https://en.wikipedia.org/wiki/Working_set) para esta consulta se encaixar na memória, uma tabela de servidor único é uma solução adequada. Consideremos as oportunidades de escalonar o modelo de dados com a opção de implantação hyperscale (Citus).

### <a name="distribute-tables-by-id"></a>Distribuir tabelas por ID

As consultas de um único servidor começam a abrandar à medida que o número de inquilinos e os dados armazenados para cada inquilino crescem. O conjunto de trabalho deixa de se encaixar na memória e o CPU torna-se um estrangulamento.

Neste caso, podemos fragmentos os dados em muitos nós usando Hyperscale (Citus). A primeira e mais importante escolha que temos de fazer quando decidimos ser fragmentos é a coluna de distribuição. Vamos começar com uma escolha ingénua de usar `event_id` para a mesa de eventos e para a `page_id` `page` mesa:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Quando os dados são dispersos por diferentes trabalhadores, não podemos fazer uma junção como seríamos num único nó PostgreSQL. Em vez disso, precisamos de emitir duas consultas:

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

Executar as consultas deve consultar dados em fragmentos espalhados por nós.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-inefficient-queries.png" alt-text="Consultas ineficientes":::

Neste caso, a distribuição de dados cria inconvenientes substanciais:

-   Sobrecarga de consulta de cada fragmento e execução de múltiplas consultas.
-   Sobrecarga da Q1 devolvendo muitas linhas ao cliente.
-   O Q2 torna-se grande.
-   A necessidade de escrever consultas em várias etapas requer alterações na aplicação.

Os dados são dispersos, para que as consultas possam ser paralelas. Só é benéfico se a quantidade de trabalho que a consulta faz for substancialmente maior do que a sobrecarga de consulta de muitos fragmentos.

### <a name="distribute-tables-by-tenant"></a>Distribuir mesas por inquilino

Em Hyperscale (Citus), as linhas com o mesmo valor da coluna de distribuição são garantidas para estar no mesmo nó. Começando de novo, podemos criar as nossas tabelas `tenant_id` com como a coluna de distribuição.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Agora a Hyperscale (Citus) pode responder à consulta original de um único servidor sem modificação (Q1):

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

Devido ao filtro e a aderir a tenant_id, a Hyperscale (Citus) sabe que toda a consulta pode ser respondida utilizando o conjunto de fragmentos com posições que contêm os dados para esse inquilino em particular. Um único nó PostgreSQL pode responder à consulta num único passo.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-better-query.png" alt-text="Melhor consulta":::

Em alguns casos, as consultas e esquemas de mesa devem ser alterados para incluir o ID do inquilino em constrangimentos únicos e condições de união. Esta mudança é geralmente simples.

## <a name="next-steps"></a>Passos seguintes

- Veja como os dados dos inquilinos são indicados no [tutorial de vários inquilinos.](tutorial-design-database-hyperscale-multi-tenant.md)
