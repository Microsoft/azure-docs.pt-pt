---
title: Criar e gerir trabalhos de base de dados elásticos com Transact-SQL (T-SQL)
description: Execute scripts em muitas bases de dados com agente de trabalho de base de dados elástica usando Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/07/2020
ms.openlocfilehash: 740a42dc94cdfa8d5c5a91b32b58cbff4c1bcda0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687776"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Utilize a Transact-SQL (T-SQL) para criar e gerir empregos de base de dados elásticas

Este artigo fornece muitos cenários de exemplo para começar a trabalhar com trabalhos elásticos usando T-SQL.

Os exemplos utilizam os [procedimentos e pontos](#job-stored-procedures) de [vista](#job-views) armazenados disponíveis na base de dados de [*emprego*](sql-database-job-automation-overview.md#job-database).

A Transact-SQL (T-SQL) é utilizada para criar, configurar, executar e gerir empregos. A criação do agente Elástico não é suportada no T-SQL, pelo que primeiro deve criar um *agente de trabalho elástico* utilizando o portal, ou [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Criar uma credencial para a execução de emprego

A credencial é usada para se ligar às bases de dados do seu alvo para a execução do script. A credencial necessita de permissões adequadas, nas bases de dados especificadas pelo grupo alvo, para executar com sucesso o script. Ao utilizar um membro do grupo de alvos de servidor e/ou pool, é altamente sugerido criar uma credencial master para usar para refrescar a credencial antes da expansão do servidor e/ou piscina no momento da execução do trabalho. A credencial de base de dados é criada na base de dados do agente de trabalho. A mesma credencial deve ser utilizada para *criar um Login* e *criar um utilizador a partir de Login para conceder as permissões de base de dados* de login nas bases de dados do alvo.


```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO
```

## <a name="create-a-target-group-servers"></a>Criar um grupo-alvo (servidores)

O exemplo que se segue mostra como executar um trabalho contra todas as bases de dados de um servidor.  
Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-an-individual-database"></a>Excluir uma base de dados individual

O exemplo que se segue mostra como executar um trabalho contra todas as bases de dados de um servidor de base de dados SQL, exceto na base de dados chamada *MappingDB*.  
Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>Criar um grupo-alvo (piscinas)

O exemplo que se segue mostra como direcionar todas as bases de dados de uma ou mais piscinas elásticas.  
Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>Implementar novo esquema para muitas bases de dados

O exemplo que se segue mostra como implementar novos esquemas em todas as bases de dados.  
Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>Recolha de dados utilizando parâmetros incorporados

Em muitos cenários de recolha de dados, pode ser útil incluir algumas destas variáveis de scriptpara ajudar a pós-processar os resultados do trabalho.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Por exemplo, para agrupar todos os resultados da mesma execução de emprego em conjunto, use o *$(job_execution_id)* como mostrado no seguinte comando:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Monitorizar o desempenho da base de dados

O exemplo seguinte cria um novo trabalho para recolher dados de desempenho de várias bases de dados.

Por padrão, o agente de trabalho criará a tabela de saída para armazenar resultados devolvidos. Por conseguinte, o principal indicador de base de dados associado à credencial de saída deve, no mínimo, dispor das seguintes permissões: `CREATE TABLE` na base `ALTER`de dados, `SELECT`na tabela de `INSERT` `DELETE` saída ou no seu esquema, e `SELECT` na vista do catálogo de [sys.indexes.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql)

Se quiser criar manualmente a tabela com antecedência, então precisa de ter as seguintes propriedades:
1. Colunas com o nome e os tipos de dados corretos para o conjunto de resultados.
2. Coluna adicional para internal_execution_id com o tipo de identificador único de dados.
3. Um índice não `IX_<TableName>_Internal_Execution_ID` agrupado nomeado na coluna internal_execution_id.
4. Todas as permissões `CREATE TABLE` listadas acima, exceto a permissão na base de dados.

Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute os seguintes comandos:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>Ver definições de trabalho

O exemplo que se segue mostra como ver as definições atuais de emprego.  
Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j 
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```


## <a name="begin-ad-hoc-execution-of-a-job"></a>Iniciar execução ad hoc de um trabalho

O exemplo que se segue mostra como iniciar um trabalho imediatamente.  
Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```


## <a name="schedule-execution-of-a-job"></a>Agendar execução de um trabalho

O exemplo que se segue mostra como agendar um trabalho para futura execução.  
Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Monitorizar o estado da execução do emprego

O exemplo que se segue mostra como visualizar os detalhes do estado de execução de todos os postos de trabalho.  
Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>Cancelar um trabalho

O exemplo que se segue mostra como cancelar um trabalho.  
Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions 
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```


## <a name="delete-old-job-history"></a>Eliminar histórico de emprego antigo

O exemplo que se segue mostra como eliminar o histórico de trabalho antes de uma data específica.  
Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Apagar um emprego e todo o seu histórico de trabalho

O exemplo que se segue mostra como apagar um emprego e todo o histórico de trabalho relacionado.  
Ligue-se à base de [*dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Procedimentos armazenados de emprego

Os seguintes procedimentos armazenados encontram-se na base de dados de [empregos.](sql-database-job-automation-overview.md#job-database)



|Procedimento armazenado  |Descrição  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Acrescenta um novo emprego.    |
|[sp_update_job](#sp_update_job)    |      Atualiza um trabalho existente.   |
|[sp_delete_job](#sp_delete_job)     |      Elimina um trabalho existente.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Dá um passo para um trabalho.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Atualiza um passo de trabalho.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Elimina um passo de trabalho.    |
|[sp_start_job](#sp_start_job)    |  Começa a executar um trabalho.       |
|[sp_stop_job](#sp_stop_job)     |     Impede a execução de um emprego.   |
|[sp_add_target_group](#sp_add_target_group)    |     Adiciona um grupo alvo.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Elimina um grupo alvo.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Adiciona uma base de dados ou um grupo de bases de dados a um grupo alvo.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Remove um membro do grupo alvo de um grupo alvo.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Remove os registos de história para um trabalho.     |





### <a name="sp_add_job"></a><a name="sp_add_job"></a>sp_add_job

Acrescenta um novo emprego. 
  
#### <a name="syntax"></a>Sintaxe  
  

```syntaxsql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
    [ , [ @job_id = ] job_id OUTPUT ]
```

  
#### <a name="arguments"></a>Argumentos  

** \@[job_name =** ] 'job_name'  
O nome da tarefa. O nome deve ser único e não pode conter a percentagem (%) personagem. job_name é nvarchar(128), sem incumprimento.

** \@[descrição =** ] «Descrição»  
A descrição do trabalho. a descrição é nvarchar (512), com um padrão de NULO. Se a descrição for omitida, é utilizada uma corda vazia.

** \@[habilitado =** ] habilitado  
Se o horário do trabalho está ativado. Ativado é bit, com um padrão de 0 (desativado). Se 0, o trabalho não estiver ativado e não funcionar de acordo com o seu horário; no entanto, pode ser executado manualmente. Se 1, o trabalho funcionará de acordo com o seu horário, e também pode ser executado manualmente.

[ ** \@schedule_interval_type =**] schedule_interval_type  
O valor indica quando o trabalho deve ser executado. schedule_interval_type é nvarchar(50), com um padrão de Once, e pode ser um dos seguintes valores:
- "Uma vez",
- "Minutos",
- "Horas",
- "Dias",
- "Semanas",
- "Meses"

** \@[schedule_interval_count =** ] schedule_interval_count  
Número de períodos schedule_interval_count a ocorrer entre cada execução do trabalho. schedule_interval_count é int, com um padrão de 1. O valor deve ser maior ou igual a 1.

[ ** \@schedule_start_time =** ] schedule_start_time  
Data em que execução de emprego pode começar. schedule_start_time é DATETIME2, com o padrão de 0001-01-01 00:00:00.00.0000000.

** \@[schedule_end_time =** ] schedule_end_time  
Data em que execução de emprego pode parar. schedule_end_time é DATETIME2, com o padrão de 9999-12-31 11:59:59.0000000. 

[ ** \@job_id =** ] job_id OUTPUT  
O número de identificação de emprego atribuído ao trabalho se for criado com sucesso. job_id é uma variável de saída de identificador único do tipo.

#### <a name="return-code-values"></a>Valores do Código de Devolução

0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
sp_add_job devem ser executados a partir da base de dados do agente de trabalho especificada na criação do agente de trabalho.
Depois de sp_add_job ter sido executada para adicionar um trabalho, sp_add_jobstep pode ser usado para adicionar passos que realizam as atividades para o trabalho. O número inicial da versão do trabalho é 0, que será incrementado para 1 quando o primeiro passo for adicionado.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:

- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.

### <a name="sp_update_job"></a><a name="sp_update_job"></a>sp_update_job

Atualiza um trabalho existente.

#### <a name="syntax"></a>Sintaxe

```syntaxsql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
    [ , [ @new_name = ] 'new_name' ]
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
```

#### <a name="arguments"></a>Argumentos
** \@[job_name =** ] 'job_name'  
O nome do trabalho a ser atualizado. job_name é nvarchar(128).

** \@[new_name =** ] 'new_name'  
O novo nome do trabalho. new_name é nvarchar(128).

** \@[descrição =** ] «Descrição»  
A descrição do trabalho. a descrição é nvarchar(512).

** \@[habilitado =** ] habilitado  
Especifica se o horário do trabalho está ativado (1) ou não (0). Ativado é bit.

** \@[schedule_interval_type=** ] schedule_interval_type  
O valor indica quando o trabalho deve ser executado. schedule_interval_type é nvarchar(50) e pode ser um dos seguintes valores:

- "Uma vez",
- "Minutos",
- "Horas",
- "Dias",
- "Semanas",
- "Meses"

** \@[schedule_interval_count=** ] schedule_interval_count  
Número de períodos schedule_interval_count a ocorrer entre cada execução do trabalho. schedule_interval_count é int, com um padrão de 1. O valor deve ser maior ou igual a 1.

** \@[schedule_start_time=** ] schedule_start_time  
Data em que execução de emprego pode começar. schedule_start_time é DATETIME2, com o padrão de 0001-01-01 00:00:00.00.0000000.

** \@[schedule_end_time=** ] schedule_end_time  
Data em que execução de emprego pode parar. schedule_end_time é DATETIME2, com o padrão de 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Valores do Código de Devolução
0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
Depois de sp_add_job ter sido executada para adicionar um trabalho, sp_add_jobstep pode ser usado para adicionar passos que realizam as atividades para o trabalho. O número inicial da versão do trabalho é 0, que será incrementado para 1 quando o primeiro passo for adicionado.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:
- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.



### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Elimina um trabalho existente.

#### <a name="syntax"></a>Sintaxe

```syntaxsql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumentos
** \@[job_name =** ] 'job_name'  
O nome do trabalho a ser apagado. job_name é nvarchar(128).

** \@[força =** ] força  
Especifica se deve apagar se o trabalho tem alguma execução em curso e cancelar todas as execuções em curso (1) ou falhar se houver execuções de emprego em curso (0). força é bit.

#### <a name="return-code-values"></a>Valores do Código de Devolução
0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
O histórico de trabalho é automaticamente eliminado quando um trabalho é apagado.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:
- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.



### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>sp_add_jobstep

Dá um passo para um trabalho.

#### <a name="syntax"></a>Sintaxe


```syntaxsql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] step_name ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]   
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumentos

** \@[job_name =** ] 'job_name'  
O nome do trabalho para adicionar o passo. job_name é nvarchar(128).

** \@[step_id =** ] step_id  
O número de identificação da sequência para o passo de trabalho. Os números de identificação dos passos começam em 1 e incrementam sem lacunas. Se um passo existente já tiver este id, então esse passo e todos os passos seguintes terão o seu id incrementado para que este novo passo possa ser inserido na sequência. Se não for especificado, o step_id será automaticamente atribuído à última na sequência de passos. step_id é um int.

** \@[step_name =** ] step_name  
O nome do degrau. Deve especificar-se, com exceção do primeiro passo de um trabalho que (por conveniência) tem um nome padrão de 'JobStep'. step_name é nvarchar(128).

** \@[command_type =** ] 'command_type'  
O tipo de comando que é executado por este passo de trabalho. command_type é nvarchar(50), com um valor padrão de TSql, o que significa que o valor do @command_type parâmetro é um script T-SQL.

Se especificado, o valor deve ser TSql.

** \@[command_source =** ] 'command_source'  
O tipo de local onde o comando é armazenado. command_source é nvarchar(50), com um valor padrão de Inline, o que significa que o valor do @command_source parâmetro é o texto literal do comando.

Se especificado, o valor deve ser Inline.

[ ** \@comando =** ] 'comando'  
O comando deve ser válido script T-SQL e é executado por este passo de trabalho. o comando é nvarchar(máx), com um padrão de NULO.

** \@[credential_name =** ] 'credential_name'  
O nome da credencial de base de dados armazenada nesta base de dados de controlo de emprego que é utilizada para se ligar a cada uma das bases de dados-alvo dentro do grupo-alvo quando este passo é executado. credential_name é nvarchar(128).

** \@[target_group_name =** ] "group_name-alvo"  
O nome do grupo alvo que contém as bases de dados-alvo em que o passo de trabalho será executado. target_group_name é nvarchar(128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
O atraso antes da primeira tentativa de repetição, se o passo de trabalho falhar na tentativa inicial de execução. initial_retry_interval_seconds é int, com valor padrão de 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
O atraso máximo entre tentativas de reprovação. Se o atraso entre as tentativas crescer mais do que este valor, está limitado a este valor. maximum_retry_interval_seconds é int, com valor padrão de 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
O multiplicador a aplicar-se ao atraso de retry se várias tentativas de execução do passo do trabalho falharem. Por exemplo, se o primeiro retry teve um atraso de 5 segundos e o multiplicador de backoff é de 2.0, então o segundo retry terá um atraso de 10 segundos e o terceiro retry terá um atraso de 20 segundos. retry_interval_backoff_multiplier é real, com valor padrão de 2.0.

** \@[retry_attempts =** ] retry_attempts  
O número de vezes para voltar a tentar a execução se a tentativa inicial falhar. Por exemplo, se o valor retry_attempts for de 10, haverá 1 tentativa inicial e 10 tentativas de repetição, dando um total de 11 tentativas. Se a tentativa final de retry falhar, então a execução do emprego terminará com um ciclo de vida de Falhado. retry_attempts é int, com valor padrão de 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
O tempo máximo permitido para o passo executar. Se este tempo for ultrapassado, então a execução do emprego terminará com um ciclo de vida de TimedOut. step_timeout_seconds int, com o valor padrão de 43.200 segundos (12 horas).

** \@[output_type =** ] 'output_type'  
Se não for nulo, o tipo de destino a que o primeiro conjunto de resultados do comando está escrito. output_type é nvarchar(50), com um incumprimento de NULO.

Se especificado, o valor deve ser SqlDatabase.

** \@[output_credential_name =** ] 'output_credential_name'  
Se não for nulo, o nome da credencial de credencial de base de dados que é utilizado para ligar à base de dados de destino de saída. Deve ser especificado se output_type equivale a SqlDatabase. output_credential_name é nvarchar(128), com um valor padrão de NULO.

** \@[output_subscription_id =** ] 'output_subscription_id'  
Precisa de descrição.

** \@[output_resource_group_name =** ] 'output_resource_group_name'  
Precisa de descrição.

** \@[output_server_name =** ] 'output_server_name'  
Se não for nulo, o nome DNS totalmente qualificado do servidor que contém a base de dados de destino de saída. Deve ser especificado se output_type equivale a SqlDatabase. output_server_name é nvarchar(256), com um incumprimento de NULO.

** \@[output_database_name =** ] 'output_database_name'  
Se não for nulo, o nome da base de dados que contém a tabela de destino de saída. Deve ser especificado se output_type equivale a SqlDatabase. output_database_name é nvarchar(128), com um incumprimento de NULO.

** \@[output_schema_name =** ] "output_schema_name"  
Se não for nulo, o nome do esquema SQL que contém a tabela de destino de saída. Se output_type igual a SqlDatabase, o valor predefinido é dbo. output_schema_name é nvarchar(128).

** \@[output_table_name =** ] "output_table_name"  
Se não for nulo, o nome da tabela a que o primeiro conjunto de resultados do comando será escrito. Se a tabela já não existir, será criada com base no esquema do conjunto de resultados retornado. Deve ser especificado se output_type equivale a SqlDatabase. output_table_name é nvarchar(128), com um valor padrão de NULO.

[ ** \@job_version =** ] produção job_version  
Parâmetro de saída que será atribuído o novo número da versão de trabalho. job_version é int.

[ ** \@max_parallelism =** ] max_parallelism OUTPUT  
O nível máximo de paralelismo por piscina elástica. Se definido, o passo de trabalho será restrito a funcionar apenas com um máximo de tantas bases de dados por piscina elástica. Isto aplica-se a cada piscina elástica que esteja diretamente incluída no grupo alvo ou esteja dentro de um servidor que está incluído no grupo alvo. max_parallelism é int.


#### <a name="return-code-values"></a>Valores do Código de Devolução
0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
Quando sp_add_jobstep for bem sucedido, o número atual da versão do trabalho é incrementado. Da próxima vez que o trabalho for executado, a nova versão será usada. Se o trabalho está a ser executado, essa execução não conterá o novo passo.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:  

- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.



### <a name="sp_update_jobstep"></a><a name="sp_update_jobstep"></a>sp_update_jobstep

Atualiza um passo de trabalho.

#### <a name="syntax"></a>Sintaxe

```syntaxsql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @new_id = ] new_id ]   
     [ , [ @new_name = ] 'new_name' ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumentos
** \@[job_name =** ] 'job_name'  
O nome do trabalho a que o passo pertence. job_name é nvarchar(128).

** \@[step_id =** ] step_id  
O número de identificação da etapa de trabalho a modificar. Deve especificar step_id ou step_name. step_id é um int.

** \@[step_name =** ] 'step_name'  
O nome do passo a modificar. Deve especificar step_id ou step_name. step_name é nvarchar(128).

[new_id = ** \@]** new_id  
O novo número de identificação da sequência para o passo de trabalho. Os números de identificação dos passos começam em 1 e incrementam sem lacunas. Se um passo for reordenado, outros passos serão automaticamente renumerados.

** \@[new_name =** ] 'new_name'  
O novo nome do passo. new_name é nvarchar(128).

** \@[command_type =** ] 'command_type'  
O tipo de comando que é executado por este passo de trabalho. command_type é nvarchar(50), com um valor padrão de TSql, o que significa que o valor do @command_type parâmetro é um script T-SQL.

Se especificado, o valor deve ser TSql.

** \@[command_source =** ] 'command_source'  
O tipo de local onde o comando é armazenado. command_source é nvarchar(50), com um valor padrão de Inline, o que significa que o valor do @command_source parâmetro é o texto literal do comando.

Se especificado, o valor deve ser Inline.

[ ** \@comando =** ] 'comando'  
O(s) comando(s) deve ser válido script T-SQL e é executado por este passo de trabalho. o comando é nvarchar(máx), com um padrão de NULO.

** \@[credential_name =** ] 'credential_name'  
O nome da credencial de base de dados armazenada nesta base de dados de controlo de emprego que é utilizada para se ligar a cada uma das bases de dados-alvo dentro do grupo-alvo quando este passo é executado. credential_name é nvarchar(128).

** \@[target_group_name =** ] "group_name-alvo"  
O nome do grupo alvo que contém as bases de dados-alvo em que o passo de trabalho será executado. target_group_name é nvarchar(128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
O atraso antes da primeira tentativa de repetição, se o passo de trabalho falhar na tentativa inicial de execução. initial_retry_interval_seconds é int, com valor padrão de 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
O atraso máximo entre tentativas de reprovação. Se o atraso entre as tentativas crescer mais do que este valor, está limitado a este valor. maximum_retry_interval_seconds é int, com valor padrão de 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
O multiplicador a aplicar-se ao atraso de retry se várias tentativas de execução do passo do trabalho falharem. Por exemplo, se o primeiro retry teve um atraso de 5 segundos e o multiplicador de backoff é de 2.0, então o segundo retry terá um atraso de 10 segundos e o terceiro retry terá um atraso de 20 segundos. retry_interval_backoff_multiplier é real, com valor padrão de 2.0.

** \@[retry_attempts =** ] retry_attempts  
O número de vezes para voltar a tentar a execução se a tentativa inicial falhar. Por exemplo, se o valor retry_attempts for de 10, haverá 1 tentativa inicial e 10 tentativas de repetição, dando um total de 11 tentativas. Se a tentativa final de retry falhar, então a execução do emprego terminará com um ciclo de vida de Falhado. retry_attempts é int, com valor padrão de 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
O tempo máximo permitido para o passo executar. Se este tempo for ultrapassado, então a execução do emprego terminará com um ciclo de vida de TimedOut. step_timeout_seconds int, com o valor padrão de 43.200 segundos (12 horas).

** \@[output_type =** ] 'output_type'  
Se não for nulo, o tipo de destino a que o primeiro conjunto de resultados do comando está escrito. Para repor o valor da output_type de volta a NULO, defina o valor deste parâmetro para '' (corda vazia). output_type é nvarchar(50), com um incumprimento de NULO.

Se especificado, o valor deve ser SqlDatabase.

** \@[output_credential_name =** ] 'output_credential_name'  
Se não for nulo, o nome da credencial de credencial de base de dados que é utilizado para ligar à base de dados de destino de saída. Deve ser especificado se output_type equivale a SqlDatabase. Para repor o valor da output_credential_name de volta a NULO, defina o valor deste parâmetro para '' (corda vazia). output_credential_name é nvarchar(128), com um valor padrão de NULO.

** \@[output_server_name =** ] 'output_server_name'  
Se não for nulo, o nome DNS totalmente qualificado do servidor que contém a base de dados de destino de saída. Deve ser especificado se output_type equivale a SqlDatabase. Para repor o valor da output_server_name de volta a NULO, defina o valor deste parâmetro para '' (corda vazia). output_server_name é nvarchar(256), com um incumprimento de NULO.

** \@[output_database_name =** ] 'output_database_name'  
Se não for nulo, o nome da base de dados que contém a tabela de destino de saída. Deve ser especificado se output_type equivale a SqlDatabase. Para repor o valor da output_database_name de volta a NULO, defina o valor deste parâmetro para '' (corda vazia). output_database_name é nvarchar(128), com um incumprimento de NULO.

** \@[output_schema_name =** ] "output_schema_name"  
Se não for nulo, o nome do esquema SQL que contém a tabela de destino de saída. Se output_type igual a SqlDatabase, o valor predefinido é dbo. Para repor o valor da output_schema_name de volta a NULO, defina o valor deste parâmetro para '' (corda vazia). output_schema_name é nvarchar(128).

** \@[output_table_name =** ] "output_table_name"  
Se não for nulo, o nome da tabela a que o primeiro conjunto de resultados do comando será escrito. Se a tabela já não existir, será criada com base no esquema do conjunto de resultados retornado. Deve ser especificado se output_type equivale a SqlDatabase. Para repor o valor da output_server_name de volta a NULO, defina o valor deste parâmetro para '' (corda vazia). output_table_name é nvarchar(128), com um valor padrão de NULO.

[ ** \@job_version =** ] produção job_version  
Parâmetro de saída que será atribuído o novo número da versão de trabalho. job_version é int.

[ ** \@max_parallelism =** ] max_parallelism OUTPUT  
O nível máximo de paralelismo por piscina elástica. Se definido, o passo de trabalho será restrito a funcionar apenas com um máximo de tantas bases de dados por piscina elástica. Isto aplica-se a cada piscina elástica que esteja diretamente incluída no grupo alvo ou esteja dentro de um servidor que está incluído no grupo alvo. Para repor o valor da max_parallelism voltar a nula, defina o valor deste parâmetro para -1. max_parallelism é int.


#### <a name="return-code-values"></a>Valores do Código de Devolução
0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
Quaisquer execuções em curso do trabalho não serão afetadas. Quando sp_update_jobstep for bem sucedido, o número da versão do trabalho é incrementado. Da próxima vez que o trabalho for executado, a nova versão será usada.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:

- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmina podem usar este procedimento armazenado para editar os atributos de empregos que são propriedade de outros utilizadores




### <a name="sp_delete_jobstep"></a><a name="sp_delete_jobstep"></a>sp_delete_jobstep

Retira um passo de trabalho de um emprego.

#### <a name="syntax"></a>Sintaxe


```syntaxsql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumentos
** \@[job_name =** ] 'job_name'  
O nome do trabalho a partir do qual o passo será removido. job_name é nvarchar(128), sem incumprimento.

** \@[step_id =** ] step_id  
O número de identificação do passo de trabalho a eliminar. Deve especificar step_id ou step_name. step_id é um int.

** \@[step_name =** ] 'step_name'  
O nome do passo a ser apagado. Deve especificar step_id ou step_name. step_name é nvarchar(128).

[ ** \@job_version =** ] produção job_version  
Parâmetro de saída que será atribuído o novo número da versão de trabalho. job_version é int.

#### <a name="return-code-values"></a>Valores do Código de Devolução
0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
Quaisquer execuções em curso do trabalho não serão afetadas. Quando sp_update_jobstep for bem sucedido, o número da versão do trabalho é incrementado. Da próxima vez que o trabalho for executado, a nova versão será usada.

As outras etapas de trabalho serão automaticamente renumeradas para colmatar a lacuna deixada pelo passo de trabalho eliminado.
 
#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:
- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.






### <a name="sp_start_job"></a><a name="sp_start_job"></a>sp_start_job

Começa a executar um trabalho.

#### <a name="syntax"></a>Sintaxe


```syntaxsql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumentos
** \@[job_name =** ] 'job_name'  
O nome do trabalho a partir do qual o passo será removido. job_name é nvarchar(128), sem incumprimento.

[ ** \@job_execution_id =** ] job_execution_id OUTPUT  
Parâmetro de saída que será atribuído a identidade da execução do trabalho. job_version é um identificador único.

#### <a name="return-code-values"></a>Valores do Código de Devolução
0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
Nenhum.
 
#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:
- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>sp_stop_job

Impede a execução de um emprego.

#### <a name="syntax"></a>Sintaxe


```syntaxsql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumentos
** \@[job_execution_id =** ] job_execution_id  
O número de identificação da execução do emprego para parar. job_execution_id é identificador único, com padrão de NULO.

#### <a name="return-code-values"></a>Valores do Código de Devolução
0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
Nenhum.
 
#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:
- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.


### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Adiciona um grupo alvo.

#### <a name="syntax"></a>Sintaxe


```syntaxsql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumentos
** \@[target_group_name =** ] 'target_group_name'  
O nome do grupo alvo para criar. target_group_name é nvarchar(128), sem incumprimento.

[ ** \@target_group_id =** ] target_group_id OUTPUT O número de identificação do grupo-alvo atribuído ao trabalho se for criado com sucesso. target_group_id é uma variável de saída de identificador único tipo, com um padrão de NULO.

#### <a name="return-code-values"></a>Valores do Código de Devolução
0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
Os grupos-alvo fornecem uma maneira fácil de direcionar um trabalho para uma coleção de bases de dados.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:
- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Elimina um grupo alvo.

#### <a name="syntax"></a>Sintaxe


```syntaxsql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumentos
** \@[target_group_name =** ] 'target_group_name'  
O nome do grupo alvo para apagar. target_group_name é nvarchar(128), sem incumprimento.

#### <a name="return-code-values"></a>Valores do Código de Devolução
0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
Nenhum.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:
- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.

### <a name="sp_add_target_group_member"></a><a name="sp_add_target_group_member"></a>sp_add_target_group_member

Adiciona uma base de dados ou um grupo de bases de dados a um grupo alvo.

#### <a name="syntax"></a>Sintaxe

```syntaxsql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]   
        [ , [ @target_type = ] 'target_type' ]   
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]   
        [ , [ @server_name = ] 'server_name' ]   
        [ , [ @database_name = ] 'database_name' ]   
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]   
        [ , [ @shard_map_name = ] 'shard_map_name' ]   
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argumentos
** \@[target_group_name =** ] 'target_group_name'  
O nome do grupo alvo ao qual o membro será adicionado. target_group_name é nvarchar(128), sem incumprimento.

