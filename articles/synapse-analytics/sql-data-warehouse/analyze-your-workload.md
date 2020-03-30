---
title: Analisar a sua carga de trabalho
description: Técnicas para analisar a priorização da consulta para a sua carga de trabalho no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: a382ef2d93f10e69569ecbbed1399f256a7afbb3
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351210"
---
# <a name="analyze-your-workload-in-azure-synapse-analytics"></a>Analise a sua carga de trabalho no Azure Synapse Analytics

Técnicas para analisar a sua carga de trabalho SQL Analytics em Azure Synapse Analytics.

## <a name="resource-classes"></a>Classes de Recursos

A SQL Analytics fornece classes de recursos para atribuir recursos do sistema a consultas.  Para obter mais informações sobre as classes de recursos, consulte as [classes de recursos & gestão](resource-classes-for-workload-management.md)da carga de trabalho.  As consultas vão esperar se a classe de recursos atribuída a uma consulta precisa de mais recursos do que estão atualmente disponíveis.

## <a name="queued-query-detection-and-other-dmvs"></a>Deteção de consultas na fila e outros DMVs

Você pode `sys.dm_pdw_exec_requests` usar o DMV para identificar consultas que estão esperando em uma fila de concurrency. As consultas à espera de uma ranhura de condivisa têm um estatuto de **suspensa**.

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

As funções de `sys.database_principals`gestão da carga de trabalho podem ser vistas com .

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

A seguinte consulta mostra qual a função a que cada utilizador é atribuído.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

A SQL Analytics tem os seguintes tipos de espera:

* **LocaiSQueriesConcurrencyResourceType**: Consultas que se sentam fora do quadro de slot de moeda slot. Consultas de DMV e funções do sistema, tais como `SELECT @@VERSION` exemplos de consultas locais.
* **UserConcurrencyResourceType**: Consultas que se sentam dentro da estrutura de slot de moeda slot. As consultas contra as tabelas de utilizador final representam exemplos que utilizariam este tipo de recursos.
* **DmsConcurrencyResourceType**: Espera resultante de operações de movimento de dados.
* **BackupConcurrencyResourceType**: Esta espera indica que uma base de dados está a ser apoiada. O valor máximo para este tipo de recurso é 1. Se várias cópias de segurança foram solicitadas ao mesmo tempo, os outros fazem fila. Em geral, recomendamos um tempo mínimo entre instantâneos consecutivos de 10 minutos. 

O `sys.dm_pdw_waits` DMV pode ser usado para ver que recursos um pedido está à espera.

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

O `sys.dm_pdw_resource_waits` DMV mostra a informação de espera para uma determinada consulta. O tempo de espera dos recursos mede o tempo de espera para que os recursos sejam disponibilizados. O tempo de espera do sinal é o tempo que os servidores SQL subjacentes demoram a agendar a consulta para o CPU.

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

Também pode utilizar `sys.dm_pdw_resource_waits` o DMV calcular quantas ranhuras de condivisões foram concedidas.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

O `sys.dm_pdw_wait_stats` DMV pode ser usado para análise de tendências históricas de esperas.

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

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a gestão de utilizadores de bases de dados e segurança, consulte [Secure a database in SQL Analytics](sql-data-warehouse-overview-manage-security.md). Para obter mais informações sobre como as classes de recursos maiores podem melhorar a qualidade do índice de colunas agrupadas, consulte índices de [reconstrução para melhorar a qualidade](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)do segmento.
