---
title: Monitorize o desempenho utilizando DMVs
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Aprenda a detetar e diagnosticar problemas de desempenho comuns utilizando pontos de vista dinâmicos de gestão para monitorizar a Base de Dados SQL do Microsoft Azure e a Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/15/2021
ms.openlocfilehash: 5c0de2c1589bfa495ab6ad287b998c403041674c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592171"
---
# <a name="monitoring-microsoft-azure-sql-database-and-azure-sql-managed-instance-performance-using-dynamic-management-views"></a>Monitorizar o desempenho do Azure SQL Managed Instance e da Base de Dados SQL do Microsoft Azure com as vistas de gestão dinâmicas
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Base de Dados DO Microsoft Azure SQL e a Azure SQL Managed Instance permitem um subconjunto de visões dinâmicas de gestão para diagnosticar problemas de desempenho, que podem ser causados por consultas bloqueadas ou de longa duração, estrangulamentos de recursos, planos de consulta deficientes, e assim por diante. Este artigo fornece informações sobre como detetar problemas de desempenho comuns utilizando pontos de vista dinâmicos de gestão.

A Base de Dados DO Microsoft Azure SQL e a Azure SQL Managed Instance suportam parcialmente três categorias de pontos de vista dinâmicos de gestão:

- Pontos de vista dinâmicos relacionados com a base de dados.
- Pontos de vista dinâmicos relacionados com a execução.
- Pontos de vista dinâmicos relacionados com transações.