** \@[membership_type =** ] 'membership_type'  
Especifica se o membro do grupo alvo será incluído ou excluído. target_group_name é nvarchar(128), com padrão de 'Incluir'. Os valores válidos para target_group_name são 'Incluir' ou 'Excluir'.

** \@[target_type =** ] 'target_type'  
O tipo de base de dados alvo ou a recolha de bases de dados, incluindo todas as bases de dados de um servidor, todas as bases de dados de um pool elástico, todas as bases de dados num mapa ou uma base de dados individual. target_type é nvarchar(128), sem incumprimento. Os valores válidos para target_type são 'SqlServer', 'SqlElasticPool', 'SqlDatabase', ou 'SqlShardMap'. 

** \@[refresh_credential_name =** ] 'refresh_credential_name'  
O nome do servidor de base de dados SQL. refresh_credential_name é nvarchar(128), sem incumprimento.

** \@[server_name =** ] "server_name"  
O nome do servidor de base de dados SQL que deve ser adicionado ao grupo alvo especificado. server_name deve ser especificado quando target_type é 'SqlServer'. server_name é nvarchar(128), sem incumprimento.

** \@[database_name =** ] 'database_name'  
O nome da base de dados que deve ser adicionado ao grupo-alvo especificado. database_name devem ser especificados quando target_type é "SqlDatabase". database_name é nvarchar(128), sem incumprimento.

