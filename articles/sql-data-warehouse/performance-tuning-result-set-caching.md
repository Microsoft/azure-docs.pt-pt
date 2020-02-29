---
title: Afinação de desempenho com o ajuste do resultado
description: Visão geral da funcionalidade de cching do conjunto de resultados para sQL Analytics em Azure Synapse Analytics
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 3d204605e68cf8cf33f69d73fb20f3cc08674e44
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200537"
---
# <a name="performance-tuning-with-result-set-caching"></a>Afinação de desempenho com o ajuste do resultado  
Quando o conjunto de resultados está ativado, o SQL Analytics automaticamente caches a consulta resulta na base de dados do utilizador para uso repetitivo.  Isto permite que execuções subsequentes de consulta obtenha resultados diretamente da cache persistida para que a recomputação não seja necessária.   O conjunto de resultados reduz o desempenho da consulta e reduz o uso de recursos computacionais.  Além disso, as consultas utilizando o conjunto de resultados em cache não utilizam faixas de conmoedação e, portanto, não contam com os limites de moeda existentes. Por razões de segurança, os utilizadores só podem aceder aos resultados em cache se tiverem as mesmas permissões de acesso a dados que os utilizadores que criam os resultados em cache.  

## <a name="key-commands"></a>Comandos-chave
[Ligue o resultado ligado/DESLIGADO para uma base de dados do utilizador](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Ligue o resultado ligado/OFF para uma sessão](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Verifique o tamanho do conjunto de resultados em cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Limpe a cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>O que não está em cache  

Uma vez que o conjunto de resultados é ligado para uma base de dados, os resultados são cachepara todas as consultas até que a cache esteja cheia, exceto para estas consultas:
- Consultas utilizando funções não determinísticas, tais como DateTime.Now()
- Consultas utilizando funções definidas pelo utilizador
- Consultas utilizando tabelas com segurança de nível de linha ou nível de coluna ativada
- Consultas de retornar dados com tamanho de linha superior a 64KB

> [!IMPORTANT]
> As operações para criar cache de conjunto de resultados e recuperar dados da cache acontecem no nó de controlo de uma instância SQL Analytics.
> Quando o conjunto de resultados é ligado, as consultas de execução que devolvem grandes resultados definidos (por exemplo, >1 milhão de linhas) podem causar uma alta utilização de CPU no nó de controlo e abrandar a resposta geral da consulta na instância.  Essas consultas são comumente usadas durante a exploração de dados ou operações ETL. Para evitar stressar o nó de controlo e causar problema sancionadis, os utilizadores devem desligar o resultado da OFF, que atira o cache na base de dados antes de executar este tipo de consultas.  

Execute esta consulta pelo tempo tomado pelo resultado definir operações de cache para uma consulta:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Aqui está uma saída de exemplo para uma consulta executada com o conjunto de resultados declorilado desativado.

![Consultas-passos-com-rsc-deficiente](media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Aqui está uma saída de exemplo para uma consulta executada com o corte de resultados ativado.

![Consulta-passos-com-rsc-habilitado](media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Quando os resultados em cache são usados

O conjunto de resultados em cache é reutilizado para uma consulta se todos os seguintes requisitos estiverem todos cumpridos:
- O utilizador que está a fazer a consulta tem acesso a todas as tabelas referenciadas na consulta.
- Há uma correspondência exata entre a nova consulta e a consulta anterior que gerou a cache do conjunto de resultados.
- Não existem alterações de dados ou esquemas nas tabelas onde o conjunto de resultados em cache foi gerado.

Execute este comando para verificar se uma consulta foi executada com um golpe de cache de resultado ou falha. Se houver um impacto na cache, o result_cache_hit voltará 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gerir resultados em cache 

O tamanho máximo da cache do conjunto de resultados é de 1 TB por base de dados.  Os resultados em cache são automaticamente invalidados quando os dados de consulta subjacentes mudam.  

O despejo em cache é gerido pela SQL Analytics automaticamente seguindo este horário: 
- A cada 48 horas se o conjunto de resultados não tiver sido utilizado ou tiver sido invalidado. 
- Quando o conjunto de resultados se aproxima do tamanho máximo.

Os utilizadores podem esvaziar manualmente toda a cache do conjunto de resultados utilizando uma destas opções: 
- Desligue a função de cache do conjunto de resultados para a base de dados 
- Executar DBCC DROPRESULTSETCACHE enquanto está ligado à base de dados

A pausa numa base de dados não esvazia o conjunto de resultados em cache.  

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte a [visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md) 
