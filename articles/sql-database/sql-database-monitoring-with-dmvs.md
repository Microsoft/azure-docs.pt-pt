---
title: Monitorização do desempenho da base de dados usando DMVs do SQL do Azure | Documentos da Microsoft
description: Saiba como detetar e diagnosticar problemas de desempenho comuns ao utilizar vistas de gestão dinâmica para monitorizar a base de dados do Microsoft Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 371632a28d22583f8b206e4d8b9d2b6b4e510ab0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62103768"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Monitorizar o desempenho de base de dados do Azure SQL através de vistas de gestão dinâmica

Base de dados do Microsoft Azure SQL permite que um subconjunto das exibições de gerenciamento dinâmico para diagnosticar problemas de desempenho, que podem ser causados por consultas bloqueadas ou de execução longa, afunilamentos de recursos, planos de consulta fraco e assim por diante. Este tópico fornece informações sobre como detectar problemas de desempenho comuns ao utilizar vistas de gestão dinâmica.

Base de dados SQL parcialmente oferece suporte a três categorias de vistas de gestão dinâmica:

- Vistas de gestão dinâmica relacionadas com a base de dados.
- Vistas de gestão dinâmica relacionadas com a execução.
- Vistas de gestão dinâmica relacionadas com a transação.

Para obter informações detalhadas sobre vistas de gestão dinâmica, consulte [exibições de gerenciamento dinâmico e as funções (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) nos manuais Online do SQL Server.

## <a name="permissions"></a>Permissões

Na base de dados SQL, a consulta de uma vista de gestão dinâmica necessita **estado de banco de dados de EXIBIÇÃO** permissões. O **estado de banco de dados de EXIBIÇÃO** permissão devolve informações sobre todos os objetos na base de dados atual.
Para conceder a **estado de banco de dados de EXIBIÇÃO** permissão a um utilizador de base de dados específica, execute a seguinte consulta:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

Numa instância do SQL Server no local, vistas de gestão dinâmica retornam informações de estado do servidor. Na base de dados SQL, o que elas retornam informações relativas à sua atual base de dados lógica apenas.

## <a name="identify-cpu-performance-issues"></a>Identificar problemas de desempenho de CPU

Se o consumo de CPU for superior a 80% por longos períodos de tempo, considere os seguintes passos de resolução de problemas:

### <a name="the-cpu-issue-is-occurring-now"></a>O problema de CPU está a ocorrer agora

Se o problema está ocorrendo neste momento, existem dois cenários possíveis:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Muitas consultas individuais que cumulativamente e consumam CPU elevada

Utilize a seguinte consulta para identificar os hashes de consulta principais:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM(SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
     FROM(SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
               CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
     GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Consultas de execução demorada que consumam CPU ainda estão em execução

Utilize a seguinte consulta para identificar estas consultas:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
     CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>Ocorreu o problema de CPU no passado

Se o problema ocorreu no passado e pretender raiz analisar a causa, utilize [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Os utilizadores com acesso de base de dados podem utilizar o T-SQL para consultar dados de consulta Store.  Configurações de padrão de consulta Store utilizam uma granularidade de 1 hora.  Utilize a seguinte consulta para examinar a atividade para consultas de consumo de CPU elevada. Essa consulta retorna as 15 CPU consumo consultas principais.  Não se esqueça de alterar `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`:

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

Depois de identificar as consultas problemáticas, está na altura de ajustar essas consultas para reduzir a utilização de CPU.  Se não tiver tempo para otimizar as consultas, pode também optar por atualizar o SLO da base de dados para contornar o problema.

## <a name="identify-io-performance-issues"></a>Identificar problemas de desempenho de e/s

Ao identificar problemas de desempenho de e/s, os tipos de espera principais associados a problemas de e/s são:

- `PAGEIOLATCH_*`

  Para problemas de e/s de arquivo de dados (incluindo `PAGEIOLATCH_SH`, `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP`).  Se tiver o nome do tipo de espera **e/s** nele, ele aponta para um problema de e/s. Se não existir nenhuma **e/s** o nome de espera de bloqueio temporário de página, ele aponta para um tipo diferente de problema (por exemplo, contenção de tempdb).

- `WRITE_LOG`

  Para problemas de e/s de registo de transação.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Se o problema de e/s está a ocorrer neste momento

Utilize o [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) ou [os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) para ver o `wait_type` e `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Identificar os dados e a utilização de e/s de registo

Utilize a seguinte consulta para identificar os dados e registar a utilização de e/s. Se a e/s de dados ou de registo for superior a 80%, significa que os utilizadores têm usado a e/s disponível para a camada de serviços de BD SQL.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Se tiver sido atingido o limite de e/s, tem duas opções:

- Opção 1: Atualizar o tamanho de computação ou camada de serviço
- Opção 2: Identificar e ajustar as consultas a maioria das e/s de consumo.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Ver relacionados com a memória intermédia e/s usando o Store de consulta

Opção 2, pode utilizar a seguinte consulta em relação a Store de consulta para relacionados com a memória intermédia de e/s para ver as últimas duas horas de atividade controlada:

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

#### <a name="view-total-log-io-for-writelog-waits"></a>Ver registo total e/s para WRITELOG aguarda

Se o tipo de espera é `WRITELOG`, utilize a seguinte consulta para ver e/s de registo total pela instrução:

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

Ao identificar problemas de desempenho de e/s, a parte superior aguardar tipos associados `tempdb` problemas é `PAGELATCH_*` (não `PAGEIOLATCH_*`). No entanto, `PAGELATCH_*` esperas não sempre significa que tenha `tempdb` contenção.  Este espera também poderá significar que tenha de contenção de página de dados de objeto de utilizador devido a pedidos simultâneos, visando a mesma página de dados. Para obter mais confirmar `tempdb` contenção, utilize [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) para confirmar que o valor de wait_resource começa com `2:x:y` em que é 2 `tempdb` é o id de base de dados, `x` é o id de ficheiro e o `y` é o id de página.  

Para a contenção do tempdb, um método comum é reduzir ou volte a escrever código de aplicação que se baseia em `tempdb`.  Comuns `tempdb` áreas de utilização incluem:

- Tabelas temporárias
- variáveis de tabela
- Parâmetros de valor de tabela
- Utilização da versão de loja (especificamente associado com transações de longa execução)
- Consultas com planos de consulta que usam tipos, junções de hash e spools

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Principais consultas que usam as variáveis de tabela e tabelas temporárias

Utilize a seguinte consulta para identificar as consultas principais que utilizam as variáveis de tabela e tabelas temporárias:

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

### <a name="identify-long-running-transactions"></a>Identificar as transações de execução longa

Utilize a seguinte consulta para identificar longo executar transações. Transações de longa execução impedir que a limpeza de arquivo da versão.

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

## <a name="identify-memory-grant-wait-performance-issues"></a>Identificar problemas de desempenho de espera de concessão de memória

Se o tipo de espera de sua parte superior é `RESOURCE_SEMAHPORE` e não tiver um problema de utilização elevado da CPU, pode ter uma problema de espera de concessão de memória.

### <a name="determine-if-a-resourcesemahpore-wait-is-a-top-wait"></a>Determinar se um `RESOURCE_SEMAHPORE` espera é uma espera de principal

Utilize a seguinte consulta para determinar se um `RESOURCE_SEMAHPORE` espera é uma espera de principal

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

### <a name="identity-high-memory-consuming-statements"></a>Instruções de consumo de memória elevadas de identidade

Utilize a seguinte consulta para identificar as instruções de consumo de memória elevadas:

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

### <a name="identify-the-top-10-active-memory-grants"></a>Identificar as concessões de memória de Active Directory de 10 principais

Utilize a seguinte consulta para identificar as concessões de memória de Active Directory 10 principais:

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

## <a name="calculating-database-and-objects-sizes"></a>Calcular os tamanhos de base de dados e objetos

A seguinte consulta devolve o tamanho da base de dados (em megabytes):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

A seguinte consulta devolve o tamanho dos objetos individuais (em megabytes) na base de dados:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Ligações de monitorização

Pode utilizar o [sys](https://msdn.microsoft.com/library/ms181509.aspx) para obter informações sobre as ligações estabelecidas para um servidor de base de dados do Azure SQL específico e os detalhes de cada ligação. Além disso, o [exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) vista é útil quando a recuperação das informações sobre todas as ligações de utilizador do Active Directory e tarefas internas.
A consulta seguinte obtém informações sobre a ligação atual:

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
> Ao executar o **sys.dm_exec_requests** e **vistas exec_sessions**, se tiver **estado da base de dados de EXIBIÇÃO** permissão na base de dados, verá a execução de todos os sessões na base de dados; caso contrário, verá apenas na sessão atual.

## <a name="monitor-resource-use"></a>Monitorizar a utilização de recursos

Pode monitorizar a utilização de recursos usando [SQL Database Query Performance Insight](sql-database-query-performance.md) e [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Também pode monitorizar a utilização com as duas exibições:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats

Pode utilizar o [DM db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) vista em cada base de dados SQL. O **DM db_resource_stats** vista mostra os dados de utilização de recursos recentes em relação a camada de serviços. Percentagens de média de CPU, de e/s de dados, de escritas de registo e de memória são gravadas a cada 15 segundos e são mantidas durante uma hora.

Uma vez que esta vista fornece uma visão mais granular no uso de recursos, utilize **DM db_resource_stats** primeiro para qualquer análise do estado atual ou resolução de problemas. Por exemplo, esta consulta mostra a utilização de recursos de média e máxima para a base de dados atual durante a última hora:

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

Para outras consultas, consulte os exemplos [DM db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats

O [resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ver no **mestre** base de dados tem informações adicionais que podem ajudar a monitorizar o desempenho de base de dados SQL na sua camada de serviço específico e tamanho de computação. Os dados são recolhidos a cada 5 minutos e são mantidos para cerca de 14 dias. Esta vista é útil para uma análise de histórica de longo prazo de como a base de dados SQL utiliza recursos.

O gráfico seguinte mostra a utilização de recursos da CPU para um banco de dados Premium com o tamanho de computação P2 por cada hora numa semana. Este gráfico começa na segunda-feira, mostra as 5 dias de trabalho e, em seguida, mostra um fim de semana, quando muito menos acontece no aplicativo.

![Utilização de recursos de base de dados SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Dos dados, esta base de dados tem atualmente um pico de carga de CPU de pouco mais de 50% de utilização de CPU relativo ao P2 computação tamanho (midday na Terça-feira). Se a CPU é o fator dominante no perfil de recurso do aplicativo, em seguida, poderá decidir que P2 tem um tamanho de computação certas para garantir que a carga de trabalho sempre se encaixa. Se necessitar de uma aplicação a crescer ao longo do tempo, é uma boa idéia ter um buffer de recurso adicional para que a aplicação já não atinge o limite de nível de desempenho. Se aumentar o tamanho de computação, pode ajudar a evitar erros visíveis para o cliente que podem ocorrer quando uma base de dados não tem energia suficiente para processar pedidos de forma eficaz, especialmente em ambientes sensíveis à latência. Um exemplo é uma base de dados que oferece suporte a um aplicativo que pinta páginas da Web com base nos resultados das chamadas de base de dados.

Outros tipos de aplicação podem interpretar o mesmo gráfico de forma diferente. Por exemplo, se um aplicativo tenta processar dados de folha de pagamento por dia e tem o mesmo gráfico, esse tipo de modelo de "tarefa do batch" pode fazer bem num tamanho de computação P1. O tamanho de computação P1 tem 100 DTUs em comparação com 200 DTUs em P2 tamanho de computação. O tamanho de computação P1 fornece o que tamanho de computação de metade o desempenho da P2. Então, 50 por cento de utilização de CPU em P2 é igual a utilização de CPU de 100 por cento do P1. Se o aplicativo não tiver tempos limite, poderá não importa se uma tarefa demora duas horas ou 2,5 horas a concluir, se isso é feito hoje mesmo. Provavelmente, uma aplicação nesta categoria pode utilizar um tamanho de computação P1. Pode aproveitar o fato de haver períodos de tempo durante o dia quando uso de recursos é mais baixo, para que qualquer "pico grande" poderá vazam ao longo para um dos vales mais tarde no dia. O tamanho de computação P1 pode ser bom para esse tipo de aplicativo (e poupar dinheiro), enquanto as tarefas podem concluído dentro do prazo por dia.

Expõe de base de dados SQL do Azure consumidos informações de recursos para cada base de dados no Active Directory a **resource_stats** ver da **mestre** em cada servidor da base de dados. Os dados na tabela são agregados para intervalos de 5 minutos. Com os escalões de serviço básico, Standard e Premium, os dados podem demorar mais de 5 minutos a aparecer na tabela, para que estes dados são mais úteis para análise histórica, em vez de análise quase em tempo real. Consulta a **resource_stats** visualizados para ver o histórico recente de uma base de dados e para validar a reserva decidir entregar o desempenho, mesmo quando necessário.

> [!NOTE]
> Tem de estar ligado para o **mestre** base de dados do seu servidor de base de dados SQL para consultar **resource_stats** nos exemplos a seguir.

Este exemplo mostra como os dados nesta vista são expostos:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![A vista de catálogo resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

O exemplo seguinte mostra-lhe diferentes formas que pode utilizar o **resource_stats** vista para obter informações sobre como a base de dados SQL utiliza recursos do catálogo:

1. Para examinar o recurso da semana passada utilizar para o userdb1 de base de dados, pode executar esta consulta:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Para avaliar a eficiência com que sua carga de trabalho se ajusta o tamanho de computação, terá de fazer uma busca detalhada em cada aspecto das métricas de recurso: CPU, leituras, gravações, o número de workers e número de sessões. Aqui está um revisado consultar com o **resource_stats** para comunicar os valores de médios e máximo destas métricas de recurso:

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

3. Com essas informações sobre os valores de médios e máxima de cada métrica de recurso, pode avaliar a eficiência com que sua carga de trabalho se ajusta o tamanho de computação que escolheu. Normalmente, médio de valores da **resource_stats** dão-lhe uma linha de base boa para utilizar com o tamanho de destino. Deve ser o pen Drive primários medida. Por exemplo, poderá estar a utilizar o escalão de serviço Standard com o tamanho de computação de S2. A média utilizar percentagens para leituras de e/s e CPU e gravações são abaixo 40 por cento, o número médio de funções de trabalho está abaixo de 50 e o número médio de sessões é inferior a 200. A carga de trabalho pode se encaixa o tamanho de computação de S1. É fácil ver se a base de dados se encaixa os limites de trabalho e de sessão. Para ver se uma base de dados se encaixa num tamanho de computação mais baixo com respeito a divisão de CPU, leituras e escritas, o número de DTUS na parte inferior tamanho pelo número DTU de seu tamanho atual de computação de computação e, em seguida, multiplique o resultado em 100:

    ```S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40```

    O resultado é a diferença de desempenho relativo entre os dois tamanhos de percentagem de computação. Se a utilização de recursos não exceder este montante, sua carga de trabalho pode se encaixa o tamanho de computação mais baixo. No entanto, precisa examinar todos os intervalos de valores de utilização de recursos e determinar, por porcentagem, a frequência com que sua carga de trabalho de base de dados caberia no tamanho de computação mais baixo. A seguinte consulta devolve a percentagem de ajuste por dimensão de recursos, com base num limiar de 40% que Calculamos neste exemplo:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Com base no escalão de serviço de base de dados, pode decidir se a carga de trabalho se ajusta o tamanho de computação mais baixo. Se o objetivo de carga de trabalho de base de dados é de 99,9% e a consulta anterior devolve valores superiores a 99,9% para todas as dimensões de três recursos, a carga de trabalho provavelmente adapta-se o tamanho de computação mais baixo.

    Observando a percentagem de ajuste também fornece uma idéia se deve avançar para o próximo tamanho maior de computação para cumprir o objetivo. Por exemplo, userdb1 mostra a utilização de CPU seguinte para a última semana:

   | Percentagem de CPU média | Percentagem de CPU máxima |
   | --- | --- |
   | 24.5 |100.00 |

    A CPU média trata de um trimestre do limite do tamanho da computação, o que caberia bem no tamanho de computação da base de dados. No entanto, o valor máximo mostra que a base de dados atinge o limite do tamanho da computação. Precisa mover para o próximo tamanho maior de computação? Veja como muitas vezes sua carga de trabalho de atingir 100 por cento e depois o compare com o objetivo de carga de trabalho de base de dados.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Se essa consulta retorna um valor inferior a 99,9% para qualquer uma das dimensões três recursos, considere a mover para o próximo tamanho de computação superior ou usar técnicas de otimização de aplicações para reduzir a carga na base de dados SQL.

4. Neste exercício também considera o aumento de carga de trabalho previsto no futuro.

Para conjuntos elásticos, pode monitorizar bases de dados individuais no conjunto, com as técnicas descritas nesta secção. Mas também é possível monitorizar o conjunto como um todo. Para informações, consulte [Monitorizar e gerir um conjunto elástico](sql-database-elastic-pool-manage-portal.md).

### <a name="maximum-concurrent-requests"></a>Pedidos em simultâneo máximos

Para ver o número de pedidos simultâneos, execute esta consulta Transact-SQL na base de dados SQL:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R;
    ```

Para analisar a carga de trabalho de uma base de dados do SQL Server no local, modificar esta consulta para filtrar na base de dados específico que pretende analisar. Por exemplo, se tiver uma base de dados no local com o nome MyDatabase, esta consulta de Transact-SQL retorna a contagem de pedidos simultâneos nessa base de dados:

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase';
    ```

Este é apenas um instantâneo num único ponto no tempo. Para obter uma melhor compreensão da sua carga de trabalho e os requisitos de pedido simultâneo, terá de recolher muitos exemplos ao longo do tempo.

### <a name="maximum-concurrent-logins"></a>Inícios de sessão simultâneos máximos

Pode analisar os padrões de utilizador e da aplicação para ter uma idéia da frequência de inícios de sessão. Também pode executar cargas reais num ambiente de teste para se certificar de que não estiver a obter isso ou outros limites que discutimos neste artigo. Não há uma única consulta ou a vista de gestão dinâmica (DMV) que pode apresentar-lhe em simultâneo a conta de início de sessão ou o histórico.

Se vários clientes utilizarem a mesma cadeia de ligação, o serviço autentica cada início de sessão. Se 10 utilizadores em simultâneo ligar a uma base de dados com o mesmo nome de utilizador e palavra-passe, haveria 10 inícios de sessão simultâneos. Este limite aplica-se apenas para a duração do início de sessão e a autenticação. Se os mesmos 10 utilizadores se ligarem à base de dados sequencialmente, o número de inícios de sessão simultâneos nunca seria maior que 1.

> [!NOTE]
> Atualmente, este limite não é aplicável às bases de dados nos conjuntos elásticos.

### <a name="maximum-sessions"></a>Máximo de sessões

Para ver o número de sessões ativas atuais, execute esta consulta Transact-SQL na base de dados SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Se está a analisar uma carga de trabalho do SQL Server no local, modifique a consulta para se concentrar num banco de dados específico. Esta consulta ajuda a determinar as necessidades de sessão possíveis para a base de dados se estiver a considerar movê-lo para a base de dados do Azure SQL.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Novamente, estas consultas devolvem uma contagem de ponto no tempo. Se recolher vários exemplos ao longo do tempo, terá a melhor compreensão da sua sessão de utilizar.

Para análise de base de dados SQL, pode obter estatísticas históricas sessões consultando o [resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) vista e ao rever o **active_session_count** coluna.

## <a name="monitoring-query-performance"></a>Monitorização do desempenho de consulta

Lento ou muito tempo a execução de consultas pode consumir recursos significativos do sistema. Esta secção demonstra como usar exibições de gerenciamento dinâmico para detectar alguns problemas de desempenho de consulta comuns. É uma referência mais antiga, mas ainda útil para resolução de problemas, o [resolver problemas de desempenho no SQL Server 2008](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) artigo no Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Localizar consultas principais N

O exemplo seguinte devolve informações sobre as cinco principais consultas classificadas por tempo médio de CPU. Neste exemplo agrega as consultas de acordo com o hash de consulta, para que as consultas logicamente equivalentes são agrupadas pelo respetivo consumo cumulativo de recursos.

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

### <a name="monitoring-blocked-queries"></a>Monitorização de consultas bloqueadas

Consultas de longa ou lentas podem contribuir para o consumo excessivo de recursos e ser a conseqüência de consultas bloqueadas. A causa do bloqueio pode ser o design da aplicação insatisfatório, planos de consulta incorreta, a falta de índices úteis e assim por diante. Pode utilizar a vista de sys.dm_tran_locks para obter informações sobre a atividade atual de bloqueio na base de dados SQL do Azure. Por exemplo de código, consulte [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) nos manuais Online do SQL Server.

### <a name="monitoring-query-plans"></a>Monitorização de planos de consulta

Um plano de consulta ineficientes também pode aumentar o consumo da CPU. O exemplo seguinte utiliza a [DM exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) vista para determinar que consulta usa a CPU mais cumulativa.

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

## <a name="see-also"></a>Consulte também

[Introdução à base de dados SQL](sql-database-technical-overview.md)
