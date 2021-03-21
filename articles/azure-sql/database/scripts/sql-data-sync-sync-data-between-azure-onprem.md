---
title: 'PowerShell: Sync dados entre a Base de Dados SQL e o Servidor SQL'
description: Utilize um script exemplo Azure PowerShell para sincronizar dados entre a Base de Dados Azure SQL e o SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 443232bb41ba73b5bd02d45c542e555904f539db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792876"
---
# <a name="use-powershell-to-sync-data-between-sql-database-and-sql-server"></a>Utilize o PowerShell para sincronizar dados entre a Base de Dados SQL e o Servidor SQL

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo Azure PowerShell configura o Data Sync para sincronizar dados entre a Base de Dados Azure SQL e o SQL Server. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer az PowerShell 1.4.0 ou mais tarde. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

Para obter uma visão geral do SQL Data Sync, consulte [os dados do Sync em várias bases de dados de nuvem e no local com sql Data Sync im Azure](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> O SQL Data Sync não suporta a Azure SQL Managed Instance neste momento.

## <a name="prerequisites"></a>Pré-requisitos

- Crie uma base de dados na Base de Dados Azure SQL a partir de uma base de dados de amostras AdventureWorksLT como base de dados de hub.
- Crie uma base de dados na Base de Dados Azure SQL na mesma região que a base de dados de sincronização.
- Crie uma base de dados numa instância do SQL Server como base de dados de membros.
- Atualize os espaços reservados para parâmetros antes de executar o exemplo.

## <a name="example"></a>Exemplo

```powershell-interactive
using namespace Microsoft.Azure.Commands.Sql.DataSync.Model
using namespace System.Collections.Generic

# hub database info
$subscriptionId = "<subscriptionId>"
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$databaseName = "<databaseName>"

# sync database info
$syncDatabaseResourceGroupName = "<syncResourceGroupName>"
$syncDatabaseServerName = "<syncServerName>"
$syncDatabaseName = "<syncDatabaseName>"

# sync group info
$syncGroupName = "<syncGroupName>"
$conflictResolutionPolicy = "HubWin" # can be HubWin or MemberWin
$intervalInSeconds = 300 # sync interval in seconds (must be no less than 300)

# member database info
$syncMemberName = "<syncMemberName>"
$memberServerName = "<memberServerName>"
$memberDatabaseName = "<memberDatabaseName>"
$memberDatabaseType = "SqlServerDatabase" # can be AzureSqlDatabase or SqlServerDatabase
$syncDirection = "Bidirectional" # can be Bidirectional, Onewaymembertohub, Onewayhubtomember

# sync agent info
$syncAgentName = "<agentName>"
$syncAgentResourceGroupName = "<syncAgentResourceGroupName>"
$syncAgentServerName = "<syncAgentServerName>"

# temp file to save the sync schema
$tempFile = $env:TEMP+"\syncSchema.json"

# list of included columns and tables in quoted name
$includedColumnsAndTables =  "[SalesLT].[Address].[AddressID]",
                             "[SalesLT].[Address].[AddressLine2]",
                             "[SalesLT].[Address].[rowguid]",
                             "[SalesLT].[Address].[PostalCode]",
                             "[SalesLT].[ProductDescription]"
$metadataList = [System.Collections.ArrayList]::new($includedColumnsAndTables)

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

# use if it's safe to show password in script, otherwise use PromptForCredential
# $user = "username"
# $password = ConvertTo-SecureString -String "password" -AsPlainText -Force
# $credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $user, $password

$credential = $Host.ui.PromptForCredential("Need credential",
              "Please enter your user name and password for server "+$serverName+".database.windows.net",
              "",
              "")

# create a new sync agent
Write-Host "Creating new Sync Agent..."
New-AzSqlSyncAgent -ResourceGroupName $resourceGroupName -ServerName  $serverName -SyncDatabaseName $syncDatabaseName -SyncAgentName $syncAgentName

# generate agent key
Write-Host "Generating Agent Key..."
$agentKey = New-AzSqlSyncAgentKey -ResourceGroupName $resourceGroupName -ServerName $serverName -SyncAgentName $syncAgentName
Write-Host "Use your agent key to configure the sync agent. Do this before proceeding."
$agentkey

# DO THE FOLLOWING BEFORE THE NEXT STEP
# Install the on-premises sync agent on your machine and register the sync agent using the agent key generated above to bring the sync agent online.
# Add the SQL Server database information including server name, database name, user name, password on the configuration tool within the sync agent.  

# create a new sync group
Write-Host "Creating Sync Group "$syncGroupName"..."
New-AzSqlSyncGroup -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Name $syncGroupName `
    -SyncDatabaseName $syncDatabaseName -SyncDatabaseServerName $syncDatabaseServerName -SyncDatabaseResourceGroupName $syncDatabaseResourceGroupName `
    -ConflictResolutionPolicy $conflictResolutionPolicy -DatabaseCredential $credential

# use if it's safe to show password in script, otherwise use PromptForCredential
#$user = "username"
#$password = ConvertTo-SecureString -String "password" -AsPlainText -Force
#$credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $user, $password

$credential = $Host.ui.PromptForCredential("Need credential",
              "Please enter your user name and password for server "+$memberServerName,
              "",
              "")

# get information from sync agent and confirm your SQL Server instance was configured (note the database ID to use for the sqlServerDatabaseID in the next step)
$syncAgentInfo = Get-AzSqlSyncAgentLinkedDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -SyncAgentName $syncAgentName

# add a new sync member
Write-Host "Adding member"$syncMemberName" to the sync group..."

New-AzSqlSyncMember -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
    -SyncGroupName $syncGroupName -Name $syncMemberName -MemberDatabaseType $memberDatabaseType -SyncAgentResourceGroupName $syncAgentResourceGroupName `
    -SyncAgentServerName $syncAgentServerName -SyncAgentName $syncAgentName  -SyncDirection $syncDirection -SqlServerDatabaseID  $syncAgentInfo.DatabaseId

# refresh database schema from hub database, specify the -SyncMemberName parameter if you want to refresh schema from the member database
Write-Host "Refreshing database schema from hub database..."
$startTime = Get-Date
Update-AzSqlSyncSchema -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -SyncGroupName $syncGroupName

# waiting for successful refresh
$startTime = $startTime.ToUniversalTime()
$timer=0
$timeout=90

# check the log and see if refresh has gone through
Write-Host "Check for successful refresh..."
$isSucceeded = $false
while ($isSucceeded -eq $false) {
    Start-Sleep -s 10
    $timer=$timer+10
    $details = Get-AzSqlSyncSchema -SyncGroupName $syncGroupName -ServerName $serverName -DatabaseName $databaseName -ResourceGroupName $resourceGroupName
    if ($details.LastUpdateTime -gt $startTime) {
        Write-Host "Refresh was successful"
        $isSucceeded = $true
    }
    if ($timer -eq $timeout) {
        Write-Host "Refresh timed out"
        break;
    }
}

# get the database schema
Write-Host "Adding tables and columns to the sync schema..."
$databaseSchema = Get-AzSqlSyncSchema -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
    -DatabaseName $DatabaseName -SyncGroupName $SyncGroupName `

$databaseSchema | ConvertTo-Json -depth 5 -Compress | Out-File "C:\Users\OnPremiseServer\AppData\Local\Temp\syncSchema.json"

$newSchema = [AzureSqlSyncGroupSchemaModel]::new()
$newSchema.Tables = [List[AzureSqlSyncGroupSchemaTableModel]]::new();

# add columns and tables to the sync schema
foreach ($tableSchema in $databaseSchema.Tables) {
    $newTableSchema = [AzureSqlSyncGroupSchemaTableModel]::new()
    $newTableSchema.QuotedName = $tableSchema.QuotedName
    $newTableSchema.Columns = [List[AzureSqlSyncGroupSchemaColumnModel]]::new();
    $addAllColumns = $false
    if ($MetadataList.Contains($tableSchema.QuotedName)) {
        if ($tableSchema.HasError) {
            $fullTableName = $tableSchema.QuotedName
            Write-Host "Can't add table $fullTableName to the sync schema" -foregroundcolor "Red"
            Write-Host $tableSchema.ErrorId -foregroundcolor "Red"
            continue;
        }
        else {
            $addAllColumns = $true
        }
    }
    foreach($columnSchema in $tableSchema.Columns) {
        $fullColumnName = $tableSchema.QuotedName + "." + $columnSchema.QuotedName
        if ($addAllColumns -or $MetadataList.Contains($fullColumnName)) {
            if ((-not $addAllColumns) -and $tableSchema.HasError) {
                Write-Host "Can't add column $fullColumnName to the sync schema" -foregroundcolor "Red"
                Write-Host $tableSchema.ErrorId -foregroundcolor "Red"
            }
            elseif ((-not $addAllColumns) -and $columnSchema.HasError) {
                Write-Host "Can't add column $fullColumnName to the sync schema" -foregroundcolor "Red"
                Write-Host $columnSchema.ErrorId -foregroundcolor "Red"
            }
            else {
                Write-Host "Adding"$fullColumnName" to the sync schema"
                $newColumnSchema = [AzureSqlSyncGroupSchemaColumnModel]::new()
                $newColumnSchema.QuotedName = $columnSchema.QuotedName
                $newColumnSchema.DataSize = $columnSchema.DataSize
                $newColumnSchema.DataType = $columnSchema.DataType
                $newTableSchema.Columns.Add($newColumnSchema)
            }
        }
    }
    if ($newTableSchema.Columns.Count -gt 0) {
        $newSchema.Tables.Add($newTableSchema)
    }
}

# convert sync schema to JSON format
$schemaString = $newSchema | ConvertTo-Json -depth 5 -Compress

# workaround a powershell bug
$schemaString = $schemaString.Replace('"Tables"', '"tables"').Replace('"Columns"', '"columns"').Replace('"QuotedName"', '"quotedName"').Replace('"MasterSyncMemberName"','"masterSyncMemberName"')

# save the sync schema to a temp file
$schemaString | Out-File $tempFile

# update sync schema
Write-Host "Updating the sync schema..."
Update-AzSqlSyncGroup -ResourceGroupName $resourceGroupName -ServerName $serverName `
    -DatabaseName $databaseName -Name $syncGroupName -Schema $tempFile

$syncLogStartTime = Get-Date

# trigger sync manually
Write-Host "Trigger sync manually..."
Start-AzSqlSyncGroupSync -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -SyncGroupName $syncGroupName

# check the sync log and wait until the first sync succeeded
Write-Host "Check the sync log..."
$isSucceeded = $false
for ($i = 0; ($i -lt 300) -and (-not $isSucceeded); $i = $i + 10) {
    Start-Sleep -s 10
    $syncLogEndTime = Get-Date
    $syncLogList = Get-AzSqlSyncGroupLog -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -SyncGroupName $syncGroupName -StartTime $syncLogStartTime.ToUniversalTime() -EndTime $syncLogEndTime.ToUniversalTime()

    if ($synclogList.Length -gt 0) {
        foreach ($syncLog in $syncLogList) {
            if ($syncLog.Details.Contains("Sync completed successfully")) {
                Write-Host $syncLog.TimeStamp : $syncLog.Details
                $isSucceeded = $true
            }
        }
    }
}

if ($isSucceeded) {
    # enable scheduled sync
    Write-Host "Enable the scheduled sync with 300 seconds interval..."
    Update-AzSqlSyncGroup  -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -Name $syncGroupName -IntervalInSeconds $intervalInSeconds
}
else {
    # output all log if sync doesn't succeed in 300 seconds
    $syncLogEndTime = Get-Date
    $syncLogList = Get-AzSqlSyncGroupLog  -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
        -SyncGroupName $syncGroupName -StartTime $syncLogStartTime.ToUniversalTime() -EndTime $syncLogEndTime.ToUniversalTime()

    if ($synclogList.Length -gt 0) {
        foreach ($syncLog in $syncLogList) {
            Write-Host $syncLog.TimeStamp : $syncLog.Details
        }
    }
}
```

## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode executar o seguinte comando para remover o grupo de recursos e todos os recursos associados ao mesmo.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Remove-AzResourceGroup -ResourceGroupName $syncDatabaseResourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzSqlSyncAgent](/powershell/module/az.sql/New-azSqlSyncAgent) |  Cria um novo Agente sincronizado. |
| [New-AzSqlSyncAgentKey](/powershell/module/az.sql/New-azSqlSyncAgentKey) |  Gera a chave do agente associada ao Agente sincronizado. |
| [Get-AzSqlSyncAgentLinkedDatabase](/powershell/module/az.sql/Get-azSqlSyncAgentLinkedDatabase) |  Obtenha toda a informação para o Agente Sincronizado. |
| [Membro do New-AzSqlSyncMember](/powershell/module/az.sql/New-azSqlSyncMember) |  Adicione um novo membro ao Sync Group. |
| [Atualização-AzSqlSyncSchema](/powershell/module/az.sql/Update-azSqlSyncSchema) |  Atualiza a informação do esquema da base de dados. |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/Get-azSqlSyncSchema) |  Obtenha a informação do esquema da base de dados. |
| [Atualização-AzSqlSyncGroup](/powershell/module/az.sql/Update-azSqlSyncGroup) |  Atualiza o Grupo Sync. |
| [Start-AzSqlSyncGroupSync](/powershell/module/az.sql/Start-azSqlSyncGroupSync) | Despoleta uma sincronização. |
| [Get-AzSqlSyncGroupLog](/powershell/module/az.sql/Get-azSqlSyncGroupLog) |  Verifica o Registo de Sincronização. |
|||

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver exemplos do script do PowerShell da Base de Dados SQL adicionais nos [scripts do PowerShell da Base de Dados SQL do Azure](../powershell-script-content-guide.md).

Para obter mais informações sobre o SQL Data Sync, consulte:

- Visão geral - [Sync dados em várias bases de dados de nuvem e no local com Azure SQL Data Sync](../sql-data-sync-data-sql-server-sql-database.md)
- Configurar o Data Sync
    - Utilize o portal Azure - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre uma base de dados na Base de Dados Azure SQL e uma base de dados de bases de dados do SQL Server nas instalações](../sql-data-sync-sql-server-configure.md)
    - Utilizar PowerShell - [Use PowerShell para sincronizar entre várias bases de dados na Base de Dados Azure SQL](sql-data-sync-sync-data-between-sql-databases.md)
- Data Sync Agent - [Data Sync Agent for SQL Data Sync in Azure](../sql-data-sync-agent-overview.md)
- Melhores práticas - [Melhores práticas para SQL Data Sync em Azure](../sql-data-sync-best-practices.md)
- Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](../monitor-tune-overview.md)
- Resolução de problemas - [Problemas de resolução de problemas com SQL Data Sync em Azure](../sql-data-sync-troubleshoot.md)
- Atualizar o esquema de sincronização
    - Utilizar Transact-SQL - [Automatizar a replicação de alterações de esquema no SQL Data Sync em Azure](../sql-data-sync-update-sync-schema.md)
    - Use PowerShell - [Use PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](update-sync-schema-in-sync-group.md)

Para obter mais informações sobre a Base de Dados Azure SQL, consulte:

- [Visão geral da base de dados sql](../sql-database-paas-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))