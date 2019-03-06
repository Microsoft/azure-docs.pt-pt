---
title: Criar e gerir conjuntos do Azure SQL da base de dados elásticos com o Transact-SQL (T-SQL) | Documentos da Microsoft
description: Execute scripts em muitas bases de dados com o agente de tarefa elástica da base de dados com o Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: de0227ac3e3d963b5ec196b58e467dbeaf0dff42
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453461"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Utilizar o Transact-SQL (T-SQL) para criar e gerir conjuntos elásticos da base de dados

Este artigo fornece muitos cenários de exemplo para começar a trabalhar com tarefas elásticas com o T-SQL.

Os exemplos utilizam o [procedimentos armazenados](#job-stored-procedures) e [vistas](#job-views) disponíveis no [ *base de dados de tarefa*](sql-database-job-automation-overview.md#job-database).

O Transact-SQL (T-SQL) é utilizado para criar, configurar, executar e gerir tarefas. Criar o agente de tarefa elástica não é suportado em T-SQL, pelo que deve primeiro criar uma *agente de tarefa elástica* com o portal, ou [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Criar uma credencial para a execução da tarefa

A credencial é utilizada para ligar aos seus bancos de dados de destino para a execução do script. A credencial tem permissões adequadas, nas bases de dados especificadas pelo grupo de destino, para executar o script com êxito. Quando utilizar um servidor de e/ou membro do grupo de destino de agrupamento, é altamente recomendável para criar uma credencial mestre para ser utilizado atualizar a credencial antes da expansão do servidor e/ou do conjunto no momento da execução da tarefa. A credencial com âmbito de base de dados é criada na base de dados de agente de tarefa. A mesma credencial tem de ser utilizada para *criar um início de sessão* e *criar um utilizador de início de sessão para conceder as permissões de base de dados de início de sessão* nas bases de dados de destino.


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

## <a name="create-a-target-group-servers"></a>Criar um grupo de destino (servidores)

O exemplo seguinte mostra como executar uma tarefa em todas as bases de dados num servidor.  
Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:


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

O exemplo seguinte mostra como executar uma tarefa em todas as bases de dados num servidor de base de dados SQL, exceto para a base de dados com o nome *MappingDB*.  
Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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


## <a name="create-a-target-group-pools"></a>Criar um grupo de destino (conjuntos)

O exemplo seguinte mostra como todas as bases de dados num ou mais conjuntos elásticos de destino.  
Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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


## <a name="deploy-new-schema-to-many-databases"></a>Implementar o novo esquema em muitas bases de dados

O exemplo seguinte mostra como implementar o novo esquema em todas as bases de dados.  
Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:


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


## <a name="data-collection-using-built-in-parameters"></a>Recolha de dados com os parâmetros internos

Em muitos cenários de recolha de dados, pode ser útil incluir algumas destas variáveis de script para ajudar a pós-processamento os resultados da tarefa.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Por exemplo, para agrupar todos os resultados da execução do trabalho mesmo em conjunto, utilize o *$(job_execution_id)* conforme mostrado no comando seguinte:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Monitorizar o desempenho da base de dados

O exemplo seguinte cria uma nova tarefa para recolher dados de desempenho de várias bases de dados.

Por predefinição, o agente de tarefa ficará para criar a tabela para armazenar os resultados devolvidos no. Como resultado o início de sessão associado com a credencial utilizada para a credencial de saída tem de ter permissões suficientes para realizar isso. Se quiser criar manualmente a tabela antes do tempo, em seguida, tem de ter as seguintes propriedades:
1. Colunas com o nome correto e tipos de dados para o conjunto de resultados.
2. Coluna adicional para internal_execution_id com o tipo de dados de uniqueidentifier.
3. Um índice não agrupado com o nome "IX_<TableName>_Internal_Execution_ID" na coluna internal_execution_id.

Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute os seguintes comandos:

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


## <a name="view-job-definitions"></a>Ver definições de tarefa

O exemplo seguinte mostra como ver definições de tarefa atual.  
Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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


## <a name="begin-ad-hoc-execution-of-a-job"></a>Iniciar execução ad hoc de uma tarefa

O exemplo seguinte mostra como iniciar uma tarefa de imediato.  
Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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


## <a name="schedule-execution-of-a-job"></a>Agendar a execução de uma tarefa

O exemplo seguinte mostra como agendar um trabalho para execução futura.  
Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Monitorizar o estado de execução da tarefa

O exemplo seguinte mostra como ver os detalhes de estado de execução para todas as tarefas.  
Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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


## <a name="cancel-a-job"></a>Cancelar uma tarefa

O exemplo seguinte mostra como cancelar uma tarefa.  
Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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


## <a name="delete-old-job-history"></a>Eliminar histórico antigo de tarefas

O exemplo seguinte mostra como eliminar histórico de tarefas antes de uma data específica.  
Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Eliminar uma tarefa e o respetivo histórico de tarefas

O exemplo seguinte mostra como eliminar uma tarefa e todos os relacionados de histórico de tarefas.  
Ligar para o [ *base de dados da tarefa* ](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Procedimentos armazenados de tarefa

Os seguintes procedimentos armazenados são no [base de dados de tarefas](sql-database-job-automation-overview.md#job-database).



|Procedimento armazenado  |Descrição  |
|---------|---------|
|[sp_add_job](#spaddjob)     |     Adiciona uma nova tarefa.    |
|[sp_update_job](#spupdatejob)    |      Atualiza uma tarefa existente.   |
|[sp_delete_job](#spdeletejob)     |      Elimina uma tarefa existente.   |
|[sp_add_jobstep](#spaddjobstep)    |    Adiciona um passo a um trabalho.     |
|[sp_update_jobstep](#spupdatejobstep)     |     Atualiza um passo de tarefa.    |
|[sp_delete_jobstep](#spdeletejobstep)     |     Elimina um passo de tarefa.    |
|[sp_start_job](#spstartjob)    |  Inicia uma tarefa em execução.       |
|[sp_stop_job](#spstopjob)     |     Interrompe uma execução de tarefa.   |
|[sp_add_target_group](#spaddtargetgroup)    |     Adiciona um grupo de destino.    |
|[sp_delete_target_group](#spdeletetargetgroup)     |    Elimina um grupo de destino.     |
|[sp_add_target_group_member](#spaddtargetgroupmember)     |    Adiciona uma base de dados ou grupo de bases de dados a um grupo de destino.     |
|[sp_delete_target_group_member](#spdeletetargetgroupmember)     |     Remove um membro do grupo de destino de um grupo de destino.    |
|[sp_purge_jobhistory](#sppurgejobhistory)    |    Remove os registos do histórico para uma tarefa.     |





### <a name="spaddjob"></a>sp_add_job

Adiciona uma nova tarefa. 
  
#### <a name="syntax"></a>Sintaxe  
  

```sql
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

[ **@job_name =** ] 'job_name'  
O nome da tarefa. O nome tem de ser exclusivo e não pode conter o sinal de percentagem) caráter. job_name é nvarchar(128), com nenhuma predefinição.

[ **@description =** ] 'description'  
A descrição da tarefa. a descrição é nvarchar(512), com uma predefinição de NULL. Se a descrição for omitida, é utilizada uma cadeia vazia.

[  **@enabled =** ] ativada  
Se o agendamento do trabalho está ativado. Ativado é o bit, com um padrão de 0 (desativado). Se for 0, a tarefa não está ativada e não é executado de acordo com seu Cronograma; No entanto, pode ser executado manualmente. Se 1, a tarefa será executada de acordo com a sua agenda e também pode ser executada manualmente.

[  **@schedule_interval_type =**] schedule_interval_type  
Valor indica quando a tarefa está a ser executado. schedule_interval_type é nvarchar(50), com uma predefinição de uma vez e pode ser um dos seguintes valores:
- 'Uma vez',
- 'Minutos',
- "Hours",
- 'Dias',
- 'Semanas',
- 'Meses'

[  **@schedule_interval_count =** ] schedule_interval_count  
Número de períodos de schedule_interval_count seja efetuada entre cada execução da tarefa. schedule_interval_count é int, com uma predefinição de 1. O valor tem de ser maior ou igual a 1.

[  **@schedule_start_time =** ] schedule_start_time  
Data na qual tarefa pode começar a execução. schedule_start_time é DATETIME2, com a predefinição de 00:00:00.0000000 0001-01-01.

[ **@schedule_end_time =** ] schedule_end_time  
Data na qual tarefa pode parar a execução. schedule_end_time é DATETIME2, com o padrão de 9999-12-31 11:59:59.0000000. 

[  **@job_id =** ] job_id saída  
O número de identificação de tarefa atribuído às tarefas se criado com êxito. job_id é uma variável de saída do tipo uniqueidentifier.

#### <a name="return-code-values"></a>Valores de código de retorno

0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
sp_add_job tem de ser executada a partir da base de dados de agente do trabalho especificado quando criar o agente de tarefa.
Depois de sp_add_job foi executado para adicionar uma tarefa, sp_add_jobstep pode ser utilizado para adicionar os passos que executam as atividades para a tarefa. Número de versão inicial da tarefa é 0, que será incrementada para 1 quando a primeira etapa é adicionada.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:

- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.

### <a name="spupdatejob"></a>sp_update_job

Atualiza uma tarefa existente.

#### <a name="syntax"></a>Sintaxe

```sql
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
[ **@job_name =** ] 'job_name'  
O nome da tarefa para ser atualizado. job_name é nvarchar(128).

[ **@new_name =** ] 'new_name'  
O novo nome da tarefa. new_name é nvarchar(128).

[ **@description =** ] 'description'  
A descrição da tarefa. a descrição é nvarchar(512).

[  **@enabled =** ] ativada  
Especifica se o agendamento do trabalho é ativado (1) ou não ativado (0). Ativado é o bit.

[  **@schedule_interval_type=** ] schedule_interval_type  
Valor indica quando a tarefa está a ser executado. schedule_interval_type é nvarchar(50) e pode ser um dos seguintes valores:

- 'Uma vez',
- 'Minutos',
- "Hours",
- 'Dias',
- 'Semanas',
- 'Meses'

[  **@schedule_interval_count=** ] schedule_interval_count  
Número de períodos de schedule_interval_count seja efetuada entre cada execução da tarefa. schedule_interval_count é int, com uma predefinição de 1. O valor tem de ser maior ou igual a 1.

[  **@schedule_start_time=** ] schedule_start_time  
Data na qual tarefa pode começar a execução. schedule_start_time é DATETIME2, com a predefinição de 00:00:00.0000000 0001-01-01.

[ **@schedule_end_time=** ] schedule_end_time  
Data na qual tarefa pode parar a execução. schedule_end_time é DATETIME2, com o padrão de 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Depois de sp_add_job foi executado para adicionar uma tarefa, sp_add_jobstep pode ser utilizado para adicionar os passos que executam as atividades para a tarefa. Número de versão inicial da tarefa é 0, que será incrementada para 1 quando a primeira etapa é adicionada.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:
- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.



### <a name="spdeletejob"></a>sp_delete_job

Elimina uma tarefa existente.

#### <a name="syntax"></a>Sintaxe

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumentos
[ **@job_name =** ] 'job_name'  
O nome da tarefa para serem eliminados. job_name é nvarchar(128).

[ **@force =** ] force  
Especifica se pretende eliminar se a tarefa tem quaisquer execuções em curso e cancelar todas as execuções de em curso (1) ou falha se todas as execuções de tarefas estão em curso (0). força é o bit.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Histórico de tarefas é eliminado automaticamente quando um trabalho é eliminado.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:
- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.



### <a name="spaddjobstep"></a>sp_add_jobstep

Adiciona um passo a um trabalho.

#### <a name="syntax"></a>Sintaxe


```sql
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

[ **@job_name =** ] 'job_name'  
O nome da tarefa ao qual pretende adicionar o passo. job_name é nvarchar(128).

[ **@step_id =** ] step_id  
O número de identificação de sequência para o passo da tarefa. Números de identificação de passo começam em 1 e incrementar sem lacunas. Se um passo existente já tem este id, em seguida, que o passo e todos os passos seguintes terão o id do incrementado para que este novo passo pode ser inserido na sequência de. Se não for especificado, o step_id será automaticamente atribuído para o último na sequência de passos. step_id é um int.

[  **@step_name =** ] step_name  
O nome do passo. Tem de ser especificado, exceto a primeira etapa de uma tarefa que (para conveniência) tem um nome predefinido de 'JobStep'. step_name é nvarchar(128).

[ **@command_type =** ] 'command_type'  
O tipo de comando que é executado por este passo de tarefa. command_type é nvarchar(50), com um valor padrão de TSql, que significa que o valor da @command_type parâmetro é um script T-SQL.

Se for especificado, o valor tem de ser TSql.

[ **@command_source =** ] 'command_source'  
O tipo de localização em que o comando é armazenado. command_source é nvarchar(50), com um valor padrão de Inline, que significa que o valor da @command_source parâmetro é o texto literal do comando.

Se for especificado, o valor tem de ser Inline.

[ **@command =** ] 'command'  
O comando deve ser o script de T-SQL válida e, em seguida, é executado por este passo de tarefa. comando é nvarchar (Max), com uma predefinição de NULL.

[ **@credential_name =** ] 'credential_name'  
O nome da base de dados no âmbito a credencial armazenada nesta base de dados de controle de tarefa que é utilizada para ligar a cada uma das bases de dados de destino dentro do grupo de destino quando este passo é executado. credential_name é nvarchar(128).

[ **@target_group_name =** ] 'target-group_name'  
O nome do grupo de destino que contém as bases de dados de destino que será executado o passo da tarefa na. target_group_name é nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
O atraso antes da primeira repetição tentar, se o passo de tarefa falhar durante a tentativa de execução inicial. initial_retry_interval_seconds é int, com o valor predefinido de 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
O atraso máximo entre tentativas de repetição. Se o atraso entre tentativas cresceriam maior do que este valor, ele está limitado a este valor em vez disso. maximum_retry_interval_seconds é int, com o valor predefinido de 120.

[ **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
O multiplicador para aplicar o atraso de repetição se a execução de passo de tarefa de várias tentativas de ativação. Por exemplo, se a primeira repetição tinha um atraso de 5 segundos e o multiplicador de término é 2.0, a segunda repetição terão um atraso de 10 segundos e a repetição de terceiro terá um atraso de 20 segundos. retry_interval_backoff_multiplier é real, com o valor predefinido do 2.0.

[ **@retry_attempts =** ] retry_attempts  
O número de vezes a repetir a execução se a tentativa inicial falhar. Por exemplo, se o valor de retry_attempts é 10, em seguida, haverá 1 inicial tentativa e 10 tentativas de repetição, fornecendo um total de tentativas de 11. Se a tentativa de repetição final falhar, a execução de tarefa irá terminar com um ciclo de vida de falhado. retry_attempts é int, com o valor predefinido de 10.

[ **@step_timeout_seconds =** ] step_timeout_seconds  
A quantidade máxima de tempo permitido para o passo executar. Se for excedido neste momento, em seguida, a execução de tarefa irá terminar com um ciclo de vida de excedido. step_timeout_seconds é int, com o valor predefinido de 43.200 segundos (12 horas).

[ **@output_type =** ] 'output_type'  
Se não for nulo, o tipo de destino do conjunto de resultados do comando de primeira é escrito. output_type é nvarchar(50), com uma predefinição de NULL.

Se for especificado, o valor tem de ser SqlDatabase.

[ **@output_credential_name =** ] 'output_credential_name'  
Se não nulo, o nome da base de dados credencial com âmbito que é utilizada para ligar à base de dados de destino de saída. Tem de ser especificado se output_type é igual a SqlDatabase. output_credential_name é nvarchar(128), com um valor predefinido nulo.

[ **@output_subscription_id =** ] 'output_subscription_id'  
Tem de descrição.

[ **@output_resource_group_name =** ] 'output_resource_group_name'  
Tem de descrição.

[ **@output_server_name =** ] 'output_server_name'  
Se não for null, o nome DNS completamente qualificado do servidor que contém a base de dados de destino de saída. Tem de ser especificado se output_type é igual a SqlDatabase. output_server_name é nvarchar(256), com uma predefinição de NULL.

[ **@output_database_name =** ] 'output_database_name'  
Se não for null, o nome da base de dados que contém a tabela de destino de saída. Tem de ser especificado se output_type é igual a SqlDatabase. output_database_name é nvarchar(128), com uma predefinição de NULL.

[ **@output_schema_name =** ] 'output_schema_name'  
Se não for null, o nome do esquema SQL que contém a tabela de destino de saída. Se output_type for igual a SqlDatabase, o valor predefinido é dbo. output_schema_name é nvarchar(128).

[ **@output_table_name =** ] 'output_table_name'  
Se não for nulo, o nome da tabela que conjunto de resultados do comando de primeira será gravado. Se a tabela já não existir, será criada com base no esquema do conjunto de resultados de retornar. Tem de ser especificado se output_type é igual a SqlDatabase. output_table_name é nvarchar(128), com um valor predefinido nulo.

[  **@job_version =** ] job_version saída  
Parâmetro de saída que será atribuído o novo número de versão da tarefa. job_version é int.

[  **@max_parallelism =** ] max_parallelism saída  
O nível máximo de paralelismo por conjunto elástico. Se o conjunto, em seguida, o passo da tarefa será restrita a só ser executado no máximo, que muitas bases de dados por conjunto elástico. Isto aplica-se para cada conjunto elástico que diretamente está incluído no grupo de destino ou está dentro de um servidor que está incluído no grupo de destino. max_parallelism é int.


#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Quando sp_add_jobstep for bem-sucedida, o número da versão atual da tarefa é incrementado. Da próxima vez que a tarefa é executada, a nova versão será utilizada. Se a tarefa está atualmente em execução, dessa execução não irá conter o novo passo.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:  

- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.



### <a name="spupdatejobstep"></a>sp_update_jobstep

Atualiza um passo de tarefa.

#### <a name="syntax"></a>Sintaxe

```sql
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
[ **@job_name =** ] 'job_name'  
O nome da tarefa à qual pertence o passo. job_name é nvarchar(128).

[ **@step_id =** ] step_id  
O número de identificação para o passo da tarefa ser modificado. Step_id ou step_name tem de ser especificado. step_id é um int.

[  **@step_name =** ] 'step_name'  
O nome do passo deve ser modificada. Step_id ou step_name tem de ser especificado. step_name é nvarchar(128).

[ **@new_id =** ] new_id  
O novo número de identificação sequência para o passo da tarefa. Números de identificação de passo começam em 1 e incrementar sem lacunas. Se um passo foi reordenado, em seguida, outros passos irão ser automaticamente renumerados.

[ **@new_name =** ] 'new_name'  
O novo nome do passo. new_name é nvarchar(128).

[ **@command_type =** ] 'command_type'  
O tipo de comando que é executado por este passo de tarefa. command_type é nvarchar(50), com um valor padrão de TSql, que significa que o valor da @command_type parâmetro é um script T-SQL.

Se for especificado, o valor tem de ser TSql.

[ **@command_source =** ] 'command_source'  
O tipo de localização em que o comando é armazenado. command_source é nvarchar(50), com um valor padrão de Inline, que significa que o valor da @command_source parâmetro é o texto literal do comando.

Se for especificado, o valor tem de ser Inline.

[ **@command =** ] 'command'  
O comando deve ser o script de T-SQL válido e, em seguida, é executado por este passo de tarefa. comando é nvarchar (Max), com uma predefinição de NULL.

[ **@credential_name =** ] 'credential_name'  
O nome da base de dados no âmbito a credencial armazenada nesta base de dados de controle de tarefa que é utilizada para ligar a cada uma das bases de dados de destino dentro do grupo de destino quando este passo é executado. credential_name é nvarchar(128).

[ **@target_group_name =** ] 'target-group_name'  
O nome do grupo de destino que contém as bases de dados de destino que será executado o passo da tarefa na. target_group_name é nvarchar(128).

[  **@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
O atraso antes da primeira repetição tentar, se o passo de tarefa falhar durante a tentativa de execução inicial. initial_retry_interval_seconds é int, com o valor predefinido de 1.

[  **@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
O atraso máximo entre tentativas de repetição. Se o atraso entre tentativas cresceriam maior do que este valor, ele está limitado a este valor em vez disso. maximum_retry_interval_seconds é int, com o valor predefinido de 120.

[ **@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
O multiplicador para aplicar o atraso de repetição se a execução de passo de tarefa de várias tentativas de ativação. Por exemplo, se a primeira repetição tinha um atraso de 5 segundos e o multiplicador de término é 2.0, a segunda repetição terão um atraso de 10 segundos e a repetição de terceiro terá um atraso de 20 segundos. retry_interval_backoff_multiplier é real, com o valor predefinido do 2.0.

[ **@retry_attempts =** ] retry_attempts  
O número de vezes a repetir a execução se a tentativa inicial falhar. Por exemplo, se o valor de retry_attempts é 10, em seguida, haverá 1 inicial tentativa e 10 tentativas de repetição, fornecendo um total de tentativas de 11. Se a tentativa de repetição final falhar, a execução de tarefa irá terminar com um ciclo de vida de falhado. retry_attempts é int, com o valor predefinido de 10.

[ **@step_timeout_seconds =** ] step_timeout_seconds  
A quantidade máxima de tempo permitido para o passo executar. Se for excedido neste momento, em seguida, a execução de tarefa irá terminar com um ciclo de vida de excedido. step_timeout_seconds é int, com o valor predefinido de 43.200 segundos (12 horas).

[ **@output_type =** ] 'output_type'  
Se não for nulo, o tipo de destino do conjunto de resultados do comando de primeira é escrito. Repor o valor de output_type NULL, defina o valor deste parâmetro para ' (cadeia de caracteres vazia). output_type é nvarchar(50), com uma predefinição de NULL.

Se for especificado, o valor tem de ser SqlDatabase.

[ **@output_credential_name =** ] 'output_credential_name'  
Se não nulo, o nome da base de dados credencial com âmbito que é utilizada para ligar à base de dados de destino de saída. Tem de ser especificado se output_type é igual a SqlDatabase. Repor o valor de output_credential_name NULL, defina o valor deste parâmetro para ' (cadeia de caracteres vazia). output_credential_name é nvarchar(128), com um valor predefinido nulo.

[ **@output_server_name =** ] 'output_server_name'  
Se não for null, o nome DNS completamente qualificado do servidor que contém a base de dados de destino de saída. Tem de ser especificado se output_type é igual a SqlDatabase. Repor o valor de output_server_name NULL, defina o valor deste parâmetro para ' (cadeia de caracteres vazia). output_server_name é nvarchar(256), com uma predefinição de NULL.

[ **@output_database_name =** ] 'output_database_name'  
Se não for null, o nome da base de dados que contém a tabela de destino de saída. Tem de ser especificado se output_type é igual a SqlDatabase. Repor o valor de output_database_name NULL, defina o valor deste parâmetro para ' (cadeia de caracteres vazia). output_database_name é nvarchar(128), com uma predefinição de NULL.

[ **@output_schema_name =** ] 'output_schema_name'  
Se não for null, o nome do esquema SQL que contém a tabela de destino de saída. Se output_type for igual a SqlDatabase, o valor predefinido é dbo. Repor o valor de output_schema_name NULL, defina o valor deste parâmetro para ' (cadeia de caracteres vazia). output_schema_name é nvarchar(128).

[ **@output_table_name =** ] 'output_table_name'  
Se não for nulo, o nome da tabela que conjunto de resultados do comando de primeira será gravado. Se a tabela já não existir, será criada com base no esquema do conjunto de resultados de retornar. Tem de ser especificado se output_type é igual a SqlDatabase. Repor o valor de output_server_name NULL, defina o valor deste parâmetro para ' (cadeia de caracteres vazia). output_table_name é nvarchar(128), com um valor predefinido nulo.

[  **@job_version =** ] job_version saída  
Parâmetro de saída que será atribuído o novo número de versão da tarefa. job_version é int.

[  **@max_parallelism =** ] max_parallelism saída  
O nível máximo de paralelismo por conjunto elástico. Se o conjunto, em seguida, o passo da tarefa será restrita a só ser executado no máximo, que muitas bases de dados por conjunto elástico. Isto aplica-se para cada conjunto elástico que diretamente está incluído no grupo de destino ou está dentro de um servidor que está incluído no grupo de destino. Para repor o valor de max_parallelism como null, defina o valor deste parâmetro como -1. max_parallelism é int.


#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Quaisquer execuções em curso do trabalho não serão afetadas. Quando sp_update_jobstep for bem-sucedida, o número da versão da tarefa é incrementado. Da próxima vez que a tarefa é executada, a nova versão será utilizada.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:

- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores




### <a name="spdeletejobstep"></a>sp_delete_jobstep

Remove um passo de tarefa de uma tarefa.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumentos
[ **@job_name =** ] 'job_name'  
O nome da tarefa da qual o passo vai ser removido. job_name é nvarchar(128), com nenhuma predefinição.

[ **@step_id =** ] step_id  
O número de identificação para o passo da tarefa a ser eliminado. Step_id ou step_name tem de ser especificado. step_id é um int.

[  **@step_name =** ] 'step_name'  
O nome do passo será eliminada. Step_id ou step_name tem de ser especificado. step_name é nvarchar(128).

[  **@job_version =** ] job_version saída  
Parâmetro de saída que será atribuído o novo número de versão da tarefa. job_version é int.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Quaisquer execuções em curso do trabalho não serão afetadas. Quando sp_update_jobstep for bem-sucedida, o número da versão da tarefa é incrementado. Da próxima vez que a tarefa é executada, a nova versão será utilizada.

Os outros passos de tarefa irão ser renumerados automaticamente para preencher a lacuna deixado pelo passo de tarefa eliminada.
 
#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:
- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.






### <a name="spstartjob"></a>sp_start_job

Inicia uma tarefa em execução.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumentos
[ **@job_name =** ] 'job_name'  
O nome da tarefa da qual o passo vai ser removido. job_name é nvarchar(128), com nenhuma predefinição.

[  **@job_execution_id =** ] job_execution_id saída  
Parâmetro que será atribuído o id de execução da tarefa de saída. job_version é uniqueidentifier.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Nenhum.
 
#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:
- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.

### <a name="spstopjob"></a>sp_stop_job

Interrompe uma execução de tarefa.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumentos
[ **@job_execution_id =** ] job_execution_id  
O número de identificação da execução da tarefa para parar. job_execution_id é uniqueidentifier, com o padrão de nulo.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Nenhum.
 
#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:
- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.


### <a name="spaddtargetgroup"></a>sp_add_target_group

Adiciona um grupo de destino.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumentos
[ **@target_group_name =** ] 'target_group_name'  
O nome do grupo de destino para criar. target_group_name é nvarchar(128), com nenhuma predefinição.

[  **@target_group_id =** ] target_group_id o destino de saída de grupo número de identificação atribuído à tarefa se criado com êxito. target_group_id é uma variável de saída do tipo uniqueidentifier, com uma predefinição de NULL.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Grupos de destino fornecem uma forma fácil de uma tarefa numa coleção de bases de dados de destino.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:
- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.

### <a name="spdeletetargetgroup"></a>sp_delete_target_group

Elimina um grupo de destino.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumentos
[ **@target_group_name =** ] 'target_group_name'  
O nome do grupo de destino para eliminar. target_group_name é nvarchar(128), com nenhuma predefinição.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Nenhum.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:
- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.

### <a name="spaddtargetgroupmember"></a>sp_add_target_group_member

Adiciona uma base de dados ou grupo de bases de dados a um grupo de destino.

#### <a name="syntax"></a>Sintaxe

```sql
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
[ **@target_group_name =** ] 'target_group_name'  
O nome do grupo de destino ao qual o membro será adicionado. target_group_name é nvarchar(128), com nenhuma predefinição.

[ **@membership_type =** ] 'membership_type'  
Especifica se o membro do grupo de destino será incluído ou excluído. target_group_name é nvarchar(128), com o padrão de "Inclusão". Valores válidos para target_group_name são 'Include' ou "Excluir".

[ **@target_type =** ] 'target_type'  
O tipo de base de dados de destino ou a coleção de bases de dados incluindo todas as bases de dados num servidor, todas as bases de dados num conjunto elástico, todas as bases de dados num mapa de partições horizontais ou uma base de dados individual. target_type é nvarchar(128), com nenhuma predefinição. Valores válidos para target_type são 'SqlServer', "SqlElasticPool", "SqlDatabase" ou 'SqlShardMap'. 

[ **@refresh_credential_name =** ] 'refresh_credential_name'  
O nome do servidor de base de dados SQL. refresh_credential_name é nvarchar(128), com nenhuma predefinição.

[ **@server_name =** ] 'server_name'  
O nome do servidor de base de dados SQL que deve ser adicionado ao grupo de destino especificado. SERVER_NAME deve ser especificado quando target_type é 'SqlServer'. SERVER_NAME é nvarchar(128), com nenhuma predefinição.

[ **@database_name =** ] 'database_name'  
O nome da base de dados que deve ser adicionado ao grupo de destino especificado. database_name deve ser especificado quando target_type é "SqlDatabase". database_name é nvarchar(128), com nenhuma predefinição.

[ **@elastic_pool_name =** ] 'elastic_pool_name'  
O nome do conjunto elástico que deve ser adicionado ao grupo de destino especificado. elastic_pool_name deve ser especificado quando target_type é 'SqlElasticPool'. elastic_pool_name é nvarchar(128), com nenhuma predefinição.

[ **@shard_map_name =** ] 'shard_map_name'  
O nome do agrupamento de mapa de partições horizontais que deve ser adicionado ao grupo de destino especificado. elastic_pool_name deve ser especificado quando target_type é 'SqlSqlShardMap'. shard_map_name é nvarchar(128), com nenhuma predefinição.

[  **@target_id =** ] target_group_id saída  
O número de identificação do destino atribuído para o membro do grupo de destino se criado adicionado ao grupo de destino. target_id é uma variável de saída do tipo uniqueidentifier, com uma predefinição de NULL.
Valores de retorno código 0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Uma tarefa é executada em todas as bases de dados em individuais dentro de um servidor de base de dados SQL ou num conjunto elástico em tempo de execução, quando um servidor de base de dados SQL ou conjunto elástico está incluído no grupo de destino.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:
- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.

#### <a name="examples"></a>Exemplos
O exemplo seguinte adiciona todas as bases de dados em servidores de Londres e NewYork ao grupo de informações do cliente de manutenção de servidores. Tem de ligar à base de dados de tarefas especificado quando criar o agente de tarefa, neste caso ElasticJobs.

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

### <a name="spdeletetargetgroupmember"></a>sp_delete_target_group_member

Remove um membro do grupo de destino de um grupo de destino.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Arguments [ @target_group_name = ] 'target_group_name'  
O nome do grupo de destino a partir da qual pretende remover o membro do grupo de destino. target_group_name é nvarchar(128), com nenhuma predefinição.

[ @target_id = ] target_id  
 O número de identificação de destino atribuído para o membro do grupo de destino a ser removido. target_id é um uniqueidentifier, com uma predefinição de NULL.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Grupos de destino fornecem uma forma fácil de uma tarefa numa coleção de bases de dados de destino.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:
- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.

#### <a name="examples"></a>Exemplos
O exemplo seguinte remove o servidor de Londres do grupo de informações do cliente de manutenção de servidores. Tem de ligar à base de dados de tarefas especificado quando criar o agente de tarefa, neste caso ElasticJobs.

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

### <a name="sppurgejobhistory"></a>sp_purge_jobhistory

Remove os registos do histórico para uma tarefa.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumentos
[ **@job_name =** ] 'job_name'  
O nome da tarefa para o qual pretende eliminar os registos do histórico. job_name é nvarchar(128), com uma predefinição de NULL. É necessário especificar job_id ou job_name, mas não não possível especificar ambos.

[ **@job_id =** ] job_id  
 O número de identificação de tarefa do trabalho para os registos a eliminar. job_id é uniqueidentifier, com uma predefinição de NULL. É necessário especificar job_id ou job_name, mas não não possível especificar ambos.

[ **@oldest_date =** ] oldest_date  
 O registo mais antigo para reter no histórico. oldest_date é DATETIME2, com uma predefinição de NULL. Quando oldest_date é especificado, o sp_purge_jobhistory remove apenas os registos que são mais antigos do que o valor especificado.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou grupos de destino de comentários do 1 (falha) fornecem uma forma fácil de uma tarefa numa coleção de bases de dados de destino.

#### <a name="permissions"></a>Permissões
Por predefinição, os membros da função de servidor fixa sysadmin podem executar este procedimento armazenado. Restringem que um usuário apenas poder monitorizar tarefas, pode conceder ao utilizador para fazer parte da seguinte função de base de dados do banco de dados de agente do trabalho especificado quando criar o agente de tarefa:
- jobs_reader

Para obter detalhes sobre as permissões destas funções, consulte a secção de permissão neste documento. Apenas os membros do administrador do sistema podem utilizar este procedimento armazenado para editar os atributos de tarefas que são propriedade de outros utilizadores.

#### <a name="examples"></a>Exemplos
O exemplo seguinte adiciona todas as bases de dados em servidores de Londres e NewYork ao grupo de informações do cliente de manutenção de servidores. Tem de ligar à base de dados de tarefas especificado quando criar o agente de tarefa, neste caso ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Vistas de tarefa

As vistas seguintes estão disponíveis no [base de dados de tarefas](sql-database-job-automation-overview.md#job-database).


|Vista  |Descrição  |
|---------|---------|
|[jobs_executions](#jobsexecutions-view)     |  Mostra o histórico de execução de tarefas.      |
|[jobs](#jobs-view)     |   Mostra todas as tarefas.      |
|[job_versions](#jobversions-view)     |   Mostra todas as versões de tarefa.      |
|[jobsteps](#jobsteps-view)     |     Mostra todos os passos na versão atual de cada tarefa.    |
|[jobstep_versions](#jobstepversions-view)     |     Mostra todos os passos em todas as versões de cada tarefa.    |
|[target_groups](#targetgroups-view)     |      Mostra todos os grupos de destino.   |
|[target_group_members](#targetgroups-view)     |   Mostra todos os membros de todos os grupos de destino.      |


### <a name="jobsexecutions-view"></a>jobs_executions view

[jobs].[jobs_executions]

Mostra o histórico de execução de tarefas.


|Nome da coluna|   Tipo de dados   |Descrição|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  ID exclusivo de uma instância de uma execução de tarefa.
|**job_name**   |nvarchar(128)  |Nome da tarefa.
|**job_id** |uniqueidentifier|  ID exclusivo da tarefa.
|**job_version**    |int    |Versão da tarefa (atualizada automaticamente sempre que a tarefa é modificada).
|**step_id**    |int|   Identificador exclusivo (para esta tarefa) para o passo. NULO indica que esta é a execução de tarefa principal.
|**is_active**| bit |Indica se as informações estão ativos ou inativos. 1 indica tarefas ativas e 0 indica inativos.
|**lifecycle**| nvarchar(50)|Valor que indica o estado da tarefa: "Criado", "Em curso", "Falha", "Com êxito", "Ignorada", 'SucceededWithSkipped'|
|**create_time**|   datetime2(7)|   Data e hora que a criação da tarefa.
|**start_time** |datetime2(7)|  Data e hora a tarefa iniciou a execução. NULO se a tarefa ainda não tiver sido executada.
|**end_time**|  datetime2(7)    |Data e hora a tarefa foi concluída a execução. NULO se a tarefa ainda não tiver sido executada ou não tem ainda concluiu a execução.
|**current_attempts**   |int    |Número de vezes que a etapa foi repetida. Tarefa principal será 0, as execuções de tarefas de subordinado vão ser 1 ou superior com base na política de execução.
|**current_attempt_start_time** |datetime2(7)|  Data e hora a tarefa iniciou a execução. NULO indica que esta é a execução de tarefa principal.
|**last_message**   |nvarchar(max)| Mensagem de histórico do trabalho ou passo. 
|**target_type**|   nvarchar(128)   |Tipo de base de dados de destino ou uma coleção de bases de dados incluindo todas as bases de dados num servidor, todas as bases de dados num conjunto elástico ou uma base de dados. Valores válidos para target_type são 'SqlServer', "SqlElasticPool" ou "SqlDatabase". NULO indica que esta é a execução de tarefa principal.
|**target_id**  |uniqueidentifier|  ID exclusivo do membro do grupo de destino.  NULO indica que esta é a execução de tarefa principal.
|**target_group_name**  |nvarchar(128)  |Nome do grupo de destino. NULO indica que esta é a execução de tarefa principal.
|**target_server_name**|    nvarchar(256)|  Nome do servidor de base de dados SQL contido no grupo de destino. Especificado apenas se target_type é 'SqlServer'. NULO indica que esta é a execução de tarefa principal.
|**target_database_name**   |nvarchar(128)| Nome da base de dados contido no grupo de destino. Especificada apenas quando o target_type é "SqlDatabase". NULO indica que esta é a execução de tarefa principal.


### <a name="jobs-view"></a>na vista de tarefas

[jobs].[jobs]

Mostra todas as tarefas.

|Nome da coluna|   Tipo de dados|  Descrição|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Nome da tarefa.|
|**job_id**|    uniqueidentifier    |ID exclusivo da tarefa.|
|**job_version**    |int    |Versão da tarefa (atualizada automaticamente sempre que a tarefa é modificada).|
|**description**    |nvarchar(512)| Descrição da tarefa. bits ativado indica se a tarefa está ativada ou desabilitada. 1 indica tarefas ativadas, e 0 trabalhos desativados.|
|**schedule_interval_type** |nvarchar(50)   |Valor que indica quando a tarefa está a ser executado: 'Uma vez', 'Minutos', 'Horas', ' dias', 'Semanas', 'Meses'
|**schedule_interval_count**|   int|    Número de períodos de schedule_interval_type seja efetuada entre cada execução da tarefa.|
|**schedule_start_time**    |datetime2(7)|  Data e hora que a tarefa foi a última execução iniciada.|
|**schedule_end_time**| datetime2(7)|   Data e hora que a tarefa foi a última execução foi concluída.|


### <a name="jobversions-view"></a>Ver job_versions

[jobs].[job_versions]

Mostra todas as versões de tarefa.

|Nome da coluna|   Tipo de dados|  Descrição|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Nome da tarefa.|
|**job_id**|    uniqueidentifier    |ID exclusivo da tarefa.|
|**job_version**    |int    |Versão da tarefa (atualizada automaticamente sempre que a tarefa é modificada).|


### <a name="jobsteps-view"></a>ver primeiro

[jobs].[jobsteps]

Mostra todos os passos na versão atual de cada tarefa.

|Nome da coluna    |Tipo de dados| Descrição|
|------|------|-------|
|**job_name**   |nvarchar(128)| Nome da tarefa.|
|**job_id** |uniqueidentifier   |ID exclusivo da tarefa.|
|**job_version**|   int|    Versão da tarefa (atualizada automaticamente sempre que a tarefa é modificada).|
|**step_id**    |int    |Identificador exclusivo (para esta tarefa) para o passo.|
|**step_name**  |nvarchar(128)  |Nome exclusivo (para esta tarefa) para o passo.|
|**command_type**   |nvarchar(50)   |Tipo de comando a executar o passo de tarefa. Para a v1, valor tem de ser igual ao e predefinido para 'TSql'.|
|**command_source** |nvarchar(50)|  Localização do comando. Para a v1, "Inline" é o padrão e apenas aceite o valor.|
|**command**|   nvarchar(max)|  Os comandos a serem executados por tarefas elásticas através de command_type.|
|**credential_name**|   nvarchar(128)   |Nome da credencial com âmbito de base de dados utilizado para a execução da tarefa.|
|**target_group_name**| nvarchar(128)   |Nome do grupo de destino.|
|**target_group_id**|   uniqueidentifier|   ID exclusivo do grupo de destino.|
|**initial_retry_interval_seconds**|    int |O atraso antes da primeira tentativa de repetição. Valor predefinido é 1.|
|**maximum_retry_interval_seconds** |int|   O atraso máximo entre tentativas de repetição. Se o atraso entre tentativas cresceriam maior do que este valor, ele está limitado a este valor em vez disso. Valor predefinido é 120.|
|**retry_interval_backoff_multiplier**  |real|  O multiplicador para aplicar o atraso de repetição se a execução de passo de tarefa de várias tentativas de ativação. Valor predefinido é 2.0.|
|**retry_attempts** |int|   O número de repetições tenta utilizar caso este passo falhe. Padrão de 10, que indica não tentativas de repetição.|
|**step_timeout_seconds**   |int|   A quantidade de tempo em minutos entre tentativas de repetição. A predefinição é 0, o que indica um intervalo de 0 minutos.|
|**output_type**    |nvarchar(11)|  Localização do comando. Na pré-visualização atual, "Inline" é o padrão e apenas aceite o valor.|
|**output_credential_name**|    nvarchar(128)   |Definir o nome das credenciais para ser utilizado para ligar ao servidor de destino para armazenar os resultados.|
|**output_subscription_id**|    uniqueidentifier|   ID exclusivo da subscrição de server\database o destino para os resultados definir entre a execução da consulta.|
|**output_resource_group_name** |nvarchar(128)| Nome do grupo de recursos onde reside o servidor de destino.|
|**output_server_name**|    nvarchar(256)   |Nome do servidor de destino para o conjunto de resultados.|
|**output_database_name**   |nvarchar(128)| Nome da base de dados de destino para o conjunto de resultados.|
|**output_schema_name** |nvarchar(max)| Nome do esquema de destino. Assume a predefinição de dbo, se não for especificado.|
|**output_table_name**| nvarchar(max)|  Nome da tabela para armazenar os resultados definido nos resultados da consulta. Tabela será criada automaticamente com base no esquema de resultados definido se ainda não exista. Esquema tem de corresponder ao esquema do conjunto de resultados.|
|**max_parallelism**|   int|    O número máximo de bases de dados por conjunto elástico que o passo da tarefa será executado cada vez. A predefinição é nulo, significa que nenhum limite. |


### <a name="jobstepversions-view"></a>Ver jobstep_versions

[jobs].[jobstep_versions]

Mostra todos os passos em todas as versões de cada tarefa. O esquema é idêntico à [primeiro](#jobsteps-view).

### <a name="targetgroups-view"></a>target_groups view

[jobs].[target_groups]

Apresenta uma lista de todos os grupos de destino.

|Nome da coluna|Tipo de dados| Descrição|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |O nome do grupo de destino, uma coleção de bases de dados. 
|**target_group_id**    |uniqueidentifier   |ID exclusivo do grupo de destino.

### <a name="targetgroupsmembers-view"></a>target_groups_members view

[jobs].[target_groups_members]

Mostra todos os membros de todos os grupos de destino.

|Nome da coluna|Tipo de dados| Descrição|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128|O nome do grupo de destino, uma coleção de bases de dados. |
|**target_group_id**    |uniqueidentifier   |ID exclusivo do grupo de destino.|
|**membership_type**    |int|   Especifica se o membro do grupo de destino é incluído ou excluído no grupo de destino. Valores válidos para target_group_name são 'Include' ou "Excluir".|
|**target_type**    |nvarchar(128)| Tipo de base de dados de destino ou uma coleção de bases de dados incluindo todas as bases de dados num servidor, todas as bases de dados num conjunto elástico ou uma base de dados. Valores válidos para target_type são 'SqlServer', "SqlElasticPool", "SqlDatabase" ou 'SqlShardMap'.|
|**target_id**  |uniqueidentifier|  ID exclusivo do membro do grupo de destino.|
|**refresh_credential_name**    |nvarchar(128)  |Nome da base de dados no âmbito a credencial utilizada para ligar para o membro do grupo de destino.|
|**subscription_id**    |uniqueidentifier|  ID exclusivo da subscrição.|
|**resource_group_name**    |nvarchar(128)| Nome do grupo de recursos no qual reside o membro do grupo de destino.|
|**server_name**    |nvarchar(128)  |Nome do servidor de base de dados SQL contido no grupo de destino. Especificado apenas se target_type é 'SqlServer'. |
|**database_name**  |nvarchar(128)  |Nome da base de dados contido no grupo de destino. Especificada apenas quando o target_type é "SqlDatabase".|
|**elastic_pool_name**  |nvarchar(128)| Nome do conjunto elástico contido no grupo de destino. Especificada apenas quando o target_type é 'SqlElasticPool'.|
|**shard_map_name** |nvarchar(128)| Nome do mapa de partições horizontais contido no grupo de destino. Especificada apenas quando o target_type é 'SqlShardMap'.|


## <a name="resources"></a>Recursos

 - ![Ícone de ligação do tópico](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "ícone de ligação do tópico") [convenções de sintaxe do Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Passos Seguintes

- [Criar e gerir Tarefas Elásticas com o PowerShell](elastic-jobs-powershell.md)
- [Autorização e as permissões do SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
  