** \@[elastic_pool_name =** ] 'elastic_pool_name'  
O nome da piscina elástica que deve ser adicionado ao grupo-alvo especificado. elastic_pool_name devem ser especificados quando target_type é "SqlElasticPool". elastic_pool_name é nvarchar(128), sem incumprimento.

** \@[shard_map_name =** ] 'shard_map_name'  
O nome do conjunto de mapas de fragmentos que deve ser adicionado ao grupo alvo especificado. elastic_pool_name devem ser especificados quando target_type é 'SqlSqlShardMap'. shard_map_name é nvarchar(128), sem incumprimento.

[ ** \@target_id =** ] target_group_id OUTPUT  
O número de identificação do alvo atribuído ao membro do grupo-alvo se for criado adicionado ao grupo-alvo. target_id é uma variável de saída de identificador único tipo, com um padrão de NULO.
Valores de Código de Devolução 0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
Um trabalho executa em todas as bases de dados únicas dentro de um servidor de base de dados SQL ou em um pool elástico no momento da execução, quando um servidor de base de dados SQL ou piscina elástica é incluído no grupo alvo.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:
- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.

#### <a name="examples"></a>Exemplos
O exemplo seguinte adiciona todas as bases de dados dos servidores de Londres e Nova Iorque ao grupo Servers Mantendo informações sobre os clientes. Deve ligar-se à base de dados de empregos especificada na criação do agente de trabalho, neste caso, a ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a><a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Remove um membro do grupo alvo de um grupo alvo.

