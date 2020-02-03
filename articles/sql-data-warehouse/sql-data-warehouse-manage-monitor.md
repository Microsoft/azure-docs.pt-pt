---
title: Monitorizar a carga de trabalho com DMVs
description: Aprenda a monitorizar a sua carga de trabalho utilizando DMVs.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/23/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 14c4bb843a93fe6d235354f24475b9974142db79
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721154"
---
# <a name="monitor-your-workload-using-dmvs"></a>Monitorizar a carga de trabalho com DMVs
Este artigo descreve como usar pontos de vista de gestão dinâmica (DMVs) para monitorizar a sua carga de trabalho. Incluído está investigando uma execução de consulta no Armazém de Dados Azure SQL.

## <a name="permissions"></a>Permissões
Para consultar os DMVs neste artigo, precisa de ver a BASE DE DADOS STATE ou a permissão CONTROL. Normalmente, conceder o VIEW DATABASE STATE é a permissão preferida, uma vez que é muito mais restritiva.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitorizar ligações
Todos os logins para o SQL Data Warehouse estão registados em [sys.dm_pdw_exec_sessions](https://msdn.microsoft.com/library/mt203883.aspx).  Este DMV contém os últimos 10.000 logins.  O session_id é a chave principal e é atribuído sequencialmente para cada novo logon.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Monitorizar a execução da consulta
Todas as consultas executadas no SQL Data Warehouse são registadas em [sys.dm_pdw_exec_requests](https://msdn.microsoft.com/library/mt203887.aspx).  Este DMV contém as últimas 10.000 consultas executadas.  O request_id identifica exclusivamente cada consulta e é a chave principal para este DMV.  O request_id é atribuído sequencialmente para cada nova consulta e é prefixado com QID, que significa identificação de consulta.  Consultar este DMV para um dado session_id mostra todas as consultas para um determinado logon.

> [!NOTE]
> Os procedimentos armazenados utilizam várias IDs de Pedido.  As iDs de pedido são atribuídas por ordem sequencial. 
> 
> 

Aqui estão os passos a seguir para investigar planos de execução de consulta e tempos para uma consulta particular.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>PASSO 1: Identifique a consulta que pretende investigar
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

A partir dos resultados da consulta anterior, **note a Identificação de Pedido** da consulta que gostaria de investigar.

As consultas no estado **suspenso** podem ser filas devido a um grande número de consultas de funcionamento ativos. Estas consultas também aparecem no [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql) aguarda consulta com um tipo de UserConcurrencyResourceType. Para obter informações sobre os limites da concurrency, consulte os limites de memória e concurrency para as classes [Azure SQL Data Warehouse](memory-concurrency-limits.md) ou [Resource para gestão de carga de trabalho](resource-classes-for-workload-management.md). As consultas também podem esperar por outras razões, como por exemplo, bloqueios de objetos.  Se a sua consulta está à espera de um recurso, consulte [consultas de investigação à espera de recursos](#monitor-waiting-queries) mais abaixo neste artigo.

Para simplificar a procura de uma consulta na tabela [sys.dm_pdw_exec_requests,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) utilize o [LABEL](https://msdn.microsoft.com/library/ms190322.aspx) para atribuir um comentário à sua consulta, que pode ser consultada na vista sys.dm_pdw_exec_requests.

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
Utilize o ID de pedido para recuperar o plano SQL (DSQL) distribuído da consulta a partir de [sys.dm_pdw_request_steps](https://msdn.microsoft.com/library/mt203913.aspx).

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Quando um plano DSQL está a demorar mais tempo do que o esperado, a causa pode ser um plano complexo com muitos passos DSQL ou apenas um passo a demorar muito tempo.  Se o plano for em muitos passos com várias operações de movimento, considere otimizar as distribuições da sua tabela para reduzir o movimento de dados. O artigo de distribuição da [tabela](sql-data-warehouse-tables-distribute.md) explica por que razão os dados devem ser movidos para resolver uma consulta. O artigo também explica algumas estratégias de distribuição para minimizar o movimento de dados.

Para investigar mais detalhes sobre um único passo, a *operation_type* coluna do passo de consulta de longa duração e observar o **Índice de Passos:**

* Prossiga com o Passo 3a para **operações SQL**: Operação, Operação Remota, Operação de Devolução.
* Prossiga com a Passo 3b para **operações**de Movimento de Dados : ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>PASSO 3a: Investigar a SQL nas bases de dados distribuídas
Utilize o ID de Pedido e o Índice de Etapa para obter detalhes de [sys.dm_pdw_sql_requests,](https://msdn.microsoft.com/library/mt203889.aspx)que contém informações de execução do passo de consulta em todas as bases de dados distribuídas.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Quando o passo de consulta está em execução, [o DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx) pode ser usado para recuperar o plano estimado do SQL Server a partir da cache do plano SQL Server para o passo em execução numa determinada distribuição.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>PASSO 3b: Investigar o movimento de dados nas bases de dados distribuídas
Utilize o ID de Pedido e o Índice de Passos para obter informações sobre um passo de movimento de dados em cada distribuição a partir de [sys.dm_pdw_dms_workers](https://msdn.microsoft.com/library/mt203878.aspx).

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Verifique a coluna *total_elapsed_time* para ver se uma determinada distribuição está a demorar significativamente mais tempo do que outras para o movimento de dados.
* Para a distribuição a longo prazo, verifique a coluna *rows_processed* para ver se o número de linhas que estão a ser deslocadas dessa distribuição é significativamente maior do que outros. Em caso afirmativo, esta descoberta pode indicar distorções dos seus dados subjacentes.

Se a consulta estiver em execução, pode utilizar [o DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx) para recuperar o plano estimado do SQL Server a partir da cache do plano SQL Server para o Passo SQL atualmente em execução dentro de uma distribuição específica.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitorizar consultas de espera
Se descobrir que a sua consulta não está a progredir porque está à espera de um recurso, aqui está uma consulta que mostra todos os recursos que uma consulta espera.

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

Se a consulta estiver ativamente à espera de recursos de outra consulta, então o Estado será **adquirir Recursos.**  Se a consulta tiver todos os recursos necessários, então o Estado será **concedido.**

## <a name="monitor-tempdb"></a>Tempdb monitor
Tempdb é usado para manter resultados intermédios durante a execução da consulta. Uma utilização elevada da base de dados tempdb pode levar a um desempenho lento da consulta. Cada nó no Azure SQL Data Warehouse tem aproximadamente 1 TB de espaço bruto para a temperatura. Abaixo estão as dicas para monitorizar o uso temporário e para diminuir o uso temporário nas suas consultas. 

### <a name="monitoring-tempdb-with-views"></a>Monitorização tempdb com vistas
Para monitorizar a utilização temporária, instale primeiro a visão [microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) do Microsoft Toolkit para O Armazém de [Dados SQL](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Em seguida, pode executar a seguinte consulta para ver o uso temporário por nó para todas as consultas executadas:

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

Se tiver uma consulta que esteja a consumir uma grande quantidade de memória ou tenha recebido uma mensagem de erro relacionada com a atribuição de tempdb, pode ser devido a uma tabela de criação muito grande [COMO SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ou [insert SELECT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) statement que está falhando na operação final de movimento de dados. Isto pode geralmente ser identificado como uma operação ShuffleMove no plano de consulta distribuído imediatamente antes do SELECT DE ENCAIXE final.  Utilize [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) para monitorizar as operações da ShuffleMove. 

A mitigação mais comum é quebrar a sua declaração CTAS ou INSERIR SELECT em várias demonstrações de carga para que o volume de dados não exceda o limite de 1TB por temperatura do nó. Também pode escalar o seu cluster para um tamanho maior que irá espalhar o tamanho temporário em mais nós reduzindo a temperatura em cada nó individual.

Além das declarações CTAS e INSERT SELECT, consultas grandes e complexas que correm com memória insuficiente podem derramar em tempdb causando falhas nas consultas.  Considere correr com uma classe de [recursos](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management) maior para evitar derramar em tempdb.

## <a name="monitor-memory"></a>Monitorizar a memória

A memória pode ser a causa principal para um desempenho lento e fora de problemas de memória. Considere escalar o seu armazém de dados se encontrar o uso da memória do SQL Server atingindo os seus limites durante a execução da consulta.

A seguinte consulta devolve o uso da memória do Servidor SQL e a pressão de memória por nó:   
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
## <a name="monitor-transaction-log-size"></a>Monitorizar o tamanho do registo de transações
A consulta seguinte devolve o tamanho do registo de transações em cada distribuição. Se um dos ficheiros de registo estiver a atingir os 160 GB, deverá considerar aumentar a sua instância ou limitar o tamanho da sua transação. 
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
## <a name="monitor-transaction-log-rollback"></a>Monitorizar a reversão do registo de transações
Se as suas dúvidas estiverem a falhar ou demorar muito tempo a proceder, pode verificar e monitorizar se tem alguma transação a reverter.
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

## <a name="monitor-polybase-load"></a>Monitorizar a carga polyBase
A seguinte consulta fornece uma estimativa aproximada do progresso da sua carga. A consulta apenas mostra ficheiros que estão a ser processados. 

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

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre DMVs, consulte [as vistas do Sistema](./sql-data-warehouse-reference-tsql-system-views.md).
