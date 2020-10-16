---
title: Otimização do desempenho com a colocação em cache dos resultados
description: Visão geral do conjunto de resultados para a piscina Sinaapse SQL em Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: aeeca38afb82e2dcd86e111d1ae5dcb2e7499f42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362270"
---
# <a name="performance-tuning-with-result-set-caching"></a>Otimização do desempenho com a colocação em cache dos resultados

Quando o caching do resultado está ativado, a consulta de caches Synapse SQL automaticamente resulta na base de dados do utilizador para uma utilização repetitiva.  Isto permite que execuções de consulta subsequentes obtenham resultados diretamente da cache persistiu, pelo que não é necessária recomputação.   O conjunto de resultados melhora o desempenho da consulta e reduz o uso de recursos compute.  Além disso, as consultas que utilizam os resultados em cache não utilizam quaisquer ranhuras de concordância e, portanto, não contam com os limites de concordância existentes. Para a segurança, os utilizadores só podem aceder aos resultados em cache se tiverem as mesmas permissões de acesso aos dados que os utilizadores que criam os resultados em cache.  

## <a name="key-commands"></a>Comandos-chave

[Ligue o conjunto de resultados LIGADO/OFF para uma base de dados de utilizadores](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Ligue o conjunto de resultados ON/OFF para uma sessão](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Verifique o tamanho do conjunto de resultados em cache](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[Limpe a cache](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>O que não está em cache  

Uma vez que o caching do resultado é ligado para uma base de dados, os resultados são em cache para todas as consultas até que a cache esteja cheia, exceto para estas consultas:

- Consultas utilizando funções não determinísticas como DateTime.Now()
- Consultas usando funções definidas pelo utilizador
- Consultas usando tabelas com segurança de nível de linha ou segurança de nível de coluna ativada
- Consultas devolvendo dados com tamanho de linha maior que 64KB
- Consultas devolvendo grandes dados em tamanho (>10GB) 

> [!IMPORTANT]
> As operações para criar cache de conjunto de resultados e obter dados da cache acontecem no nó de controlo de uma placa de piscina Sinapse SQL.
> Quando o caching do resultado é ligado, as consultas de execução que devolvem o grande conjunto de resultados (por exemplo, >1GB) podem causar um elevado estrangulamento no nó de controlo e abrandar a resposta geral da consulta sobre o caso.  Estas consultas são comumente utilizadas durante a exploração de dados ou operações ETL. Para evitar stressar o nó de controlo e causar problemas de desempenho, os utilizadores devem desligar o conjunto de resultados na base de dados antes de executar este tipo de consultas.  

Executar esta consulta pelo tempo tomado pelas operações de caching do resultado para uma consulta:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Aqui está uma saída de exemplo para uma consulta executada com o conjunto de resultados desativado.

![A screenshot mostra resultados de consulta, incluindo o tipo de localização e o comando.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Aqui está uma saída de exemplo para uma consulta executada com caching conjunto de resultados ativado.

![O screenshot mostra resultados de consulta com o comando selecionado * de [D W ResultadoCache D b] dot d b o chamado.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Quando os resultados em cache são usados

O conjunto de resultados em cache é reutilizado para uma consulta se todos os seguintes requisitos forem cumpridos:

- O utilizador que está a executar a consulta tem acesso a todas as tabelas referenciadas na consulta.
- Existe uma correspondência exata entre a nova consulta e a consulta anterior que gerou a cache definida pelo resultado.
- Não existem alterações de dados ou esquemas nas tabelas onde o conjunto de resultados em cache foi gerado.

Execute este comando para verificar se uma consulta foi executada com um resultado atingido ou falha. A coluna result_cache_hit devolve 1 para o cache, 0 para falha de cache, e valores negativos por razões pelas quais o caching do resultado não foi usado. Verifique [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para mais detalhes.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gerir resultados em cache

O tamanho máximo da cache definida pelo resultado é de 1 TB por base de dados.  Os resultados em cache são automaticamente invalidados quando os dados de consulta subjacentes mudam.  

O despejo de cache é gerido pela Synapse SQL seguindo automaticamente este horário:

- A cada 48 horas se o conjunto de resultados não tiver sido utilizado ou tiver sido invalidado.
- Quando a cache definida pelo resultado se aproximar do tamanho máximo.

Os utilizadores podem esvaziar manualmente toda a cache definida pelo resultado utilizando uma destas opções:

- Desligue a função de cache definida por resultados para a base de dados
- Executar DBCC DROPRESULTSETCACHE enquanto estiver ligado à base de dados

Fazer uma pausa numa base de dados não esvazia o conjunto de resultados em cache.  

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)
