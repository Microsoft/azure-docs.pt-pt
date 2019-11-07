---
title: Analisar a sua carga de trabalho
description: Técnicas para analisar a priorização de consulta para sua carga de trabalho no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 14e53c1ebe63fac0f7c8e29f66ee5aa0cb3b9526
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693126"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Analise sua carga de trabalho no Azure SQL Data Warehouse

Técnicas para analisar sua carga de trabalho no Azure SQL Data Warehouse.

## <a name="resource-classes"></a>Classes de Recursos

SQL Data Warehouse fornece classes de recurso para atribuir recursos do sistema a consultas.  Para obter mais informações sobre classes de recursos, consulte [classes de recursos & gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).  As consultas aguardarão se a classe de recurso atribuída a uma consulta precisar de mais recursos do que o disponível no momento.

## <a name="queued-query-detection-and-other-dmvs"></a>Detecção de consulta em fila e outros DMVs

Você pode usar o `sys.dm_pdw_exec_requests` DMV para identificar consultas que estão aguardando em uma fila de simultaneidade. As consultas que aguardam um slot de simultaneidade têm o status **suspenso**.

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

As funções de gerenciamento de carga de trabalho podem ser exibidas com `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

A consulta a seguir mostra a qual função cada usuário está atribuído.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL Data Warehouse tem os seguintes tipos de espera:

* **LocalQueriesConcurrencyResourceType**: consultas que ficam fora da estrutura de slot de simultaneidade. Consultas DMV e funções do sistema, como `SELECT @@VERSION`, são exemplos de consultas locais.
* **UserConcurrencyResourceType**: consultas que ficam dentro da estrutura do slot de simultaneidade. As consultas em tabelas do usuário final representam exemplos que usariam esse tipo de recurso.
* **DmsConcurrencyResourceType**: esperas resultantes de operações de movimentação de dados.
* **BackupConcurrencyResourceType**: essa espera indica que um banco de dados está sendo submetido a backup. O valor máximo para esse tipo de recurso é 1. Se vários backups tiverem sido solicitados ao mesmo tempo, as outras filas. Em geral, recomendamos um tempo mínimo entre os instantâneos consecutivos de 10 minutos. 

A DMV `sys.dm_pdw_waits` pode ser usada para ver quais recursos uma solicitação está aguardando.

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

A DMV `sys.dm_pdw_resource_waits` mostra as informações de espera de uma determinada consulta. O tempo de espera do recurso mede o tempo aguardando que os recursos sejam fornecidos. O tempo de espera do sinal é o tempo que leva para os SQL Servers subjacentes agendarem a consulta na CPU.

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

Você também pode usar o `sys.dm_pdw_resource_waits` DMV calcula quantos slots de simultaneidade foram concedidos.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

A DMV `sys.dm_pdw_wait_stats` pode ser usada para análise de tendência histórica de esperas.

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

Para obter mais informações sobre como gerenciar usuários e segurança de banco de dados, consulte [proteger um banco de dados no SQL data warehouse](sql-data-warehouse-overview-manage-security.md). Para obter mais informações sobre como as classes de recursos maiores podem melhorar a qualidade do índice columnstore clusterizado, consulte [recriando índices para melhorar a qualidade do segmento](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
