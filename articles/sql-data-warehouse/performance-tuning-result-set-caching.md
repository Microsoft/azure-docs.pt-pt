---
title: Ajuste de desempenho com cache de conjunto de resultados
description: Visão geral do recurso de cache do conjunto de resultados para Azure SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: seo-lt-2019
ms.openlocfilehash: 461320b9c3ed48176fb60fe695704c582edcd552
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692947"
---
# <a name="performance-tuning-with-result-set-caching"></a>Ajuste de desempenho com cache de conjunto de resultados  
Quando o cache do conjunto de resultados estiver habilitado, o Azure SQL Data Warehouse armazenará em cache automaticamente os resultados da consulta no banco de dados do usuário para uso repetitivo.  Isso permite que as execuções de consulta subsequentes obtenham resultados diretamente do cache persistente, de modo que a recomputação não seja necessária.   O cache do conjunto de resultados melhora o desempenho da consulta e reduz o uso de recursos de computação.  Além disso, as consultas que usam resultados em cache definidos não usam nenhum slot de simultaneidade e, portanto, não contam com os limites de simultaneidade existentes. Por segurança, os usuários só poderão acessar os resultados armazenados em cache se tiverem as mesmas permissões de acesso a dados que os usuários que criam os resultados armazenados em cache.  

## <a name="key-commands"></a>Comandos de chave
[Ativar/desativar o cache do conjunto de resultados para um banco de dados de usuário](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Ativar/desativar o cache do conjunto de resultados para uma sessão](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Verificar o tamanho do conjunto de resultados em cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Limpar o cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>O que não está armazenado em cache  

Quando o cache do conjunto de resultados está ativado para um banco de dados, os resultados são armazenados em cache para todas as consultas até que o cache esteja cheio, exceto para essas consultas:
- Consultas que usam funções não determinísticas, como DateTime. Now ()
- Consultas que usam funções definidas pelo usuário
- Consultas que usam tabelas com segurança em nível de linha ou segurança em nível de coluna habilitada
- Consultas retornando dados com tamanho de linha maior que 64 KB

> [!IMPORTANT]
> As operações para criar o cache do conjunto de resultados e recuperar dados do cache acontecem no nó de controle de uma instância de data warehouse. Quando o cache do conjunto de resultados está ativado, a execução de consultas que retornam um conjunto de resultados grande (por exemplo, > 1 milhão de linhas) pode causar alto uso da CPU no nó de controle e reduzir a resposta geral da consulta na instância.  Essas consultas são usadas normalmente durante a exploração de dados ou operações de ETL. Para evitar a sobrecarga do nó de controle e causar problemas de desempenho, os usuários devem desativar o cache do conjunto de resultados no banco de dados antes de executar esses tipos de consultas.  

Execute esta consulta para o tempo gasto pelas operações de cache do conjunto de resultados para uma consulta:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Aqui está um exemplo de saída para uma consulta executada com o cache de conjunto de resultados desabilitado.

![Consulta-etapas-com-RSC-desabilitado](media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Aqui está um exemplo de saída para uma consulta executada com o cache do conjunto de resultados habilitado.

![Consulta-etapas-com-habilitado para RSC](media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Quando os resultados armazenados em cache são usados

O conjunto de resultados em cache será reutilizado para uma consulta se todos os seguintes requisitos forem atendidos:
- O usuário que está executando a consulta tem acesso a todas as tabelas referenciadas na consulta.
- Há uma correspondência exata entre a nova consulta e a consulta anterior que gerou o cache do conjunto de resultados.
- Não há nenhuma alteração de esquema ou de dados nas tabelas em que o conjunto de resultados em cache foi gerado.

Execute este comando para verificar se uma consulta foi executada com um erro de cache de resultado ou perda. Se houver um impacto no cache, o result_cache_hit retornará 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gerenciar resultados em cache 

O tamanho máximo do cache do conjunto de resultados é 1 TB por banco de dados.  Os resultados armazenados em cache são automaticamente invalidados quando os dados de consulta subjacentes são alterados.  

A remoção de cache é gerenciada pelo Azure SQL Data Warehouse automaticamente após este agendamento: 
- A cada 48 horas, se o conjunto de resultados não foi usado ou se foi invalidado. 
- Quando o cache do conjunto de resultados se aproximar do tamanho máximo.

Os usuários podem esvaziar manualmente todo o cache do conjunto de resultados usando uma destas opções: 
- Desativar o recurso de cache do conjunto de resultados para o banco de dados 
- Executar DBCC DROPRESULTSETCACHE enquanto estiver conectado ao banco de dados

Pausar um banco de dados não esvaziará o conjunto de resultados em cache.  

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte [Descrição geral do desenvolvimento no SQL Data Warehouse](sql-data-warehouse-overview-develop.md). 
