---
title: Monitorize a carga de trabalho da piscina SQL utilizando DMVs
description: Aprenda a monitorizar a carga de trabalho da piscina Azure Synapse Analytics SQL e a execução de consultas utilizando DMVs.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 9eb1006bdba6c69136c972359bb13420a04f4180
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89048029"
---
# <a name="monitor-your-azure-synapse-analytics-sql-pool-workload-using-dmvs"></a>Monitorize a sua carga de trabalho de piscina Azure Synapse Analytics SQL utilizando DMVs

Este artigo descreve como usar o Dynamic Management Views (DMVs) para monitorizar a sua carga de trabalho, incluindo a investigação da execução de consultas na piscina SQL.

## <a name="permissions"></a>Permissões

Para consultar os DMVs neste artigo, necessita de ver **base de dados de base** de dados ou **permissão DE CONTROLO.** Normalmente, a concessão **do VIEW DATABASE STATE** é a permissão preferida, uma vez que é muito mais restritiva.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitorizar ligações

Todos os logins no seu armazém de dados são registados [em sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Este DMV contém os últimos 10.000 logins.  O session_id é a chave primária e é atribuído sequencialmente para cada novo logon.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Monitorização da execução de consultas

Todas as consultas executadas na piscina SQL são registadas [em sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Este DMV contém as últimas 10.000 consultas executadas.  O request_id identifica exclusivamente cada consulta e é a chave primária para este DMV.  O request_id é atribuído sequencialmente para cada nova consulta e é prefixado com QID, que significa ID de consulta.  Consulta deste DMV por um dado session_id mostra todas as consultas para um determinado início de são.

> [!NOTE]
> Os procedimentos armazenados utilizam vários IDs de pedido.  Os IDs de pedido são atribuídos por ordem sequencial.

Aqui estão os passos a seguir para investigar planos de execução de consultas e horários para uma consulta particular.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>PASSO 1: Identifique a consulta que deseja investigar

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

A partir dos resultados da consulta anterior, **note o ID** do Pedido da consulta que gostaria de investigar.

As consultas no estado **suspenso** podem ser em fila devido a um grande número de consultas de execução ativas. Estas consultas também aparecem no [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) espera consulta com um tipo de UserConcurrencyResourceType. Para obter informações sobre os limites de concordância, consulte [os limites de memória e concuência](memory-concurrency-limits.md) ou [classes de recursos para a gestão da carga de trabalho.](resource-classes-for-workload-management.md) As consultas também podem esperar por outras razões, tais como para bloqueios de objetos.  Se a sua consulta estiver à espera de um recurso, consulte [as consultas de investigação à espera](#monitor-waiting-queries) de recursos mais abaixo neste artigo.

Para simplificar a procura de uma consulta na tabela [sys.dm_pdw_exec_requests,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) utilize o [LABEL](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para atribuir um comentário à sua consulta, que pode ser analisado na opinião sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>PASSO 2: Investigar o plano de consulta

Utilize o ID do pedido para recuperar o plano de SQL distribuído (DSQL) da [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Quando um plano DSQL está a demorar mais tempo do que o esperado, a causa pode ser um plano complexo com muitos passos DSQL ou apenas um passo a demorar muito tempo.  Se o plano for muitos passos com várias operações de movimento, considere otimizar as distribuições da sua mesa para reduzir o movimento de dados. O artigo [de distribuição da tabela](sql-data-warehouse-tables-distribute.md) explica por que os dados devem ser movidos para resolver uma consulta. O artigo explica ainda algumas estratégias de distribuição para minimizar o movimento de dados.

Para investigar mais detalhes sobre um único passo, a *coluna operation_type* do passo de consulta de longa duração e observar o Índice de **Passos:**

* Prossiga com o Passo 3 para **operações SQL**: OnOperation, RemoteOperation, ReturnOperation.
* Proceda ao Passo 4 para **operações de Movimento de Dados**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>PASSO 3: Investigar o SQL nas bases de dados distribuídas

Utilize o ID do Pedido e o Índice de Passo para obter detalhes de [sys.dm_pdw_sql_requests,](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)que contém informações de execução do passo de consulta em todas as bases de dados distribuídas.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Quando o passo de consulta está em curso, [o DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pode ser usado para recuperar o plano estimado do SQL Server a partir da cache do plano do SQL Server para o passo em execução numa determinada distribuição.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>PASSO 4: Investigar o movimento de dados nas bases de dados distribuídas

Utilize o ID do Pedido e o Índice de Passo para obter informações sobre um passo de movimento de dados em cada distribuição a partir de [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Verifique a coluna *total_elapsed_time* para ver se uma determinada distribuição está a demorar significativamente mais tempo do que outras para o movimento de dados.
* Para a distribuição de longa duração, verifique a coluna *rows_processed* para ver se o número de linhas que estão a ser movidas dessa distribuição é significativamente maior do que outros. Em caso afirmativo, esta descoberta pode indicar distorção dos seus dados subjacentes.

Se a consulta estiver em execução, pode utilizar [o DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar o plano estimado do SQL Server a partir da cache do plano sql server para o passo SQL atualmente em execução dentro de uma determinada distribuição.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitorize consultas de espera

Se descobrir que a sua consulta não está a progredir porque está à espera de um recurso, eis uma consulta que mostra todos os recursos que uma consulta está à espera.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Se a consulta estiver ativamente à espera de recursos de outra consulta, então o Estado será **AcquireResources**.  Se a consulta tiver todos os recursos necessários, então o Estado será **concedido.**

## <a name="monitor-tempdb"></a>Temperatura do monitor

A temperatura é usada para manter resultados intermédios durante a execução de consultas. A utilização elevada da base de dados temporária pode levar a um desempenho de consulta lenta. Por cada DW100c configurado, 399 GB de espaço temporário é atribuído (DW1000c teria 3,99 TB de espaço temporário total).  Abaixo estão as dicas para monitorizar o uso temporário e para diminuir o uso temporário nas suas consultas.

### <a name="monitoring-tempdb-with-views"></a>Temperatura de monitorização com vistas

Para monitorizar a utilização temporária, instale primeiro a vista [microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) do [Microsoft Toolkit para piscina SQL](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Em seguida, pode executar a seguinte consulta para ver a utilização temporária por nó para todas as consultas executadas:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

Se tiver uma consulta que esteja a consumir uma grande quantidade de memória ou tiver recebido uma mensagem de erro relacionada com a atribuição de temperatura, pode ser devido a uma tabela de criação muito grande [AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ou INSERIR A declaração [SELECT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) que está a falhar na operação final de movimento de dados. Isto pode geralmente ser identificado como uma operação ShuffleMove no plano de consulta distribuído antes do INSIMENTO FINAL SELECT.  Utilize [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para monitorizar as operações do ShuffleMove.

A mitigação mais comum é quebrar a sua declaração CTAS ou INSERT SELECT em várias demonstrações de carga para que o volume de dados não exceda o limite de 1TB por nó temporário. Também pode escalar o seu cluster para um tamanho maior que irá espalhar o tamanho temporário através de mais nós reduzindo a temperatura em cada nó individual.

Além das declarações CTAS e INSERT SELECT, consultas grandes e complexas que correm com memória insuficiente podem derramar em temporários fazendo com que as consultas falhem.  Considere correr com uma [classe de recursos](resource-classes-for-workload-management.md) maior para evitar derramar em temporário.

## <a name="monitor-memory"></a>Monitorizar a memória

A memória pode ser a causa principal para um desempenho lento e para problemas de memória. Considere escalar o seu armazém de dados se encontrar o uso da memória do SQL Server atingindo os seus limites durante a execução de consultas.

A seguinte consulta devolve o uso da memória do SQL Server e a pressão da memória por nó:

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>Monitorar o tamanho do registo de transações

A seguinte consulta devolve o tamanho do registo de transações em cada distribuição. Se um dos ficheiros de registo estiver a atingir os 160 GB, deve considerar aumentar a sua instância ou limitar o tamanho da sua transação.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>Monitorização do registo de transações

Se as suas consultas estiverem a falhar ou a demorar muito tempo a prosseguir, pode verificar e monitorizar se tem alguma transação a reverter.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>Monitor PolyBase carga

A seguinte consulta fornece uma estimativa aproximada do progresso da sua carga. A consulta apenas mostra ficheiros atualmente em processo.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre DMVs, consulte [as vistas do Sistema](../sql/reference-tsql-system-views.md).