Para obter informações detalhadas sobre pontos de vista dinâmicos de gestão, consulte [Vistas e Funções de Gestão Dinâmica (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

## <a name="monitor-with-sql-insights"></a>Monitor com insights SQL

[Azure Monitor SQL insights](../../azure-monitor/insights/sql-insights-overview.md) é uma ferramenta para monitorizar instâncias geridas Azure SQL, bases de dados Azure SQL e instâncias do SQL Server em VMs Azure SQL. Este serviço utiliza um agente remoto para capturar dados a partir de pontos de vista dinâmicos de gestão (DMVs) e encaminha os dados para a Azure Log Analytics, onde pode ser monitorizado e analisado. Pode ver estes dados do [Azure Monitor](../../azure-monitor/overview.md) em vistas fornecidas ou aceder diretamente aos dados do Registo para executar consultas e analisar tendências. Para começar a utilizar insights SQL do Monitor Azure, consulte [ativar os insights SQL](../../azure-monitor/insights/sql-insights-enable.md).

## <a name="permissions"></a>Permissões

Na Base de Dados Azure SQL, consultar uma visão dinâmica de gestão requer permissões **VIEW DATABASE STATE.** A permissão **VIEW DATABASE STATE** devolve informações sobre todos os objetos dentro da base de dados atual.
Para conceder a permissão **DO VIEW DATABASE STATE** a um utilizador de base de dados específico, execute a seguinte consulta:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

Em Azure SQL Managed Instance, consultando uma visão de gestão dinâmica requer permissões **VIEW SERVER STATE.** Para mais informações, consulte [o System Dynamic Management Views](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views#required-permissions).

Num caso de SQL Server e em Azure SQL Managed Instance, as vistas dinâmicas de gestão devolvem informações do estado do servidor. Na Base de Dados Azure SQL, retornam apenas informações sobre a sua base de dados lógica atual.

Este artigo contém uma coleção de consultas de DMV que pode executar usando o SQL Server Management Studio ou o Azure Data Studio para detetar os seguintes tipos de problemas de desempenho de consulta:

- [Identificar consultas relacionadas com consumo excessivo de CPU](#identify-cpu-performance-issues)
- [PAGELATCH_* e WRITE_LOG esperas relacionadas com estrangulamentos da OI](#identify-io-performance-issues)
- [PAGELATCH_* espera causada por contenção bytTempDB](#identify-tempdb-performance-issues)
- [RESOURCE_SEMAHPORE espera causada por problemas de espera de subsídio de memória](#identify-memory-grant-wait-performance-issues)
- [Identificação de bases de dados e tamanhos de objetos](#calculating-database-and-objects-sizes)
- [Recuperar informações sobre sessões ativas](#monitoring-connections)
- [Recuperar informações de utilização de recursos em todo o sistema e base de dados](#monitor-resource-use)
- [Recuperar informações sobre desempenho de consulta](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>Identificar problemas de desempenho da CPU

Se o consumo de CPU for superior a 80% por longos períodos de tempo, considere as seguintes etapas de resolução de problemas:

### <a name="the-cpu-issue-is-occurring-now"></a>A questão do CPU está a ocorrer agora

Se a questão está a ocorrer agora, há dois cenários possíveis:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Muitas consultas individuais que consomem cumulativamente alta CPU

Utilize a seguinte consulta para identificar os hashes de consulta superior:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Consultas de longa duração que consomem CPU ainda estão em execução

Utilize a seguinte consulta para identificar estas consultas:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>A questão da CPU ocorreu no passado

Se o problema ocorreu no passado e pretender fazer análises de causa de raiz, utilize [a Loja de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Os utilizadores com acesso à base de dados podem utilizar o T-SQL para consultar os dados da Loja de Consultas. As configurações padrão da Loja de Consulta utilizam uma granularidade de 1 hora. Utilize a seguinte consulta para analisar a atividade para consultas elevadas de consumo de CPU. Esta consulta devolve as 15 principais consultas de consumo de CPU. Lembre-se de `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()` mudar:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Uma vez que identifique as consultas problemáticas, é hora de afinar essas consultas para reduzir a utilização do CPU.  Se não tiver tempo para sintonizar as consultas, também poderá optar por atualizar a SLO da base de dados para contornar o problema.

## <a name="identify-io-performance-issues"></a>Identificar problemas de desempenho de IO

Ao identificar problemas de desempenho de E/S, os principais tipos de espera associados a problemas de E/S são:

- `PAGEIOLATCH_*`

  Para problemas de IO de ficheiros de dados (incluindo `PAGEIOLATCH_SH` `PAGEIOLATCH_EX` , , `PAGEIOLATCH_UP` ).  Se o nome do tipo de espera tiver **IO,** aponta para um problema de IO. Se não houver **IO** no nome de espera do trinco da página, aponta para um tipo diferente de problema (por exemplo, contenção temporária).

- `WRITE_LOG`

  Para problemas de IO de registo de transações.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Se a questão da IO está a ocorrer agora

Utilize o [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) ou [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) para ver o `wait_type` e `wait_time` .

#### <a name="identify-data-and-log-io-usage"></a>Identificar dados e registar utilização de IO

Utilize a seguinte consulta para identificar dados e registar a utilização do IO. Se os dados ou iO de registo forem superiores a 80%, significa que os utilizadores utilizaram o IO disponível para o nível de serviço sql Database.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Se o limite de IO tiver sido atingido, tem duas opções:

- Opção 1: Atualizar o tamanho da computação ou o escalão de serviço
- Opção 2: Identificar e afinar as consultas que consomem mais IO.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Ver IO relacionado com tampão utilizando a Loja de Consultas

Para a opção 2, pode utilizar a seguinte consulta contra a Loja de Consultas para iO relacionado com tampão para visualizar as últimas duas horas de atividade rastreada:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Ver IO de registo total para esperas writelog

Se o tipo de espera `WRITELOG` for, utilize a seguinte consulta para visualizar o IO total de registo por declaração:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identificar `tempdb` problemas de desempenho

Ao identificar problemas de desempenho de IO, os tipos de espera de topo associados a `tempdb` problemas `PAGELATCH_*` são `PAGEIOLATCH_*` (não). No entanto, `PAGELATCH_*` esperas nem sempre significam que tens `tempdb` discórdia.  Esta espera também pode significar que tem disputa da página de dados de objeto de utilizador devido a pedidos simultâneos dirigidos à mesma página de dados. Para confirmar ainda mais `tempdb` a contenção, utilize [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para confirmar que o valor wait_resource começa `2:x:y` com onde 2 está é o `tempdb` ID da base de dados, `x` é o ID do ficheiro, e `y` é o ID da página.  

Para a contenção temporária, um método comum é reduzir ou reescrever código de aplicação que se baseia `tempdb` em .  As áreas de `tempdb` utilização comuns incluem:

- Tabelas temporárias
- Variáveis de tabela
- Parâmetros de valor de tabela
- Utilização da loja de versão (associada a transações de longa duração)
- Consultas com planos de consulta que utilizam ordenações, associações hash e spools

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Consultas de topo que usam variáveis de mesa e tabelas temporárias

Utilize a seguinte consulta para identificar consultas de topo que utilizam variáveis de tabela e tabelas temporárias:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Identificar transações de longa duração

Utilize a seguinte consulta para identificar transações de longa duração. As transações de longa duração impedem a limpeza da loja de versões.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identificar problemas de desempenho de espera de subsídio de memória

Se o seu tipo de espera superior `RESOURCE_SEMAHPORE` for e não tiver um problema de utilização de CPU elevado, pode ter um problema de espera de subsídio de memória.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Determinar se uma `RESOURCE_SEMAHPORE` espera é uma espera superior

Use a seguinte consulta para determinar se uma `RESOURCE_SEMAHPORE` espera é uma espera superior

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identify-high-memory-consuming-statements"></a>Identificar declarações de elevado consumo de memória

Utilize a seguinte consulta para identificar declarações de elevado consumo de memória:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Identifique os 10 principais subsídios de memória ativos

Utilize a seguinte consulta para identificar os 10 principais subsídios de memória ativos:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Calcular os tamanhos da base de dados e de objetos

A consulta seguinte devolve o tamanho da base de dados (em megabytes):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

A consulta seguinte devolve o tamanho de objetos individuais (em megabytes) na base de dados:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Conexões de monitorização

Pode utilizar a [vista sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) para obter informações sobre as ligações estabelecidas num servidor específico e casos geridos e detalhes de cada ligação. Além disso, a [visão sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) é útil na recuperação de informações sobre todas as ligações ativas do utilizador e tarefas internas.

A seguinte consulta obtém informações sobre a ligação atual:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Ao executar as **sys.dm_exec_requests** e **sys.dm_exec_sessions pontos de vista**, se tiver a permissão DO VIEW DATABASE **STATE** na base de dados, consulte todas as sessões de execução na base de dados; caso contrário, só se vê a sessão atual.

## <a name="monitor-resource-use"></a>Monitorizar a utilização de recursos

Pode monitorizar a utilização do recurso de base de dados Azure SQL utilizando o [SQL Database Query Performance Insight](query-performance-insight-use.md). Para a base de dados Azure SQL e a exemplo gerida do Azure SQL, pode monitorizar através [da Loja de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

Também pode monitorizar a utilização utilizando estas vistas:

- Base de Dados Azure SQL: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- Azure SQL Caso Gerido: [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database)
- Azure SQL Database e Azure SQL Managed Instance: [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

Pode utilizar a [vista sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) em todas as bases de dados. A **visão sys.dm_db_resource_stats** mostra dados recentes de utilização de recursos relativos ao nível de serviço. Percentagens médias para CPU, IO de dados, registos e memória são registadas a cada 15 segundos e são mantidas durante 1 hora.

Como esta vista proporciona um olhar mais granular sobre o uso de recursos, use **sys.dm_db_resource_stats** primeiro para qualquer análise do estado atual ou resolução de problemas. Por exemplo, esta consulta mostra a utilização média e máxima de recursos para a base de dados atual durante a última hora:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Para outras consultas, consulte os exemplos em [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).

### <a name="sysserver_resource_stats"></a>sys.server_resource_stats

Pode utilizar [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database) para devolver o uso do CPU, IO e dados de armazenamento para uma Instância Gerida Azure SQL. Os dados são recolhidos e agregados dentro de intervalos de cinco minutos. Há uma fila para cada 15 segundos a reportar. Os dados devolvidos incluem uso de CPU, tamanho de armazenamento, utilização de IO e exemplo gerido SKU. Os dados históricos são conservados por aproximadamente 14 dias.

```sql
DECLARE @s datetime;  
DECLARE @e datetime;  
SET @s= DateAdd(d,-7,GetUTCDate());  
SET @e= GETUTCDATE();  
SELECT resource_name, AVG(avg_cpu_percent) AS Average_Compute_Utilization
FROM sys.server_resource_stats
WHERE start_time BETWEEN @s AND @e  
GROUP BY resource_name  
HAVING AVG(avg_cpu_percent) >= 80;
```

### <a name="sysresource_stats"></a>sys.resource_stats

A [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vista na base de dados **principal** tem informações adicionais que podem ajudá-lo a monitorizar o desempenho da sua base de dados no seu nível de serviço específico e tamanho de cálculo. Os dados são recolhidos a cada 5 minutos e são mantidos durante aproximadamente 14 dias. Esta visão é útil para uma análise histórica a longo prazo de como a sua base de dados utiliza recursos.

O gráfico que se segue mostra a utilização do recurso CPU para uma base de dados Premium com o tamanho do cálculo P2 para cada hora numa semana. Este gráfico começa numa segunda-feira, mostra cinco dias de trabalho, e depois mostra um fim de semana, quando muito menos acontece na aplicação.

![Utilização de recursos de base de dados](./media/monitoring-with-dmvs/sql_db_resource_utilization.png)

A partir dos dados, esta base de dados tem atualmente uma carga de CPU de pouco mais de 50% de utilização de CPU em relação ao tamanho do cálculo P2 (meio-dia de terça-feira). Se o CPU é o fator dominante no perfil de recursos da aplicação, então pode decidir que o P2 é o tamanho certo do cálculo para garantir que a carga de trabalho se adequa sempre. Se espera que uma aplicação cresça ao longo do tempo, é uma boa ideia ter um amortecedor de recursos extra para que a aplicação nunca atinja o limite de nível de desempenho. Se aumentar o tamanho do cálculo, pode ajudar a evitar erros visíveis do cliente que podem ocorrer quando uma base de dados não tem energia suficiente para processar os pedidos de forma eficaz, especialmente em ambientes sensíveis à latência. Um exemplo é uma base de dados que suporta uma aplicação que pinta páginas web com base nos resultados das chamadas de base de dados.

Outros tipos de aplicação podem interpretar o mesmo gráfico de forma diferente. Por exemplo, se uma aplicação tentar processar dados de folha de pagamento todos os dias e tiver o mesmo gráfico, este tipo de modelo de "trabalho em lote" pode fazer bem com um tamanho de computação P1. O tamanho do cálculo P1 tem 100 DTUs em comparação com 200 DTUs no tamanho do cálculo P2. O tamanho do cálculo P1 proporciona metade do desempenho do tamanho do cálculo P2. Assim, 50% do uso de CPU em P2 equivale a 100% de uso de CPU em P1. Se a aplicação não tiver intervalos de tempo, pode não importar se um trabalho leva 2 horas ou 2,5 horas para terminar, se for feito hoje. Uma aplicação nesta categoria provavelmente pode usar um tamanho de computação P1. Pode aproveitar o facto de que há períodos de tempo durante o dia em que o uso do recurso é menor, de modo que qualquer "grande pico" pode derramar para uma das calhas mais tarde. O tamanho do cálculo P1 pode ser bom para este tipo de aplicação (e economizar dinheiro), desde que os trabalhos possam terminar a tempo todos os dias.

O motor de base de dados expõe informações de recursos consumidos para cada base de dados ativa na **sys.resource_stats** vista da base de dados **principal** em cada servidor. Os dados na tabela são agregados para intervalos de 5 minutos. Com os níveis de serviço Basic, Standard e Premium, os dados podem demorar mais de 5 minutos a aparecer na tabela, pelo que estes dados são mais úteis para análises históricas do que para análises quase em tempo real. Consulte a **sys.resource_stats** vista para ver o histórico recente de uma base de dados e validar se a reserva que escolheu entregou o desempenho que deseja quando necessário.

> [!NOTE]
> Na Base de Dados Azure SQL, deve estar ligado à base de dados **principal** para consultar **sys.resource_stats** nos seguintes exemplos.

Este exemplo mostra como os dados desta vista são expostos:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC;
```

![A vista do catálogo sys.resource_stats](./media/monitoring-with-dmvs/sys_resource_stats.png)

O próximo exemplo mostra-lhe diferentes formas de usar a **vista de** catálogo sys.resource_stats para obter informações sobre como a sua base de dados utiliza recursos:

1. Para ver a utilização de recursos da semana passada para o userdb1 da base de dados, pode executar esta consulta:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Para avaliar o quão bem a sua carga de trabalho se adequa ao tamanho do cálculo, é necessário aprofundar cada aspeto das métricas de recursos: CPU, lê, escreve, número de trabalhadores e número de sessões. Aqui está uma consulta revista usando **sys.resource_stats** para reportar os valores médios e máximos destas métricas de recursos:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Com esta informação sobre os valores médios e máximos de cada métrica de recurso, pode avaliar o quão bem a sua carga de trabalho se encaixa no tamanho do cálculo que escolheu. Normalmente, os valores médios de **sys.resource_stats** dão-lhe uma boa linha de base para usar contra o tamanho do alvo. Deve ser a sua vara de medição primária. Por exemplo, pode estar a utilizar o nível de serviço Standard com tamanho de computação S2. As percentagens médias de utilização das leituras e escritas de CPU e IO são inferiores a 40%, o número médio de trabalhadores é inferior a 50, e o número médio de sessões é inferior a 200. A sua carga de trabalho pode encaixar no tamanho da computação S1. É fácil ver se a sua base de dados se enquadra nos limites de trabalho e de sessão. Para ver se uma base de dados se enquadra num tamanho de cálculo mais baixo no que diz respeito ao CPU, lê e escreve, divida o número DTU do tamanho do cálculo inferior pelo número DTU do seu tamanho atual de computação e, em seguida, multiplique o resultado por 100:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    O resultado é a diferença de desempenho relativa entre os dois tamanhos de cálculo em percentagem. Se o seu uso de recursos não exceder esta quantidade, a sua carga de trabalho poderá encaixar no tamanho inferior do cálculo. No entanto, é necessário olhar para todas as gamas de valores de utilização de recursos e determinar, por percentagem, quantas vezes a carga de trabalho da sua base de dados se encaixaria no tamanho inferior do cálculo. As seguintes saídas de consulta a percentagem de ajuste por dimensão de recurso, com base no limiar de 40 por cento que calculamos neste exemplo:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Com base no seu nível de serviço de base de dados, pode decidir se a sua carga de trabalho se enquadra no tamanho inferior do cálculo. Se o objetivo da carga de trabalho da sua base de dados for de 99,9% e a consulta anterior devolver valores superiores a 99,9% para as três dimensões dos recursos, a sua carga de trabalho provavelmente encaixa no tamanho do cálculo mais baixo.

    Olhar para a percentagem de ajuste também lhe dá uma ideia de se deve passar para o próximo tamanho de computação superior para cumprir o seu objetivo. Por exemplo, o userdb1 mostra a seguinte utilização do CPU na semana passada:

   | CPU médio por cento | Máximo de CPU por cento |
   | --- | --- |
   | 24.5 |100.00 |

    O CPU médio é de cerca de um quarto do limite do tamanho do cálculo, o que se encaixaria bem no tamanho do cálculo da base de dados. Mas, o valor máximo mostra que a base de dados atinge o limite do tamanho do cálculo. Precisa passar para o próximo tamanho de computação mais elevado? Veja quantas vezes a sua carga de trabalho atinge 100% e depois compara-a com o objetivo de carga de trabalho da base de dados.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Se esta consulta devolver um valor inferior a 99,9% para qualquer uma das três dimensões de recursos, considere mover-se para o próximo tamanho de computação superior ou utilizar técnicas de afinação de aplicações para reduzir a carga na base de dados.

4. Este exercício também considera o seu aumento de carga de trabalho projetado no futuro.

Para conjuntos elásticos, pode monitorizar bases de dados individuais no conjunto, com as técnicas descritas nesta secção. Mas também é possível monitorizar o conjunto como um todo. Para informações, consulte [Monitorizar e gerir um conjunto elástico](elastic-pool-overview.md).

### <a name="maximum-concurrent-requests"></a>Pedidos simultâneos máximos

Para ver o número de pedidos simultâneos, execute esta consulta Transact-SQL na sua base de dados:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Para analisar a carga de trabalho de uma base de dados do SQL Server, modifique esta consulta para filtrar na base de dados específica que pretende analisar. Por exemplo, se tiver uma base de dados no local chamada MyDatabase, esta consulta Transact-SQL devolve a contagem de pedidos simultâneos nessa base de dados:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Isto é apenas uma foto instantânea num único ponto do tempo. Para obter uma melhor compreensão da sua carga de trabalho e requisitos de pedido simultâneos, você precisará recolher muitas amostras ao longo do tempo.

### <a name="maximum-concurrent-logins"></a>Máximo de logins simultâneos

Pode analisar os seus padrões de utilizador e aplicação para ter uma ideia da frequência de logins. Você também pode executar cargas no mundo real em um ambiente de teste para se certificar de que você não está atingindo este ou outros limites que discutimos neste artigo. Não há uma única consulta ou uma visão dinâmica de gestão (DMV) que possa mostrar-lhe contagens de login simultâneas ou histórico.

Se vários clientes utilizarem a mesma cadeia de ligação, o serviço autentica cada login. Se 10 utilizadores se ligarem simultaneamente a uma base de dados utilizando o mesmo nome de utilizador e palavra-passe, existiriam 10 logins simultâneos. Este limite aplica-se apenas à duração do login e da autenticação. Se os mesmos 10 utilizadores se ligarem à base de dados sequencialmente, o número de logins simultâneos nunca seria superior a 1.

> [!NOTE]
> Atualmente, este limite não se aplica às bases de dados em piscinas elásticas.

### <a name="maximum-sessions"></a>Sessões máximas

Para ver o número de sessões ativas atuais, execute esta consulta Transact-SQL na sua base de dados:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections;
```

Se estiver a analisar uma carga de trabalho do SQL Server, modifique a consulta para se concentrar numa base de dados específica. Esta consulta ajuda-o a determinar possíveis necessidades de sessão para a base de dados se estiver a considerar mudá-la para Azure.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase';
```

Mais uma vez, estas consultas devolvem uma contagem pontual. Se recolher várias amostras ao longo do tempo, terá a melhor compreensão da sua utilização da sessão.

Você pode obter estatísticas históricas sobre sessões consultando a vista [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) e revendo a coluna **ative_session_count.**

## <a name="monitoring-query-performance"></a>Desempenho da consulta de monitorização

Consultas lentas ou longas podem consumir recursos significativos do sistema. Esta secção demonstra como utilizar pontos de vista dinâmicos de gestão para detetar alguns problemas de desempenho de consultas comuns.

### <a name="finding-top-n-queries"></a>Encontrar consultas de topo N

O exemplo seguinte devolve informações sobre as cinco principais consultas classificadas pelo tempo médio da CPU. Este exemplo agrega as consultas de acordo com o seu haxixe de consulta, de modo que as consultas logicamente equivalentes são agrupadas pelo seu consumo cumulativo de recursos.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Consultas bloqueadas de monitorização

Consultas lentas ou de longa duração podem contribuir para o consumo excessivo de recursos e ser consequência de consultas bloqueadas. A causa do bloqueio pode ser o mau design de aplicações, os maus planos de consulta, a falta de índices úteis, e assim por diante. Pode utilizar a visão sys.dm_tran_locks para obter informações sobre a atividade de bloqueio atual na base de dados. Por exemplo, código, consulte [sys.dm_tran_locks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql). Para obter mais informações sobre o bloqueio de resolução de [problemas, consulte Compreender e resolver problemas de bloqueio do Azure SQL](understand-resolve-blocking.md).

### <a name="monitoring-query-plans"></a>Planos de consulta de monitorização

Um plano de consulta ineficiente também pode aumentar o consumo de CPU. O exemplo a seguir utiliza a visão [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql) para determinar qual a consulta que utiliza o CPU mais cumulativo.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Ver também

[Introdução à Base de Dados Azure SQL e Azure SQL Gestão de Instância](sql-database-paas-overview.md)