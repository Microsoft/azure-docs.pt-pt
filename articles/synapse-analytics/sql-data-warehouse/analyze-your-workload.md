---
title: Analisar a sua carga de trabalho
description: Técnicas para analisar a priorização da consulta para a sua carga de trabalho no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c547263be8c61d75491d1517b58c03b6365ef929
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208404"
---
# <a name="analyze-your-workload-in-azure-synapse-analytics"></a>Analise a sua carga de trabalho no Azure Synapse Analytics

Técnicas para analisar a sua carga de trabalho Synapse SQL em Azure Synapse Analytics. 

## <a name="resource-classes"></a>Classes de Recursos

O Synapse SQL fornece classes de recursos para atribuir recursos do sistema a consultas.  Para obter mais informações sobre as classes de recursos, consulte [as classes de Recursos & gestão da carga de trabalho.](resource-classes-for-workload-management.md)  As consultas aguardarão se a classe de recursos atribuída a uma consulta necessita de mais recursos do que estão atualmente disponíveis.

## <a name="queued-query-detection-and-other-dmvs"></a>Deteção de consultas em fila e outros DMVs

Você pode usar o `sys.dm_pdw_exec_requests` DMV para identificar consultas que estão esperando em uma fila de convícula. As consultas à espera de uma vaga de conuncy têm um estatuto de **suspensão**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

As funções de gestão da carga de trabalho podem ser vistas com `sys.database_principals` .

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

A seguinte consulta mostra a função a que cada utilizador é atribuído.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

O Sinapse SQL tem os seguintes tipos de espera:

* **LocalQueriesConcurrencyResourceType**: Consultas que se sentam fora do quadro de slot de concurrency. Consultas de DMV e funções do sistema, tais como `SELECT @@VERSION` exemplos de consultas locais.
* **UserConcurrencyResourceType**: Consultas que se sentam dentro da estrutura de ranhuras de concuência. As consultas contra as tabelas de utilizadores finais representam exemplos que utilizariam este tipo de recurso.
* **DmsConcurrencyResourceType**: Esperas resultantes de operações de movimento de dados.
* **BackupConcurrencyResourceType**: Esta espera indica que uma base de dados está a ser apoiada. O valor máximo para este tipo de recurso é 1. Se várias cópias de segurança foram solicitadas ao mesmo tempo, os outros fazem fila. Em geral, recomendamos um tempo mínimo entre instantâneos consecutivos de 10 minutos.

O `sys.dm_pdw_waits` DMV pode ser usado para ver quais os recursos que um pedido está à espera.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

O `sys.dm_pdw_resource_waits` DMV mostra a informação de espera para uma determinada consulta. O tempo de espera dos recursos mede o tempo que espera que os recursos sejam fornecidos. O tempo de espera do sinal é o tempo que os servidores SQL subjacentes demoram a agendar a consulta para o CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

Também pode utilizar o `sys.dm_pdw_resource_waits` DMV calcular quantas faixas horárias de concordância foram concedidas.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

O `sys.dm_pdw_wait_stats` DMV pode ser usado para a análise histórica das tendências das esperas.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a gestão de utilizadores de bases de dados e segurança, consulte [Secure a database in Synapse SQL](sql-data-warehouse-overview-manage-security.md). Para obter mais informações sobre como as classes de recursos maiores podem melhorar a qualidade do índice de colunas agrupadas, consulte [índices de reconstrução para melhorar a qualidade do segmento.](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)