#### <a name="syntax"></a>Sintaxe


```syntaxsql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Argumentos [ @target_group_name = ] 'target_group_name'  
O nome do grupo alvo para remover o membro do grupo alvo. target_group_name é nvarchar(128), sem incumprimento.

[ @target_id ] target_id  
 O número de identificação do alvo atribuído ao membro do grupo alvo a remover. target_id é um identificador único, com um padrão de NULO.

#### <a name="return-code-values"></a>Valores do Código de Devolução
0 (sucesso) ou 1 (falha)

#### <a name="remarks"></a>Observações
Os grupos-alvo fornecem uma maneira fácil de direcionar um trabalho para uma coleção de bases de dados.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:
- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.

#### <a name="examples"></a>Exemplos
O exemplo seguinte remove o servidor londrino do grupo Servers Mantendo informações sobre o cliente. Deve ligar-se à base de dados de empregos especificada na criação do agente de trabalho, neste caso, a ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sp_purge_jobhistory"></a><a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Remove os registos de história para um trabalho.

#### <a name="syntax"></a>Sintaxe


```syntaxsql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumentos
** \@[job_name =** ] 'job_name'  
O nome do trabalho para o qual apagar os registos de história. job_name é nvarchar(128), com um incumprimento de NULO. Quer job_id quer job_name devem ser especificados, mas ambos não podem ser especificados.

