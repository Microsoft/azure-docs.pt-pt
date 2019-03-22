---
title: Analisar a sua carga de trabalho - Azure SQL Data Warehouse | Documentos da Microsoft
description: Técnicas de análise de priorização de consulta para a sua carga de trabalho no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 434cbb18a109308844dbc7ff219d40948678e86e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310732"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Analisar a sua carga de trabalho no Azure SQL Data Warehouse

Técnicas para analisar a sua carga de trabalho no Azure SQL Data Warehouse.

## <a name="resource-classes"></a>Classes de Recursos

O SQL Data Warehouse fornece classes de recursos para atribuir recursos de sistema para consultas.  Para obter mais informações sobre classes de recursos, consulte [classes e carga de trabalho de gestão de recursos](resource-classes-for-workload-management.md).  Consultas aguardará se a classe de recursos atribuída a uma consulta precisa de mais recursos do que estão atualmente disponíveis.

## <a name="queued-query-detection-and-other-dmvs"></a>Deteção de consulta em fila e outros DMVs

Pode utilizar o `sys.dm_pdw_exec_requests` DMV para identificar consultas que estão a aguardar na fila de simultaneidade. Consultas a aguardar que um bloco de simultaneidade têm o estado **suspenso**.

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

Funções de gestão da carga de trabalho podem ser vistas com `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

A seguinte consulta mostra qual é atribuída a cada usuário para a função.

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

* **LocalQueriesConcurrencyResourceType**: Consultas que ficam fora do framework da ranhura de simultaneidade. Consultas DMV e o sistema funciona como `SELECT @@VERSION` são exemplos de consultas locais.
* **UserConcurrencyResourceType**: Consultas que ficam dentro do framework da ranhura de simultaneidade. Consultas em tabelas de utilizador final representam exemplos que usaria este tipo de recurso.
* **DmsConcurrencyResourceType**: Esperas resultantes de operações de movimento de dados.
* **BackupConcurrencyResourceType**: Este wait indica que uma base de dados é a cópia de segurança. O valor máximo para este tipo de recurso é 1. Se várias cópias de segurança foram solicitadas ao mesmo tempo, os outros fila. Em geral, recomendamos um tempo mínimo entre os instantâneos consecutivos de 10 minutos. 

O `sys.dm_pdw_waits` DMV pode ser utilizado para ver quais os recursos que um pedido está a aguardar.

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

O `sys.dm_pdw_resource_waits` DMV mostra as informações de espera para uma determinada consulta. Recursos tempo de espera medidas de tempo o aguardar por recursos para ser fornecido. Tempo de espera de sinal é o tempo que demora para os servidores SQL subjacentes agendar a consulta para a CPU.

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

Também pode utilizar o `sys.dm_pdw_resource_waits` DMV calcular quantos blocos de simultaneidade e foi concedidos.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

O `sys.dm_pdw_wait_stats` DMV pode ser utilizado para análise de tendências históricas de esperas.

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

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a gestão de utilizadores de base de dados e segurança, consulte [proteger uma base de dados no SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md). Para obter mais informações sobre classes de recursos maiores como podem melhorar a qualidade do índice columnstore em cluster, consulte [reconstruir índices para melhorar a qualidade do segmento](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
