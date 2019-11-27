---
title: Criar um agente de Tarefa Elástica com o PowerShell
description: Saiba como criar um agente de Tarefa Elástica com o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: 74a72df9d8c0bc8a578fea57ab81fb496f8e6add
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420365"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Criar um agente de Tarefa Elástica com o PowerShell

As [tarefas elásticas](sql-database-job-automation-overview.md#elastic-database-jobs-preview) permitem a execução de um ou mais scripts Transact-SQL (T-SQL) em paralelo em muitas bases de dados.

Neste tutorial, você aprende as etapas necessárias para executar uma consulta em vários bancos de dados:

> [!div class="checklist"]
> * Criar um agente de Tarefa Elástica
> * Criar credenciais de tarefa para que as tarefas possam executar scripts nos respetivos destinos
> * Definir os destinos (servidores, conjuntos elásticos, bases de dados, mapas de partições horizontais) em que quer executar a tarefa
> * Criar credenciais com âmbito de base de dados nas bases de dados de destino para que o agente ligue e execute tarefas
> * Criar uma tarefa
> * Adicionar passos de tarefa a uma tarefa
> * Iniciar a execução de uma tarefa
> * Monitorizar uma tarefa

## <a name="prerequisites"></a>Pré-requisitos

A versão atualizada do trabalhos de Banco de Dados Elástico tem um novo conjunto de cmdlets do PowerShell para uso durante a migração. Esses novos cmdlets transferem todas as suas credenciais de trabalho existentes, destinos (incluindo bancos de dados, servidores, coleções personalizadas), gatilhos de trabalho, agendas de trabalho, conteúdos de trabalho e trabalhos para um novo agente de trabalho elástico.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Instalar os cmdlets de trabalhos elásticos mais recentes

Se ainda não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Instale o módulo **AZ. SQL** para obter os cmdlets de trabalho elástico mais recentes. Execute os comandos a seguir no PowerShell com acesso administrativo.

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

Além do módulo **AZ. SQL** , este tutorial também requer o módulo PowerShell do *SqlServer* . Para obter detalhes, consulte [instalar SQL Server PowerShell Module](/sql/powershell/download-sql-server-ps-module).

## <a name="create-required-resources"></a>Criar os recursos necessários

A criação de um agente de Tarefa Elástica requer uma base de dados (S0 ou superior) para utilização como [base de dados de Tarefa](sql-database-job-automation-overview.md#job-database).

O script a seguir cria um novo grupo de recursos, servidor e banco de dados para uso como o banco de dados de trabalho. O segundo script cria um segundo servidor com dois bancos de dados em branco para executar trabalhos.

As tarefas elásticas não têm requisitos de nomenclatura específicos, pelo que pode utilizar quaisquer convenções de nomenclatura que queira, desde que estejam em conformidade com os [requisitos do Azure](/azure/architecture/best-practices/resource-naming).

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

## <a name="use-elastic-jobs"></a>Usar trabalhos elásticos

Para usar trabalhos elásticos, registre o recurso em sua assinatura do Azure executando o comando a seguir. Execute esse comando uma vez para a assinatura na qual você pretende provisionar o agente de trabalho elástico. As assinaturas que contêm apenas bancos de dados que são destinos de trabalho não precisam ser registradas.

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

### <a name="create-the-elastic-job-agent"></a>Criar o agente de Tarefa Elástica

Um agente de Tarefa Elástica é um recurso do Azure para criar, executar e gerir tarefas. O agente executa tarefas com base num agendamento ou como uma tarefa única.

O cmdlet **New-AzSqlElasticJobAgent** requer que um banco de dados SQL do Azure já exista, portanto, os parâmetros *resourceGroupName*, *ServerName*e *DatabaseName* devem apontar para os recursos existentes.

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>Criar as credenciais do trabalho

Os trabalhos usam as credenciais no escopo do banco de dados para se conectar aos banco de dados de destino especificados pelo grupo de destino na execução e executar scripts. Estas credenciais com âmbito de base de dados também são utilizadas para ligar à base de dados mestra para enumerar todas as bases de dados num servidor ou conjunto elástico, quando qualquer um destes for utilizado como tipo de membro do grupo de destino.

As credenciais com âmbito de base de dados têm de ser criadas na base de dados de tarefa. Todas as bases de dados de destino precisam de um início de sessão com permissões suficientes para a tarefa ser concluída com êxito.

![Credenciais de Tarefas Elásticas](media/elastic-jobs-overview/job-credentials.png)

Além das credenciais na imagem, tenha em atenção a adição dos comandos **GRANT** no script seguinte. Estas permissões são necessárias para o script que escolhemos para esta tarefa de exemplo. Como o exemplo cria uma nova tabela nos bancos de dados direcionados, cada BD de destino precisa das permissões apropriadas para ser executada com êxito.

Para criar as credenciais de tarefa necessárias (na base de dados de tarefa), execute o seguinte script:

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>Definir os bancos de dados de destino para executar o trabalho

Um [grupo de destino](sql-database-job-automation-overview.md#target-group) define o conjunto de um ou mais bases de dados onde será executado um passo de tarefa.

O trecho a seguir cria dois grupos de destino: *ServerGroup*e *serverGroupExcludingDb2*. o *ServerGroup* tem como destino todos os bancos de dados existentes no servidor no momento da execução e o *serverGroupExcludingDb2* se destina a todos os bancos de dados no servidor, exceto *targetDb2*:

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>Criar um trabalho e etapas

Este exemplo define um trabalho e duas etapas de trabalho para que o trabalho seja executado. O primeiro passo de tarefa (*step1*) cria uma nova tabela (*Step1Table*) em cada base de dados no grupo de destino *ServerGroup*. A segunda etapa de trabalho (*etapa 2*) cria uma nova tabela (*Step2Table*) em cada banco de dados, exceto *TargetDb2*, porque o grupo de destino definido anteriormente foi especificado para excluí-lo.

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>Executar a tarefa

Para iniciar a tarefa imediatamente, execute o seguinte comando:

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

Após a conclusão com êxito, deve ver duas novas tabelas em TargetDb1 e apenas uma nova tabela em TargetDb2:

   ![verificação de novas tabelas no SSMS](media/elastic-jobs-overview/job-execution-verification.png)

Você também pode agendar o trabalho para ser executado posteriormente. Para agendar uma tarefa para ser executada num momento específico, execute o seguinte comando:

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>Monitorizar o estado das execuções de tarefas

Os fragmentos seguintes obtêm os detalhes de execução da tarefa:

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

A tabela a seguir lista os Estados de execução de trabalho possíveis:

|Estado|Descrição|
|:---|:---|
|**Criação** | A execução do trabalho acabou de ser criada e ainda não está em andamento.|
|**InProgress** | A execução do trabalho está atualmente em andamento.|
|**WaitingForRetry** | A execução do trabalho não pôde concluir sua ação e está aguardando para tentar novamente.|
|**Foi** | A execução do trabalho foi concluída com êxito.|
|**SucceededWithSkipped** | A execução do trabalho foi concluída com êxito, mas alguns de seus filhos foram ignorados.|
|**Falha ao** | A execução do trabalho falhou e esgotou suas tentativas.|
|**TimedOut** | A execução do trabalho atingiu o tempo limite.|
|**Cancel** | A execução do trabalho foi cancelada.|
|**Ignorado** | A execução do trabalho foi ignorada porque outra execução da mesma etapa de trabalho já estava em execução no mesmo destino.|
|**WaitingForChildJobExecutions** | A execução do trabalho está aguardando a conclusão de suas execuções filhas.|

## <a name="clean-up-resources"></a>Limpar recursos

Elimine os recursos do Azure criados neste tutorial ao eliminar o grupo de recursos.

> [!TIP]
> Se você planeja continuar a trabalhar com esses trabalhos, não limpe os recursos criados neste artigo.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, executou um script Transact-SQL num conjunto de bases de dados. Aprendeu a efetuar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um agente de Tarefa Elástica
> * Criar credenciais de tarefa para que as tarefas possam executar scripts nos respetivos destinos
> * Definir os destinos (servidores, conjuntos elásticos, bases de dados, mapas de partições horizontais) em que quer executar a tarefa
> * Criar credenciais com âmbito de base de dados nas bases de dados de destino para que o agente ligue e execute tarefas
> * Criar uma tarefa
> * Adicionar um passo de tarefa à tarefa
> * Iniciar uma execução da tarefa
> * Monitorizar a tarefa

> [!div class="nextstepaction"]
> [Gerir Tarefas Elásticas com Transact-SQL](elastic-jobs-tsql.md)