** \@[job_id =** ] job_id  
 O número de identificação de emprego do trabalho para os registos a eliminar. job_id é identificador único, com um padrão de NULO. Quer job_id quer job_name devem ser especificados, mas ambos não podem ser especificados.

** \@[oldest_date =** ] oldest_date  
 O recorde mais antigo a manter na história. oldest_date é DATETIME2, com um padrão de NULO. Quando oldest_date é especificado, sp_purge_jobhistory apenas remove registos mais antigos do que o valor especificado.

#### <a name="return-code-values"></a>Valores do Código de Devolução
0 (sucesso) ou 1 (falha) Os grupos-alvo fornecem uma maneira fácil de direcionar um emprego para uma coleção de bases de dados.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixo sysadmin podem executar este procedimento armazenado. Restringem um utilizador a penas para poder monitorizar os postos de trabalho, pode conceder ao utilizador a seguinte função de base de dados na base de dados do agente de trabalho especificada na criação do agente de trabalho:
- jobs_reader

Para mais detalhes sobre as permissões destas funções, consulte a secção de Permissão neste documento. Apenas os membros da sysadmin a empresa podem utilizar este procedimento armazenado para editar os atributos dos postos de trabalho que são propriedade de outros utilizadores.

#### <a name="examples"></a>Exemplos
O exemplo seguinte adiciona todas as bases de dados dos servidores de Londres e Nova Iorque ao grupo Servers Mantendo informações sobre os clientes. Deve ligar-se à base de dados de empregos especificada na criação do agente de trabalho, neste caso, a ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Vistas de emprego

