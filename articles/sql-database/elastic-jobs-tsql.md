---
title: Criar e gerenciar trabalhos de banco de dados elástico do SQL Azure usando Transact-SQL (T-SQL) | Microsoft Docs
description: Execute scripts em vários bancos de dados com o agente de trabalho de banco de dados elástico usando Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: d1123affa79f401b5142af604adbd757bdfb7d73
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641054"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Usar o Transact-SQL (T-SQL) para criar e gerenciar trabalhos de banco de dados elástico

Este artigo fornece muitos cenários de exemplo para começar a trabalhar com trabalhos elásticos usando o T-SQL.

Os exemplos usam os [procedimentos armazenados](#job-stored-procedures) e [exibições](#job-views) disponíveis no [*banco de dados do trabalho*](sql-database-job-automation-overview.md#job-database).

O Transact-SQL (T-SQL) é usado para criar, configurar, executar e gerenciar trabalhos. Não há suporte para a criação do agente de trabalho elástico no T-SQL, portanto, você deve primeiro criar um *agente de trabalho elástico* usando o portal ou o [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Criar uma credencial para a execução do trabalho

A credencial é usada para se conectar aos seus bancos de dados de destino para execução de script. A credencial precisa de permissões apropriadas, nos bancos de dados especificados pelo grupo de destino, para executar o script com êxito. Ao usar um membro do grupo de destino de servidor e/ou pool, é altamente recomendável criar uma credencial mestre para usar para atualizar a credencial antes da expansão do servidor e/ou pool no momento da execução do trabalho. A credencial no escopo do banco de dados é criada no banco de dados do agente de trabalho. A mesma credencial deve ser usada para *criar um logon* e *criar um usuário a partir do logon para conceder as permissões de banco de dados de logon* nos dados de destino.


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

O exemplo a seguir mostra como executar um trabalho em todos os bancos de dados em um servidor.  
Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:


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


## <a name="exclude-an-individual-database"></a>Excluir um banco de dados individual

O exemplo a seguir mostra como executar um trabalho em relação a todos os bancos de dados em um servidor do SQL Database, exceto para o banco de dados chamado *MappingDB*.  
Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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


## <a name="create-a-target-group-pools"></a>Criar um grupo de destino (pools)

O exemplo a seguir mostra como direcionar todos os bancos de dados em um ou mais pools elásticos.  
Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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


## <a name="deploy-new-schema-to-many-databases"></a>Implantar novo esquema em vários bancos de dados

O exemplo a seguir mostra como implantar um novo esquema em todos os bancos de dados.  
Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:


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


## <a name="data-collection-using-built-in-parameters"></a>Coleta de dados usando parâmetros internos

Em muitos cenários de coleta de dados, pode ser útil incluir algumas dessas variáveis de script para ajudar a processar os resultados do trabalho.

- $ (job_name)
- $(job_id)
- $ (job_version)
- $ (step_id)
- $ (step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Por exemplo, para agrupar todos os resultados da mesma execução de trabalho, use o *$ (job_execution_id)* , conforme mostrado no comando a seguir:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Monitorizar o desempenho da base de dados

O exemplo a seguir cria um novo trabalho para coletar dados de desempenho de vários bancos.

Por padrão, o agente de trabalho procurará criar a tabela na qual os resultados retornados serão armazenados. Como resultado, o logon associado à credencial usada para a credencial de saída precisará ter permissões suficientes para realizar isso. Se você quiser criar manualmente a tabela antes do tempo, precisará ter as seguintes propriedades:
1. Colunas com o nome e os tipos de dados corretos para o conjunto de resultados.
2. Coluna adicional para internal_execution_id com o tipo de dados uniqueidentifier.
3. Um índice não clusterizado chamado `IX_<TableName>_Internal_Execution_ID` na coluna internal_execution_id.

Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute os seguintes comandos:

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


## <a name="view-job-definitions"></a>Exibir definições de trabalho

O exemplo a seguir mostra como exibir as definições de trabalho atuais.  
Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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


## <a name="begin-ad-hoc-execution-of-a-job"></a>Iniciar a execução ad hoc de um trabalho

O exemplo a seguir mostra como iniciar um trabalho imediatamente.  
Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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


## <a name="schedule-execution-of-a-job"></a>Agendar a execução de um trabalho

O exemplo a seguir mostra como agendar um trabalho para execução futura.  
Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Monitorar o status de execução do trabalho

O exemplo a seguir mostra como exibir detalhes de status de execução para todos os trabalhos.  
Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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

O exemplo a seguir mostra como cancelar um trabalho.  
Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

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


## <a name="delete-old-job-history"></a>Excluir Histórico de trabalho antigo

O exemplo a seguir mostra como excluir o histórico de trabalho antes de uma data específica.  
Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Excluir um trabalho e todo o seu histórico de trabalho

O exemplo a seguir mostra como excluir um trabalho e todo o histórico de trabalho relacionado.  
Conecte-se ao [*banco de dados de trabalho*](sql-database-job-automation-overview.md#job-database) e execute o seguinte comando:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Procedimentos armazenados de trabalho

Os procedimentos armazenados a seguir estão no [banco de dados de trabalhos](sql-database-job-automation-overview.md#job-database).



|Procedimento armazenado  |Descrição  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Adiciona um novo trabalho.    |
|[sp_update_job](#sp_update_job)    |      Atualiza um trabalho existente.   |
|[sp_delete_job](#sp_delete_job)     |      Exclui um trabalho existente.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Adiciona uma etapa a um trabalho.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Atualiza uma etapa de trabalho.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Exclui uma etapa de trabalho.    |
|[sp_start_job](#sp_start_job)    |  Inicia a execução de um trabalho.       |
|[sp_stop_job](#sp_stop_job)     |     Interrompe a execução de um trabalho.   |
|[sp_add_target_group](#sp_add_target_group)    |     Adiciona um grupo de destino.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Exclui um grupo de destino.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Adiciona um banco de dados ou grupo de bancos de dados a um grupo de destino.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Remove um membro do grupo de destino de um grupo de destino.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Remove os registros de histórico de um trabalho.     |





### <a name="sp_add_job"></a>sp_add_job

Adiciona um novo trabalho. 
  
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

[ **\@job_name =** ] 'job_name'  
O nome do trabalho. O nome deve ser exclusivo e não pode conter a porcentagem (%) espaço. job_name é nvarchar (128), sem padrão.

**[\@Descrição =** ] ' Descrição '  
A descrição do trabalho. a descrição é nvarchar (512), com um padrão de NULL. Se Description for omitido, uma cadeia de caracteres vazia será usada.

**[\@Enabled =** ] habilitado  
Se a agenda do trabalho está habilitada. Habilitado é bit, com um padrão de 0 (desabilitado). Se 0, o trabalho não está habilitado e não é executado de acordo com seu agendamento; no entanto, ele pode ser executado manualmente. Se 1, o trabalho será executado de acordo com sua agenda e também poderá ser executado manualmente.

**[\@schedule_interval_type =** ] schedule_interval_type  
Valor indica quando o trabalho deve ser executado. schedule_interval_type é nvarchar (50), com um padrão de uma vez e pode ser um dos seguintes valores:
- ' Once ',
- ' Minutos ',
- ' Horas ',
- ' Dias ',
- ' Semanas ',
- Meses

**[\@schedule_interval_count =** ] schedule_interval_count  
Número de períodos de schedule_interval_count a ocorrer entre cada execução do trabalho. schedule_interval_count é int, com um padrão de 1. O valor deve ser maior ou igual a 1.

**[\@schedule_start_time =** ] schedule_start_time  
Data em que a execução do trabalho pode começar. schedule_start_time é DATETIME2, com o padrão de 0001-01-01 00:00:00.0000000.

[ **\@schedule_end_time =** ] schedule_end_time  
Data em que a execução do trabalho pode parar. schedule_end_time é DATETIME2, com o padrão de 9999-12-31 11:59:59.0000000. 

**[\@job_id =** ] job_id saída  
O número de identificação do trabalho atribuído ao trabalho, se criado com êxito. job_id é uma variável de saída do tipo uniqueidentifier.

#### <a name="return-code-values"></a>Valores de código de retorno

0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
sp_add_job deve ser executado no banco de dados do agente de trabalho especificado ao criar o agente de trabalho.
Depois que sp_add_job tiver sido executado para adicionar um trabalho, sp_add_jobstep poderá ser usado para adicionar etapas que executam as atividades do trabalho. O número de versão inicial do trabalho é 0, que será incrementado para 1 quando a primeira etapa for adicionada.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:

- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.

### <a name="sp_update_job"></a>sp_update_job

Atualiza um trabalho existente.

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
[ **\@job_name =** ] 'job_name'  
O nome do trabalho a ser atualizado. job_name é nvarchar (128).

**[\@new_name =** ] ' new_name '  
O novo nome do trabalho. new_name é nvarchar (128).

**[\@Descrição =** ] ' Descrição '  
A descrição do trabalho. a descrição é nvarchar (512).

**[\@Enabled =** ] habilitado  
Especifica se a agenda do trabalho está habilitada (1) ou não habilitada (0). Habilitado é bit.

[ **\@schedule_interval_type=** ] schedule_interval_type  
Valor indica quando o trabalho deve ser executado. schedule_interval_type é nvarchar (50) e pode ser um dos seguintes valores:

- ' Once ',
- ' Minutos ',
- ' Horas ',
- ' Dias ',
- ' Semanas ',
- Meses

**[\@schedule_interval_count =** ] schedule_interval_count  
Número de períodos de schedule_interval_count a ocorrer entre cada execução do trabalho. schedule_interval_count é int, com um padrão de 1. O valor deve ser maior ou igual a 1.

**[\@schedule_start_time =** ] schedule_start_time  
Data em que a execução do trabalho pode começar. schedule_start_time é DATETIME2, com o padrão de 0001-01-01 00:00:00.0000000.

[ **\@schedule_end_time=** ] schedule_end_time  
Data em que a execução do trabalho pode parar. schedule_end_time é DATETIME2, com o padrão de 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Depois que sp_add_job tiver sido executado para adicionar um trabalho, sp_add_jobstep poderá ser usado para adicionar etapas que executam as atividades do trabalho. O número de versão inicial do trabalho é 0, que será incrementado para 1 quando a primeira etapa for adicionada.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.



### <a name="sp_delete_job"></a>sp_delete_job

Exclui um trabalho existente.

#### <a name="syntax"></a>Sintaxe

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumentos
[ **\@job_name =** ] 'job_name'  
O nome do trabalho a ser excluído. job_name é nvarchar (128).

**[\@Force =** ] forçar  
Especifica se deve ser excluído se o trabalho tiver alguma execução em andamento e cancelar todas as execuções em andamento (1) ou falhar se alguma execução de trabalho estiver em andamento (0). Force é bit.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
O histórico de trabalhos é excluído automaticamente quando um trabalho é excluído.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.



### <a name="sp_add_jobstep"></a>sp_add_jobstep

Adiciona uma etapa a um trabalho.

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

[ **\@job_name =** ] 'job_name'  
O nome do trabalho ao qual adicionar a etapa. job_name é nvarchar (128).

**[\@step_id =** ] step_id  
O número de identificação da sequência para a etapa de trabalho. Os números de identificação da etapa começam em 1 e são incrementados sem lacunas. Se uma etapa existente já tiver essa ID, essa etapa e todas as etapas a seguir terão suas IDs incrementadas para que essa nova etapa possa ser inserida na sequência. Se não for especificado, o step_id será atribuído automaticamente ao último na sequência de etapas. step_id é um int.

**[\@step_name =** ] step_name  
O nome da etapa. Deve ser especificado, exceto para a primeira etapa de um trabalho que (por conveniência) tem um nome padrão de ' JobStep '. step_name é nvarchar (128).

[ **\@command_type =** ] 'command_type'  
O tipo de comando que é executado por este JobStep. command_type é nvarchar (50), com um valor padrão de TSql, o que significa que o valor @command_type do parâmetro é um script T-SQL.

Se especificado, o valor deve ser TSql.

[ **\@command_source =** ] 'command_source'  
O tipo de local onde o comando é armazenado. command_source é nvarchar (50), com um valor padrão de inline, o que significa que o valor @command_source do parâmetro é o texto literal do comando.

Se especificado, o valor deve ser embutido.

[ **\@command =** ] 'command'  
O comando deve ser um script T-SQL válido e, em seguida, é executado por essa etapa de trabalho. o comando é nvarchar (max), com um padrão de NULL.

**[\@credential_name =** ] ' credential_name '  
O nome da credencial no escopo do banco de dados armazenada neste banco de dados de controle de trabalho que é usada para se conectar a cada um dos bancos de dados de destino no grupo de destino quando esta etapa é executada. credential_name é nvarchar (128).

[ **\@target_group_name =** ] 'target-group_name'  
O nome do grupo de destino que contém os bancos de dados de destino em que a etapa de trabalho será executada. target_group_name é nvarchar (128).

**[\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
O atraso antes da primeira tentativa de repetição, se a etapa de trabalho falhar na tentativa de execução inicial. initial_retry_interval_seconds é int, com o valor padrão de 1.

**[\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
O atraso máximo entre as tentativas de repetição. Se o atraso entre as repetições aumentar maior que esse valor, ele será limitado a esse valor em vez disso. maximum_retry_interval_seconds é int, com o valor padrão de 120.

**[\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
O multiplicador a ser aplicado ao intervalo de repetição se várias tentativas de execução de etapa de trabalho falharem. Por exemplo, se a primeira nova tentativa tiver um atraso de 5 segundos e o multiplicador de retirada for 2,0, a segunda repetição terá um atraso de 10 segundos e a terceira repetição terá um atraso de 20 segundos. retry_interval_backoff_multiplier é real, com o valor padrão de 2,0.

[ **\@retry_attempts =** ] retry_attempts  
O número de vezes para repetir a execução se a tentativa inicial falhar. Por exemplo, se o valor de retry_attempts for 10, haverá 1 tentativa inicial e 10 tentativas de repetição, dando um total de 11 tentativas. Se a tentativa de repetição final falhar, a execução do trabalho será encerrada com um ciclo de vida de falha. retry_attempts é int, com o valor padrão de 10.

**[\@step_timeout_seconds =** ] step_timeout_seconds  
A quantidade máxima de tempo permitida para a etapa ser executada. Se esse tempo for excedido, a execução do trabalho será encerrada com um ciclo de vida de TimedOut. step_timeout_seconds é int, com o valor padrão de 43.200 segundos (12 horas).

[ **\@output_type =** ] 'output_type'  
Se não for NULL, o tipo de destino para o qual o primeiro conjunto de resultados do comando será gravado. output_type é nvarchar (50), com um padrão de NULL.

Se especificado, o valor deve ser SQLDatabase.

[ **\@output_credential_name =** ] 'output_credential_name'  
Se não for NULL, o nome da credencial no escopo do banco de dados usada para se conectar ao banco de dados de destino de saída. Deve ser especificado se output_type for igual a SQLDatabase. output_credential_name é nvarchar (128), com um valor padrão de NULL.

[ **\@output_subscription_id =** ] 'output_subscription_id'  
Precisa de uma descrição.

[ **\@output_resource_group_name =** ] 'output_resource_group_name'  
Precisa de uma descrição.

[ **\@output_server_name =** ] 'output_server_name'  
Se não for NULL, o nome DNS totalmente qualificado do servidor que contém o banco de dados de destino de saída. Deve ser especificado se output_type for igual a SQLDatabase. output_server_name é nvarchar (256), com um padrão de NULL.

**[\@output_database_name =** ] ' output_database_name '  
Se não for NULL, o nome do banco de dados que contém a tabela de destino de saída. Deve ser especificado se output_type for igual a SQLDatabase. output_database_name é nvarchar (128), com um padrão de NULL.

[ **\@output_schema_name =** ] 'output_schema_name'  
Se não for NULL, o nome do esquema SQL que contém a tabela de destino de saída. Se output_type for igual a SQLDatabase, o valor padrão será dbo. output_schema_name é nvarchar (128).

[ **\@output_table_name =** ] 'output_table_name'  
Se não for NULL, o nome da tabela para a qual o primeiro conjunto de resultados do comando será gravado. Se a tabela ainda não existir, ela será criada com base no esquema do conjunto de resultados de retorno. Deve ser especificado se output_type for igual a SQLDatabase. output_table_name é nvarchar (128), com um valor padrão de NULL.

**[\@job_version =** ] saída de job_version  
O parâmetro de saída que será atribuído ao novo número de versão do trabalho. job_version é int.

**[\@max_parallelism =** ] saída de max_parallelism  
O nível máximo de paralelismo por pool elástico. Se definido, a etapa de trabalho será restrita para ser executada somente em um máximo de vários bancos de dados por pool elástico. Isso se aplica a cada pool elástico que está diretamente incluído no grupo de destino ou está dentro de um servidor que está incluído no grupo de destino. max_parallelism é int.


#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Quando o sp_add_jobstep tiver sucesso, o número da versão atual do trabalho será incrementado. Na próxima vez em que o trabalho for executado, a nova versão será usada. Se o trabalho estiver em execução no momento, essa execução não conterá a nova etapa.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:  

- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.



### <a name="sp_update_jobstep"></a>sp_update_jobstep

Atualiza uma etapa de trabalho.

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
[ **\@job_name =** ] 'job_name'  
O nome do trabalho ao qual a etapa pertence. job_name é nvarchar (128).

**[\@step_id =** ] step_id  
O número de identificação da etapa de trabalho a ser modificada. Step_id ou step_name deve ser especificado. step_id é um int.

**[\@step_name =** ] ' step_name '  
O nome da etapa a ser modificada. Step_id ou step_name deve ser especificado. step_name é nvarchar (128).

**[\@new_id =** ] new_id  
O novo número de identificação de sequência para a etapa de trabalho. Os números de identificação da etapa começam em 1 e são incrementados sem lacunas. Se uma etapa for reordenada, outras etapas serão automaticamente renumeradas.

**[\@new_name =** ] ' new_name '  
O novo nome da etapa. new_name é nvarchar (128).

[ **\@command_type =** ] 'command_type'  
O tipo de comando que é executado por este JobStep. command_type é nvarchar (50), com um valor padrão de TSql, o que significa que o valor @command_type do parâmetro é um script T-SQL.

Se especificado, o valor deve ser TSql.

[ **\@command_source =** ] 'command_source'  
O tipo de local onde o comando é armazenado. command_source é nvarchar (50), com um valor padrão de inline, o que significa que o valor @command_source do parâmetro é o texto literal do comando.

Se especificado, o valor deve ser embutido.

[ **\@command =** ] 'command'  
Os comandos devem ser um script T-SQL válido e, em seguida, são executados por essa etapa de trabalho. o comando é nvarchar (max), com um padrão de NULL.

**[\@credential_name =** ] ' credential_name '  
O nome da credencial no escopo do banco de dados armazenada neste banco de dados de controle de trabalho que é usada para se conectar a cada um dos bancos de dados de destino no grupo de destino quando esta etapa é executada. credential_name é nvarchar (128).

[ **\@target_group_name =** ] 'target-group_name'  
O nome do grupo de destino que contém os bancos de dados de destino em que a etapa de trabalho será executada. target_group_name é nvarchar (128).

**[\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
O atraso antes da primeira tentativa de repetição, se a etapa de trabalho falhar na tentativa de execução inicial. initial_retry_interval_seconds é int, com o valor padrão de 1.

**[\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
O atraso máximo entre as tentativas de repetição. Se o atraso entre as repetições aumentar maior que esse valor, ele será limitado a esse valor em vez disso. maximum_retry_interval_seconds é int, com o valor padrão de 120.

**[\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
O multiplicador a ser aplicado ao intervalo de repetição se várias tentativas de execução de etapa de trabalho falharem. Por exemplo, se a primeira nova tentativa tiver um atraso de 5 segundos e o multiplicador de retirada for 2,0, a segunda repetição terá um atraso de 10 segundos e a terceira repetição terá um atraso de 20 segundos. retry_interval_backoff_multiplier é real, com o valor padrão de 2,0.

[ **\@retry_attempts =** ] retry_attempts  
O número de vezes para repetir a execução se a tentativa inicial falhar. Por exemplo, se o valor de retry_attempts for 10, haverá 1 tentativa inicial e 10 tentativas de repetição, dando um total de 11 tentativas. Se a tentativa de repetição final falhar, a execução do trabalho será encerrada com um ciclo de vida de falha. retry_attempts é int, com o valor padrão de 10.

**[\@step_timeout_seconds =** ] step_timeout_seconds  
A quantidade máxima de tempo permitida para a etapa ser executada. Se esse tempo for excedido, a execução do trabalho será encerrada com um ciclo de vida de TimedOut. step_timeout_seconds é int, com o valor padrão de 43.200 segundos (12 horas).

[ **\@output_type =** ] 'output_type'  
Se não for NULL, o tipo de destino para o qual o primeiro conjunto de resultados do comando será gravado. Para redefinir o valor de output_type de volta para NULL, defina o valor desse parâmetro como ' ' (cadeia de caracteres vazia). output_type é nvarchar (50), com um padrão de NULL.

Se especificado, o valor deve ser SQLDatabase.

[ **\@output_credential_name =** ] 'output_credential_name'  
Se não for NULL, o nome da credencial no escopo do banco de dados usada para se conectar ao banco de dados de destino de saída. Deve ser especificado se output_type for igual a SQLDatabase. Para redefinir o valor de output_credential_name de volta para NULL, defina o valor desse parâmetro como ' ' (cadeia de caracteres vazia). output_credential_name é nvarchar (128), com um valor padrão de NULL.

[ **\@output_server_name =** ] 'output_server_name'  
Se não for NULL, o nome DNS totalmente qualificado do servidor que contém o banco de dados de destino de saída. Deve ser especificado se output_type for igual a SQLDatabase. Para redefinir o valor de output_server_name de volta para NULL, defina o valor desse parâmetro como ' ' (cadeia de caracteres vazia). output_server_name é nvarchar (256), com um padrão de NULL.

**[\@output_database_name =** ] ' output_database_name '  
Se não for NULL, o nome do banco de dados que contém a tabela de destino de saída. Deve ser especificado se output_type for igual a SQLDatabase. Para redefinir o valor de output_database_name de volta para NULL, defina o valor desse parâmetro como ' ' (cadeia de caracteres vazia). output_database_name é nvarchar (128), com um padrão de NULL.

[ **\@output_schema_name =** ] 'output_schema_name'  
Se não for NULL, o nome do esquema SQL que contém a tabela de destino de saída. Se output_type for igual a SQLDatabase, o valor padrão será dbo. Para redefinir o valor de output_schema_name de volta para NULL, defina o valor desse parâmetro como ' ' (cadeia de caracteres vazia). output_schema_name é nvarchar (128).

[ **\@output_table_name =** ] 'output_table_name'  
Se não for NULL, o nome da tabela para a qual o primeiro conjunto de resultados do comando será gravado. Se a tabela ainda não existir, ela será criada com base no esquema do conjunto de resultados de retorno. Deve ser especificado se output_type for igual a SQLDatabase. Para redefinir o valor de output_server_name de volta para NULL, defina o valor desse parâmetro como ' ' (cadeia de caracteres vazia). output_table_name é nvarchar (128), com um valor padrão de NULL.

**[\@job_version =** ] saída de job_version  
O parâmetro de saída que será atribuído ao novo número de versão do trabalho. job_version é int.

**[\@max_parallelism =** ] saída de max_parallelism  
O nível máximo de paralelismo por pool elástico. Se definido, a etapa de trabalho será restrita para ser executada somente em um máximo de vários bancos de dados por pool elástico. Isso se aplica a cada pool elástico que está diretamente incluído no grupo de destino ou está dentro de um servidor que está incluído no grupo de destino. Para redefinir o valor de max_parallelism de volta para NULL, defina o valor desse parâmetro como-1. max_parallelism é int.


#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
As execuções em andamento do trabalho não serão afetadas. Quando o sp_update_jobstep é executado com sucesso, o número de versão do trabalho é incrementado. Na próxima vez em que o trabalho for executado, a nova versão será usada.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:

- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários




### <a name="sp_delete_jobstep"></a>sp_delete_jobstep

Remove uma etapa de trabalho de um trabalho.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumentos
[ **\@job_name =** ] 'job_name'  
O nome do trabalho do qual a etapa será removida. job_name é nvarchar (128), sem padrão.

**[\@step_id =** ] step_id  
O número de identificação da etapa de trabalho a ser excluída. Step_id ou step_name deve ser especificado. step_id é um int.

**[\@step_name =** ] ' step_name '  
O nome da etapa a ser excluída. Step_id ou step_name deve ser especificado. step_name é nvarchar (128).

**[\@job_version =** ] saída de job_version  
O parâmetro de saída que será atribuído ao novo número de versão do trabalho. job_version é int.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
As execuções em andamento do trabalho não serão afetadas. Quando o sp_update_jobstep é executado com sucesso, o número de versão do trabalho é incrementado. Na próxima vez em que o trabalho for executado, a nova versão será usada.

As outras etapas de trabalho serão automaticamente renumeradas para preencher a lacuna à esquerda pela etapa de trabalho excluída.
 
#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.






### <a name="sp_start_job"></a>sp_start_job

Inicia a execução de um trabalho.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumentos
[ **\@job_name =** ] 'job_name'  
O nome do trabalho do qual a etapa será removida. job_name é nvarchar (128), sem padrão.

**[\@job_execution_id =** ] saída de job_execution_id  
O parâmetro de saída que será atribuído à ID da execução do trabalho. job_version é uniqueidentifier.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Nenhum.
 
#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.

### <a name="sp_stop_job"></a>sp_stop_job

Interrompe a execução de um trabalho.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumentos
[ **\@job_execution_id =** ] job_execution_id  
O número de identificação da execução do trabalho a ser interrompida. job_execution_id é uniqueidentifier, com o padrão NULL.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Nenhum.
 
#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.


### <a name="sp_add_target_group"></a>sp_add_target_group

Adiciona um grupo de destino.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumentos
[ **\@target_group_name =** ] 'target_group_name'  
O nome do grupo de destino a ser criado. target_group_name é nvarchar (128), sem padrão.

**[\@target_group_id =** ] target_group_id saída o número de identificação do grupo de destino atribuído ao trabalho, se criado com êxito. target_group_id é uma variável de saída do tipo uniqueidentifier, com um padrão de NULL.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Os grupos de destino fornecem uma maneira fácil de direcionar um trabalho em uma coleção de bancos de dados.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.

### <a name="sp_delete_target_group"></a>sp_delete_target_group

Exclui um grupo de destino.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumentos
[ **\@target_group_name =** ] 'target_group_name'  
O nome do grupo de destino a ser excluído. target_group_name é nvarchar (128), sem padrão.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Nenhum.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.

### <a name="sp_add_target_group_member"></a>sp_add_target_group_member

Adiciona um banco de dados ou grupo de bancos de dados a um grupo de destino.

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
[ **\@target_group_name =** ] 'target_group_name'  
O nome do grupo de destino ao qual o membro será adicionado. target_group_name é nvarchar (128), sem padrão.

**[\@membership_type =** ] ' membership_type '  
Especifica se o membro do grupo de destino será incluído ou excluído. target_group_name é nvarchar (128), com o padrão ' include '. Os valores válidos para target_group_name são ' include ' ou ' Exclude '.

[ **\@target_type =** ] 'target_type'  
O tipo de banco de dados de destino ou de uma coleção de dados, incluindo todos os bancos de dados em um servidor, todos os bancos de dados em um pool elástico, todos os bancos de dados em um mapa de fragmentos ou um banco de dados individual. target_type é nvarchar (128), sem padrão. Os valores válidos para target_type são ' SqlServer ', ' SqlElasticPool ', ' SQLDatabase ' ou ' SqlShardMap '. 

**[\@refresh_credential_name =** ] ' refresh_credential_name '  
O nome do servidor do banco de dados SQL. refresh_credential_name é nvarchar (128), sem padrão.

**[\@server_name =** ] ' nome_servidor '  
O nome do servidor do banco de dados SQL que deve ser adicionado ao grupo de destino especificado. server_name deve ser especificado quando target_type for ' SqlServer '. server_name é nvarchar (128), sem padrão.

[ **\@database_name =** ] 'database_name'  
O nome do banco de dados que deve ser adicionado ao grupo de destino especificado. database_name deve ser especificado quando target_type é ' SQLDatabase '. database_name é nvarchar (128), sem padrão.

**[\@elastic_pool_name =** ] ' elastic_pool_name '  
O nome do pool elástico que deve ser adicionado ao grupo de destino especificado. elastic_pool_name deve ser especificado quando target_type é ' SqlElasticPool '. elastic_pool_name é nvarchar (128), sem padrão.

**[\@shard_map_name =** ] ' shard_map_name '  
O nome do pool de mapas de fragmentos que deve ser adicionado ao grupo de destino especificado. elastic_pool_name deve ser especificado quando target_type é ' SqlSqlShardMap '. shard_map_name é nvarchar (128), sem padrão.

**[\@target_id =** ] saída de target_group_id  
O número de identificação de destino atribuído ao membro do grupo de destino, se criado adicionado ao grupo de destino. target_id é uma variável de saída do tipo uniqueidentifier, com um padrão de NULL.
Valores de código de retorno 0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Um trabalho é executado em todos os bancos de dados individuais em um servidor de banco de dados SQL ou em um pool elástico no momento da execução, quando um servidor de banco de dados SQL ou um pool elástico é incluído no grupo de destino.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.

#### <a name="examples"></a>Exemplos
O exemplo a seguir adiciona todos os bancos de dados nos servidores London e NewYork aos servidores do grupo que mantêm as informações do cliente. Você deve se conectar ao banco de dados de trabalhos especificado ao criar o agente de trabalho, neste caso, ElasticJobs.

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

### <a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Remove um membro do grupo de destino de um grupo de destino.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Arguments @target_group_name [=] ' target_group_name '  
O nome do grupo de destino do qual remover o membro do grupo de destino. target_group_name é nvarchar (128), sem padrão.

[ @target_id = ] target_id  
 O número de identificação de destino atribuído ao membro do grupo de destino a ser removido. target_id é um uniqueidentifier, com um padrão de NULL.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha)

#### <a name="remarks"></a>Observações
Os grupos de destino fornecem uma maneira fácil de direcionar um trabalho em uma coleção de bancos de dados.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.

#### <a name="examples"></a>Exemplos
O exemplo a seguir remove o servidor London dos servidores de grupo que mantêm as informações do cliente. Você deve se conectar ao banco de dados de trabalhos especificado ao criar o agente de trabalho, neste caso, ElasticJobs.

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

### <a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Remove os registros de histórico de um trabalho.

#### <a name="syntax"></a>Sintaxe


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumentos
[ **\@job_name =** ] 'job_name'  
O nome do trabalho para o qual os registros de histórico são excluídos. job_name é nvarchar (128), com um padrão de NULL. Job_id ou job_name devem ser especificados, mas ambos não podem ser especificados.

[ **\@job_id =** ] job_id  
 O número de identificação do trabalho para os registros a serem excluídos. job_id é uniqueidentifier, com um padrão de NULL. Job_id ou job_name devem ser especificados, mas ambos não podem ser especificados.

[ **\@oldest_date =** ] oldest_date  
 O registro mais antigo a ser mantido no histórico. oldest_date é DATETIME2, com um padrão de NULL. Quando oldest_date é especificado, o sp_purge_jobhistory remove somente os registros que são mais antigos que o valor especificado.

#### <a name="return-code-values"></a>Valores de código de retorno
0 (êxito) ou 1 (falha) comentários grupos de destino fornecem uma maneira fácil de direcionar um trabalho em uma coleção de bancos de dados.

#### <a name="permissions"></a>Permissões
Por padrão, os membros da função de servidor fixa sysadmin podem executar esse procedimento armazenado. Eles restringem um usuário a apenas poder monitorar trabalhos, você pode conceder ao usuário fazer parte da seguinte função de banco de dados no banco de dados do agente de trabalho especificado ao criar o agente de trabalho:
- jobs_reader

Para obter detalhes sobre as permissões dessas funções, consulte a seção permissão neste documento. Somente os membros do sysadmin podem usar esse procedimento armazenado para editar os atributos de trabalhos que pertencem a outros usuários.

#### <a name="examples"></a>Exemplos
O exemplo a seguir adiciona todos os bancos de dados nos servidores London e NewYork aos servidores do grupo que mantêm as informações do cliente. Você deve se conectar ao banco de dados de trabalhos especificado ao criar o agente de trabalho, neste caso, ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Exibições de trabalho

As exibições a seguir estão disponíveis no [banco de dados de trabalhos](sql-database-job-automation-overview.md#job-database).


|Vista  |Descrição  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Mostra o histórico de execução do trabalho.      |
|[jobs](#jobs-view)     |   Mostra todos os trabalhos.      |
|[job_versions](#job_versions-view)     |   Mostra todas as versões de trabalho.      |
|[JobSteps](#jobsteps-view)     |     Mostra todas as etapas na versão atual de cada trabalho.    |
|[jobstep_versions](#jobstep_versions-view)     |     Mostra todas as etapas em todas as versões de cada trabalho.    |
|[target_groups](#target_groups-view)     |      Mostra todos os grupos de destino.   |
|[target_group_members](#target_groups_members-view)     |   Mostra todos os membros de todos os grupos de destino.      |


### <a name="job_executions-view"></a>exibição de job_executions

[trabalhos]. [job_executions]

Mostra o histórico de execução do trabalho.


|Nome da coluna|   Tipo de dados   |Descrição|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  ID exclusiva de uma instância de uma execução de trabalho.
|**job_name**   |nvarchar(128)  |Nome do trabalho.
|**job_id** |uniqueidentifier|  ID exclusiva do trabalho.
|**job_version**    |int    |Versão do trabalho (atualizada automaticamente cada vez que o trabalho é modificado).
|**step_id**    |int|   Identificador exclusivo (para este trabalho) para a etapa. NULL indica que esta é a execução do trabalho pai.
|**is_active**| bit |Indica se as informações estão ativas ou inativas. 1 indica trabalhos ativos e 0 indica inativo.
|**lifecycle**| nvarchar(50)|Valor que indica o status do trabalho: ' criado ', ' em andamento ', ' falha ', ' êxito ', ' ignorado ', ' SucceededWithSkipped '|
|**create_time**|   datetime2 (7)|   Data e hora em que o trabalho foi criado.
|**start_time** |datetime2 (7)|  Data e hora em que o trabalho iniciou a execução. NULL se o trabalho ainda não tiver sido executado.
|**end_time**|  datetime2 (7)    |Data e hora em que o trabalho concluiu a execução. NULL se o trabalho ainda não tiver sido executado ou ainda não tiver concluído a execução.
|**current_attempts**   |int    |Número de vezes que a etapa foi repetida. O trabalho pai será 0, as execuções de trabalho filho serão de 1 ou maior com base na política de execução.
|**current_attempt_start_time** |datetime2 (7)|  Data e hora em que o trabalho iniciou a execução. NULL indica que esta é a execução do trabalho pai.
|**last_message**   |nvarchar (Max)| Mensagem de histórico de trabalho ou etapa. 
|**target_type**|   nvarchar(128)   |Tipo de banco de dados de destino ou coleção de bancos de dados, incluindo todos os bancos de dados em um servidor, todos os bancos de dados em um pool elástico ou um banco de dados. Os valores válidos para target_type são ' SqlServer ', ' SqlElasticPool ' ou ' SQLDatabase '. NULL indica que esta é a execução do trabalho pai.
|**target_id**  |uniqueidentifier|  ID exclusiva do membro do grupo de destino.  NULL indica que esta é a execução do trabalho pai.
|**target_group_name**  |nvarchar(128)  |Nome do grupo de destino. NULL indica que esta é a execução do trabalho pai.
|**target_server_name**|    nvarchar(256)|  Nome do servidor de banco de dados SQL contido no grupo de destino. Especificado somente se target_type for ' SqlServer '. NULL indica que esta é a execução do trabalho pai.
|**target_database_name**   |nvarchar(128)| Nome do banco de dados contido no grupo de destino. Especificado somente quando target_type é ' SQLDatabase '. NULL indica que esta é a execução do trabalho pai.


### <a name="jobs-view"></a>exibição de trabalhos

[jobs].[jobs]

Mostra todos os trabalhos.

|Nome da coluna|   Tipo de dados|  Descrição|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Nome do trabalho.|
|**job_id**|    uniqueidentifier    |ID exclusiva do trabalho.|
|**job_version**    |int    |Versão do trabalho (atualizada automaticamente cada vez que o trabalho é modificado).|
|**description**    |nvarchar(512)| Descrição do trabalho. bit habilitado indica se o trabalho está habilitado ou desabilitado. 1 indica trabalhos habilitados e 0 indica trabalhos desabilitados.|
|**schedule_interval_type** |nvarchar(50)   |Valor que indica quando o trabalho deve ser executado: "Once", "Minutes", "hours", "Days", "Weeks", "months"
|**schedule_interval_count**|   int|    Número de períodos de schedule_interval_type a ocorrer entre cada execução do trabalho.|
|**schedule_start_time**    |datetime2 (7)|  Data e hora da última execução do trabalho foi iniciado.|
|**schedule_end_time**| datetime2 (7)|   Data e hora da última execução do trabalho concluída.|


### <a name="job_versions-view"></a>exibição de job_versions

[trabalhos]. [job_versions]

Mostra todas as versões de trabalho.

|Nome da coluna|   Tipo de dados|  Descrição|
|------|------|-------|
|**job_name**|  nvarchar(128)   |Nome do trabalho.|
|**job_id**|    uniqueidentifier    |ID exclusiva do trabalho.|
|**job_version**    |int    |Versão do trabalho (atualizada automaticamente cada vez que o trabalho é modificado).|


### <a name="jobsteps-view"></a>exibição de JobSteps

[trabalhos]. JobSteps

Mostra todas as etapas na versão atual de cada trabalho.

|Nome da coluna    |Tipo de dados| Descrição|
|------|------|-------|
|**job_name**   |nvarchar(128)| Nome do trabalho.|
|**job_id** |uniqueidentifier   |ID exclusiva do trabalho.|
|**job_version**|   int|    Versão do trabalho (atualizada automaticamente cada vez que o trabalho é modificado).|
|**step_id**    |int    |Identificador exclusivo (para este trabalho) para a etapa.|
|**step_name**  |nvarchar(128)  |Nome exclusivo (para este trabalho) para a etapa.|
|**command_type**   |nvarchar(50)   |Tipo de comando a ser executado na etapa de trabalho. Para v1, o valor deve ser igual a e o padrão é ' TSql '.|
|**command_source** |nvarchar(50)|  Local do comando. Para v1, ' inline ' é o padrão e apenas o valor aceito.|
|**command**|   nvarchar (Max)|  Os comandos a serem executados por trabalhos elásticos por meio de command_type.|
|**credential_name**|   nvarchar(128)   |Nome da credencial no escopo do banco de dados usada para executar o trabalho.|
|**target_group_name**| nvarchar(128)   |Nome do grupo de destino.|
|**target_group_id**|   uniqueidentifier|   ID exclusiva do grupo de destino.|
|**initial_retry_interval_seconds**|    int |O atraso antes da primeira tentativa de repetição. O valor padrão é 1.|
|**maximum_retry_interval_seconds** |int|   O atraso máximo entre as tentativas de repetição. Se o atraso entre as repetições aumentar maior que esse valor, ele será limitado a esse valor em vez disso. O valor padrão é 120.|
|**retry_interval_backoff_multiplier**  |real|  O multiplicador a ser aplicado ao intervalo de repetição se várias tentativas de execução de etapa de trabalho falharem. O valor padrão é 2,0.|
|**retry_attempts** |int|   O número de tentativas de repetição a serem usadas se essa etapa falhar. Padrão de 10, que indica nenhuma tentativa de repetição.|
|**step_timeout_seconds**   |int|   A quantidade de tempo em minutos entre as tentativas de repetição. O padrão é 0, que indica um intervalo de 0 minuto.|
|**output_type**    |nvarchar(11)|  Local do comando. Na visualização atual, ' inline ' é o padrão e apenas o valor aceito.|
|**output_credential_name**|    nvarchar(128)   |Nome das credenciais a serem usadas para se conectar ao servidor de destino para armazenar o conjunto de resultados.|
|**output_subscription_id**|    uniqueidentifier|   ID exclusiva da assinatura do SERVER\Database de destino para o conjunto de resultados da execução da consulta.|
|**output_resource_group_name** |nvarchar(128)| Nome do grupo de recursos no qual o servidor de destino reside.|
|**output_server_name**|    nvarchar(256)   |Nome do servidor de destino para o conjunto de resultados.|
|**output_database_name**   |nvarchar(128)| Nome do banco de dados de destino para o conjunto de resultados.|
|**output_schema_name** |nvarchar (Max)| Nome do esquema de destino. O padrão é dbo, se não for especificado.|
|**output_table_name**| nvarchar (Max)|  Nome da tabela para armazenar o conjunto de resultados dos resultados da consulta. A tabela será criada automaticamente com base no esquema do conjunto de resultados, caso ele ainda não exista. O esquema deve corresponder ao esquema do conjunto de resultados.|
|**max_parallelism**|   int|    O número máximo de bancos de dados por pool elástico em que a etapa de trabalho será executada por vez. O padrão é NULL, o que significa que não há limite. |


### <a name="jobstep_versions-view"></a>exibição de jobstep_versions

[trabalhos]. [jobstep_versions]

Mostra todas as etapas em todas as versões de cada trabalho. O esquema é idêntico a [JobSteps](#jobsteps-view).

### <a name="target_groups-view"></a>target_groups view

[jobs].[target_groups]

Lista todos os grupos de destino.

|Nome da coluna|Tipo de dados| Descrição|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |O nome do grupo de destino, uma coleção de bancos de dados. 
|**target_group_id**    |uniqueidentifier   |ID exclusiva do grupo de destino.

### <a name="target_groups_members-view"></a>target_groups_members view

[jobs].[target_groups_members]

Mostra todos os membros de todos os grupos de destino.

|Nome da coluna|Tipo de dados| Descrição|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128|O nome do grupo de destino, uma coleção de bancos de dados. |
|**target_group_id**    |uniqueidentifier   |ID exclusiva do grupo de destino.|
|**membership_type**    |int|   Especifica se o membro do grupo de destino está incluído ou excluído no grupo de destino. Os valores válidos para target_group_name são ' include ' ou ' Exclude '.|
|**target_type**    |nvarchar(128)| Tipo de banco de dados de destino ou coleção de bancos de dados, incluindo todos os bancos de dados em um servidor, todos os bancos de dados em um pool elástico ou um banco de dados. Os valores válidos para target_type são ' SqlServer ', ' SqlElasticPool ', ' SQLDatabase ' ou ' SqlShardMap '.|
|**target_id**  |uniqueidentifier|  ID exclusiva do membro do grupo de destino.|
|**refresh_credential_name**    |nvarchar(128)  |Nome da credencial no escopo do banco de dados usada para se conectar ao membro do grupo de destino.|
|**subscription_id**    |uniqueidentifier|  ID exclusiva da assinatura.|
|**resource_group_name**    |nvarchar(128)| Nome do grupo de recursos no qual o membro do grupo de destino reside.|
|**server_name**    |nvarchar(128)  |Nome do servidor de banco de dados SQL contido no grupo de destino. Especificado somente se target_type for ' SqlServer '. |
|**database_name**  |nvarchar(128)  |Nome do banco de dados contido no grupo de destino. Especificado somente quando target_type é ' SQLDatabase '.|
|**elastic_pool_name**  |nvarchar(128)| Nome do pool elástico contido no grupo de destino. Especificado somente quando target_type é ' SqlElasticPool '.|
|**shard_map_name** |nvarchar(128)| Nome do mapa de fragmentos contido no grupo de destino. Especificado somente quando target_type é ' SqlShardMap '.|


## <a name="resources"></a>Recursos

 - ![Ícone de link do tópico](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "Ícone de link do tópico") [Convenções de sintaxe Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Passos Seguintes

- [Criar e gerir Tarefas Elásticas com o PowerShell](elastic-jobs-powershell.md)
- [Autorização e permissões SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
