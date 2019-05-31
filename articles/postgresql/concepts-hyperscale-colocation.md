---
title: Conceitos de servidor na base de dados do Azure para PostgreSQL
description: Este artigo fornece considerações e diretrizes para configurar e gerir a base de dados do Azure para servidores PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d03cfd49887adf1f6a4650e374d3e13eeca735a4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077474"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Colocalização de tabela na base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização)

Significa que a partilha de localização, armazenando informações relacionadas em conjunto em nós do mesmo. Podem aceder a consultas rápidas quando os dados necessários tudo estão disponíveis sem qualquer tráfego de rede. Colocalizar dados relacionados em nós diferentes permite consultas a serem executadas com eficiência em paralelo em cada nó.

## <a name="data-colocation-for-hash-distributed-tables"></a>Colocalização de dados para tabelas de hash e distribuída

Em grande escala, uma linha é armazenada numa partição horizontal, se o hash do valor da coluna de distribuição está dentro do intervalo de hash a partição horizontal. As partições horizontais com o mesmo intervalo de hash sempre são colocadas no mesmo nó. Linhas com valores de coluna de distribuição igual são sempre no mesmo nó em tabelas.

![Partições horizontais](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Um exemplo prático de partilha de localização

Considere as seguintes tabelas que podem fazer parte de uma análise de web do multi-inquilino SaaS:

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

Agora queremos responder a consultas que podem ser emitidas por um dashboard do lado do cliente, tais como: "Devolver o número de visitas da semana passada para todas as páginas, começando com" / blog "no inquilino seis."

Se os nossos dados estavam numa opção de implementação de servidor único, podemos facilmente expressar nossa consulta usando o conjunto avançado de operações relacionais oferecida pelo SQL:

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

Desde que o [conjunto de trabalho](https://en.wikipedia.org/wiki/Working_set) para esta consulta se encaixa na memória, uma tabela de servidor único é uma solução apropriada. No entanto, vamos considerar as oportunidades de dimensionar o modelo de dados com a opção de implementação de Hiperescala.

### <a name="distributing-tables-by-id"></a>Distribuição de tabelas por ID

Consultas de servidor único iniciar lento conforme cresce o número de inquilinos e os dados armazenados para cada inquilino. O trabalho definir paradas que se ajusta na memória e CPU tornar-se um estrangulamento.

Neste caso, podemos partições horizontais os dados em vários nós usando Hiperescala. A opção de primeira e mais importante que precisamos fazer quando a fragmentação é a coluna de distribuição. Vamos começar com uma opção simples de usar `event_id` para a tabela de eventos e `page_id` para o `page` tabela:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Quando os dados estão dispersos entre diferentes funções de trabalho, não é possível realizar uma junção à medida que faríamos num único nó de PostgreSQL. Em vez disso, será necessário emitir consultas de dois:

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

Depois disso, os resultados de dois passos tem de ser combinados pela aplicação.

Executar as consultas tem de consultar dados em partições horizontais espalhados entre nós.

![consultas ineficientes](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

Neste caso, a distribuição de dados cria substanciais desvantagens:

-   Sobrecarga de consultar a cada partição horizontal, em execução várias consultas
-   Sobrecarga de Q1 número de linhas a devolver ao cliente
-   Q2 se torne grande
-   A necessidade de escrever consultas em várias etapas são necessárias alterações no aplicativo

Uma vez que os dados estão dispersos, as consultas podem ser colocado em paralelo. No entanto, só é útil se a quantidade de trabalho que faz a consulta é consideravelmente maior do que a sobrecarga de consulta muitos fragmentos.

### <a name="distributing-tables-by-tenant"></a>Distribuição de tabelas por inquilino

Em grande escala, linhas com o mesmo valor de coluna de distribuição são garantidas no mesmo nó. Recomeçar, podemos criar nossos tabelas com `tenant_id` como a coluna de distribuição.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Agora Hiperescala pode responder a consulta original de servidor único sem modificação (P1):

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

Graças ao filtro e a associação no tenant_id, Hiperescala sabe que a consulta inteira pode ser respondida usando o conjunto de partições horizontais colocalizados que contêm os dados para esse inquilino específico. Um único nó de PostgreSQL pode responder a consulta numa única etapa.

![consulta melhor](media/concepts-hyperscale-colocation/colocation-better-query.png)

Em alguns casos, as consultas e esquemas de tabela devem ser alteradas para incluir o ID de inquilino nas restrições exclusivas e Junte-se a condições. No entanto, isso normalmente é uma alteração simples.

## <a name="next-steps"></a>Passos Seguintes

- Veja como os dados de inquilino é colocalizados no [tutorial de multi-inquilino](tutorial-design-database-hyperscale-multi-tenant.md)