Na base de [dados](sql-database-job-automation-overview.md#job-database)sobre empregos estão disponíveis as seguintes opiniões.


|Vista  |Descrição  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Mostra o histórico de execução de emprego.      |
|[empregos](#jobs-view)     |   Mostra todos os trabalhos.      |
|[job_versions](#job_versions-view)     |   Mostra todas as versões de trabalho.      |
|[passos de trabalho](#jobsteps-view)     |     Mostra todos os passos na versão atual de cada trabalho.    |
|[jobstep_versions](#jobstep_versions-view)     |     Mostra todos os passos em todas as versões de cada trabalho.    |
|[target_groups](#target_groups-view)     |      Mostra todos os grupos de alvos.   |
|[target_group_members](#target_group_members-view)     |   Mostra todos os membros de todos os grupos-alvo.      |


### <a name="job_executions-view"></a><a name="job_executions-view"></a>job_executions vista

[empregos]. [job_executions]

Mostra o histórico de execução de emprego.


|Nome da coluna|   Tipo de dados   |Descrição|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  Identificação única de um caso de execução de emprego.
|**job_name**   |nvarchar(128)  |Nome do trabalho.
|**job_id** |uniqueidentifier|  Identificação única do trabalho.
|**job_version**    |int    |Versão do trabalho (atualizado automaticamente cada vez que o trabalho é modificado).
|**step_id**    |int|   Identificador único (para este trabalho) para o passo. O NULO indica que esta é a execução do trabalho dos pais.
|**is_active**| bit |Indica se a informação é ativa ou inativa. 1 indica trabalhos ativos, e 0 indica inativo.
|**ciclo de vida**| nvarchar(50)|Valor indicando o estado do trabalho:'Criado', 'Em Progresso', 'Falhado', 'Sucedido', 'Skipped', 'SucceededWithSkipped'|
|**create_time**|   data2(7)|   Data e hora do trabalho foi criado.
|**start_time** |data2(7)|  Data e hora em que o trabalho começou a ser executado. NULO se o trabalho ainda não tiver sido executado.
|**end_time**|  data2(7)    |Data e hora do trabalho terminou a execução. NULO se o trabalho ainda não tiver sido executado ou ainda não tiver concluído a execução.
|**current_attempts**   |int    |Número de vezes que o passo foi novamente tentado. O trabalho dos pais será 0, as execuções de emprego infantil serão 1 ou maior com base na política de execução.
|**current_attempt_start_time** |data2(7)|  Data e hora em que o trabalho começou a ser executado. O NULO indica que esta é a execução do trabalho dos pais.
|**last_message**   |nvarchar (max)| Mensagem de trabalho ou passo histórico. 
|**target_type**|   nvarchar(128)   |Tipo de base de dados de alvo ou recolha de bases de dados, incluindo todas as bases de dados de um servidor, todas as bases de dados de uma piscina elástica ou de uma base de dados. Os valores válidos para target_type são 'SqlServer', 'SqlElasticPool' ou 'SqlDatabase'. O NULO indica que esta é a execução do trabalho dos pais.
|**target_id**  |uniqueidentifier|  Identificação única do membro do grupo alvo.  O NULO indica que esta é a execução do trabalho dos pais.
|**target_group_name**  |nvarchar(128)  |Nome do grupo alvo. O NULO indica que esta é a execução do trabalho dos pais.
|**target_server_name**|    nvarchar(256)|  Nome do servidor de base de dados SQL contido no grupo alvo. Especificado apenas se target_type for 'SqlServer'. O NULO indica que esta é a execução do trabalho dos pais.
|**target_database_name**   |nvarchar(128)| Nome da base de dados contida no grupo alvo. Especificado apenas quando target_type é 'SqlDatabase'. O NULO indica que esta é a execução do trabalho dos pais.


### <a name="jobs-view"></a>vista empregos

[empregos]. [empregos]

Mostra todos os trabalhos.

|Nome da coluna|   Tipo de dados|  Descrição|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Nome do trabalho.|
|**job_id**|    uniqueidentifier    |Identificação única do trabalho.|
|**job_version**    |int    |Versão do trabalho (atualizado automaticamente cada vez que o trabalho é modificado).|
|**descrição**    |nvarchar(512)| Descrição do trabalho. bit ativada Indica se o trabalho está ativado ou desativado. 1 indica empregos habilitados e 0 indica empregos deficientes.|
|**schedule_interval_type** |nvarchar(50)   |Valor indicando quando o trabalho deve ser executado:'Uma vez', 'Minutos', 'Horas', 'Dias', 'Semanas', 'Meses'
|**schedule_interval_count**|   int|    Número de períodos schedule_interval_type a ocorrer entre cada execução do trabalho.|
|**schedule_start_time**    |data2(7)|  Data e hora do trabalho foi iniciado pela última vez.|
|**schedule_end_time**| data2(7)|   Data e hora do trabalho foi executado pela última vez.|


### <a name="job_versions-view"></a><a name="job_versions-view"></a>job_versions vista

[empregos]. [job_versions]

Mostra todas as versões de trabalho.

|Nome da coluna|   Tipo de dados|  Descrição|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Nome do trabalho.|
|**job_id**|    uniqueidentifier    |Identificação única do trabalho.|
|**job_version**    |int    |Versão do trabalho (atualizado automaticamente cada vez que o trabalho é modificado).|


### <a name="jobsteps-view"></a>vista de passos de trabalho

[empregos]. [passos de trabalho]

Mostra todos os passos na versão atual de cada trabalho.

|Nome da coluna    |Tipo de dados| Descrição|
|------|------|-------|
|**job_name**   |nvarchar(128)| Nome do trabalho.|
|**job_id** |uniqueidentifier   |Identificação única do trabalho.|
|**job_version**|   int|    Versão do trabalho (atualizado automaticamente cada vez que o trabalho é modificado).|
|**step_id**    |int    |Identificador único (para este trabalho) para o passo.|
|**step_name**  |nvarchar(128)  |Nome único (para este trabalho) para o passo.|
|**command_type**   |nvarchar(50)   |Tipo de comando para executar no passo de trabalho. Para v1, o valor deve ser igual e padrão a 'TSql'.|
|**command_source** |nvarchar(50)|  Localização do comando. Para v1, 'Inline' é o valor predefinido e apenas aceite.|
|**comando**|   nvarchar (max)|  As ordens para serem executadas por trabalhos elásticos através de command_type.|
|**credential_name**|   nvarchar(128)   |Nome da credencial de base de dados utilizada para executar o trabalho.|
|**target_group_name**| nvarchar(128)   |Nome do grupo alvo.|
|**target_group_id**|   uniqueidentifier|   Identificação única do grupo alvo.|
|**initial_retry_interval_seconds**|    int |O atraso antes da primeira tentativa de novo. O valor padrão é 1.|
|**maximum_retry_interval_seconds** |int|   O atraso máximo entre tentativas de reprovação. Se o atraso entre as tentativas crescer mais do que este valor, está limitado a este valor. O valor padrão é de 120.|
|**retry_interval_backoff_multiplier**  |real|  O multiplicador a aplicar-se ao atraso de retry se várias tentativas de execução do passo do trabalho falharem. O valor predefinido é de 2.0.|
|**retry_attempts** |int|   O número de tentativas de retenção tenta utilizar se este passo falhar. Padrão de 10, o que indica nenhuma tentativa de retry.|
|**step_timeout_seconds**   |int|   A quantidade de tempo em minutos entre tentativas de reprovação. O padrão é 0, o que indica um intervalo de 0 minutos.|
|**output_type**    |nvarchar(11)|  Localização do comando. Na pré-visualização atual, 'Inline' é o valor predefinido e apenas aceite.|
|**output_credential_name**|    nvarchar(128)   |Nome das credenciais a utilizar para ligar ao servidor de destino para armazenar o conjunto de resultados.|
|**output_subscription_id**|    uniqueidentifier|   Identificação única da subscrição do servidor de destino\base de dados para os resultados definidos a partir da execução da consulta.|
|**output_resource_group_name** |nvarchar(128)| Nome do grupo de recursos onde o servidor de destino reside.|
|**output_server_name**|    nvarchar(256)   |Nome do servidor de destino para o conjunto de resultados.|
|**output_database_name**   |nvarchar(128)| Nome da base de dados de destino para o conjunto de resultados.|
|**output_schema_name** |nvarchar (max)| Nome do esquema de destino. Incumprimentos ao dbo, se não especificados.|
|**output_table_name**| nvarchar (max)|  Nome da tabela para armazenar os resultados definidos a partir dos resultados da consulta. A tabela será criada automaticamente com base no esquema dos resultados definidos se não existir. Schema deve coincidir com o esquema dos resultados definidos.|
|**max_parallelism**|   int|    O número máximo de bases de dados por piscina elástica que o passo de trabalho será executado de cada vez. O padrão é NULO, o que significa que não há limite. |


### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>jobstep_versions vista

[empregos]. [jobstep_versions]

Mostra todos os passos em todas as versões de cada trabalho. O esquema é idêntico aos [passos de trabalho.](#jobsteps-view)

### <a name="target_groups-view"></a><a name="target_groups-view"></a>target_groups vista

[empregos]. [target_groups]

Lista todos os grupos-alvo.

|Nome da coluna|Tipo de dados| Descrição|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |O nome do grupo alvo, uma coleção de bases de dados. 
|**target_group_id**    |uniqueidentifier   |Identificação única do grupo alvo.

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>target_group_members vista

[empregos]. [target_group_members]

Mostra todos os membros de todos os grupos-alvo.

|Nome da coluna|Tipo de dados| Descrição|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128|O nome do grupo alvo, uma coleção de bases de dados. |
|**target_group_id**    |uniqueidentifier   |Identificação única do grupo alvo.|
|**membership_type**    |int|   Especifica se o membro do grupo alvo está incluído ou excluído no grupo-alvo. Os valores válidos para target_group_name são 'Incluir' ou 'Excluir'.|
|**target_type**    |nvarchar(128)| Tipo de base de dados de alvo ou recolha de bases de dados, incluindo todas as bases de dados de um servidor, todas as bases de dados de uma piscina elástica ou de uma base de dados. Os valores válidos para target_type são 'SqlServer', 'SqlElasticPool', 'SqlDatabase', ou 'SqlShardMap'.|
|**target_id**  |uniqueidentifier|  Identificação única do membro do grupo alvo.|
|**refresh_credential_name**    |nvarchar(128)  |Nome da credencial de base de dados utilizada para ligar ao membro do grupo alvo.|
|**subscription_id**    |uniqueidentifier|  Identificação única da subscrição.|
|**resource_group_name**    |nvarchar(128)| Nome do grupo de recursos em que reside o membro do grupo-alvo.|
|**server_name**    |nvarchar(128)  |Nome do servidor de base de dados SQL contido no grupo alvo. Especificado apenas se target_type for 'SqlServer'. |
|**database_name**  |nvarchar(128)  |Nome da base de dados contida no grupo alvo. Especificado apenas quando target_type é 'SqlDatabase'.|
|**elastic_pool_name**  |nvarchar(128)| Nome da piscina elástica contida no grupo alvo. Especificado apenas quando target_type é 'SqlElasticPool'.|
|**shard_map_name** |nvarchar(128)| Nome do mapa do fragmento contido no grupo alvo. Especificado apenas quando target_type é 'SqlShardMap'.|


## <a name="resources"></a>Recursos

 - Ícone de ![ligação de tópicoS](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "Ícone de link tópico") [Convenções de Sintaxe Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Passos seguintes

- [Criar e gerir Tarefas Elásticas com o PowerShell](elastic-jobs-powershell.md)
- [Autorização e Permissões Servidor SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
