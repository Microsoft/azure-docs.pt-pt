---
title: 'PowerShell: Migrar o SqL Server para o SQL Managed Instance online'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar on-line do SQL Server para Azure SQL Managed Instance utilizando a Azure PowerShell e o Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697829"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>Migrar o SQL Server para o SQL Gerenciado Instance online com o PowerShell & Serviço de Migração de Bases de Dados Azure

Neste artigo, você migra on-line a base de dados **Adventureworks2016** restaurada para um caso no local do SQL Server 2005 ou acima para um Azure SQL SQL Managed Instance usando o Microsoft Azure PowerShell. Pode migrar bases de dados de uma instância sql server para uma sql Managed Instance utilizando o `Az.DataMigration` módulo no Microsoft Azure PowerShell.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Crie um grupo de recursos.
> * Crie uma instância do Azure Database Migration Service.
> * Criar um projeto de migração num caso do Serviço de Migração da Base de Dados Azure.
> * Executar a migração on-line.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo fornece passos para uma migração on-line, mas também é possível migrar [offline](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md).


## <a name="prerequisites"></a>Pré-requisitos

Para completar estes passos, você precisa:

* [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição).
* Uma cópia local da base de dados **AdventureWorks2016,** que está disponível para download [aqui.](/sql/samples/adventureworks-install-configure)
* Para ativar o protocolo TCP/IP, que é desativado por predefinição com a instalação SQL Server Express. Ativar o protocolo TCP/IP seguindo o artigo [Ative ou desative um Protocolo de Rede de Servidor](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Para configurar o seu [Windows Firewall para acesso ao motor de base de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um SQL Managed Instance. Pode criar uma SqL Managed Instance seguindo os detalhes do artigo [Criar uma Instância Gerida ASQL](../azure-sql/managed-instance/instance-create-quickstart.md).
* Para descarregar e instalar [o Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
* Uma Rede Virtual Microsoft Azure criada utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece ao Serviço de Migração de Bases de Dados Azure conectividade site-to-site aos seus servidores de origem no local, utilizando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou [o VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Uma avaliação completa da sua base de dados no local e migração de esquemas utilizando o Data Migration Assistant, conforme descrito no artigo [Realizando uma avaliação de migração do SQL Server](/sql/dma/dma-assesssqlonprem).
* Para descarregar e instalar o `Az.DataMigration` módulo (versão 0.7.2 ou posterior) da Galeria PowerShell utilizando o [cmdlet De instalação-Módulo PowerShell](/powershell/module/powershellget/Install-Module).
* Para garantir que as credenciais utilizadas para ligar à origem SQL Server têm a permissão [do SERVIDOR DE CONTROLO.](/sql/t-sql/statements/grant-server-permissions-transact-sql)
* Para garantir que as credenciais utilizadas para se ligar ao exemplo gerido do SQL têm a permissão de BASE DE DADOS DE CONTROLO nas bases de dados de casos geridos sql alvo.

    > [!IMPORTANT]
    > Para migrações on-line, já deve ter configurado as suas credenciais de Diretório Ativo Azure. Para mais informações, consulte o artigo [Utilize o portal para criar uma aplicação AD AZure e um responsável de serviço que possa aceder aos recursos.](../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos Azure é um recipiente lógico no qual os recursos da Azure são implantados e geridos.

Criar um grupo de recursos utilizando o [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) comando.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *leste dos EUA.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>Criar um exemplo de DMS

Pode criar uma nova instância do Serviço de Migração da Base de Dados Azure utilizando o `New-AzDataMigrationService` cmdlet.
Este cmdlet espera os seguintes parâmetros necessários:

* *Nome do Grupo de Recursos Azure*. Pode utilizar [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) o comando para criar um grupo de Recursos Azure, como anteriormente mostrado e fornecer o seu nome como parâmetro.
* *Nome de serviço*. Cadeia que corresponde ao nome de serviço único desejado para O Serviço de Migração de Bases de Dados Azure.
* *Localização*. Especifica a localização do serviço. Especifique uma localização do centro de dados Azure, como o Oeste dos EUA ou o Sudeste Asiático.
* *Sku.* Este parâmetro corresponde ao nome DMS Sku. Os nomes Sku atualmente apoiados são *Basic_1vCore*, *Basic_2vCores,* *GeneralPurpose_4vCores*.
* *Identificador de sub-rede virtual.* Pode utilizar o cmdlet [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede.

O exemplo a seguir cria um serviço chamado *MyDMS* no grupo de recursos *MyDMSResourceGroup* localizado na região *leste dos EUA* usando uma rede virtual chamada *MyVNET* e uma sub-rede chamada *MySubnet*.


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois de criar um serviço de migração de bases de dados Azure, crie um projeto de migração. Um projeto do Azure Database Migration Service requer informações de ligação tanto para as instâncias de origem como para alvos, bem como uma lista de bases de dados que pretende migrar como parte do projeto.
Defina as cadeias de ligação de conectividade de origem e alvo.

O seguinte script define detalhes de ligação do SQL Server de origem: 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

O seguinte script define os detalhes de conexão sql gerido do alvo: 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>Definir mapeamento de base de dados de origem e alvo

Fornecer bases de dados a serem migradas neste projeto de migração

O seguinte script mapeia a base de dados de origem para a respetiva nova base de dados no target SQL Managed Instance com o nome fornecido.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

Para várias bases de dados, adicione a lista de bases de dados ao script acima, utilizando o seguinte formato:

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>Criar o projeto do DMS

Pode criar um projeto do Serviço de Migração da Base de Dados Azure no caso DMS.

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>Criar um objeto de backup FileShare

Agora crie um objeto FileShare que represente a partilha de rede SMB local para a qual o Azure Database Migration Service pode obter as cópias de dados de origem utilizando o New-AzDmsFileShare cmdlet.

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Definir o Armazenamento Azure 

Selecione O Recipiente de Armazenamento Azure para ser utilizado para a migração: 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Configure Azure Ative Directory App

Forneça os detalhes necessários para o Azure Ative Directory para uma migração online da SQL Managed Instance: 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração

Em seguida, crie e inicie uma tarefa do Serviço de Migração da Base de Dados Azure. Ligue para a fonte e o alvo usando variáveis, e enuse as tabelas de bases de dados a migrar: 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

O exemplo a seguir cria e inicia uma tarefa de migração online: 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

Para mais informações, consulte [New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask).

## <a name="monitor-the-migration"></a>Monitorizar a migração

Para monitorizar a migração, execute as seguintes tarefas.

### <a name="check-the-status-of-task"></a>Verifique o estado da tarefa

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

Use o seguinte para obter a lista de erros:-

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>Executando o cutover 

Com uma migração on-line, é realizada uma cópia de segurança completa e restauro das bases de dados e, em seguida, trabalha-se na restauração dos Registos de Transações armazenados no BackupFileShare.

Quando a base de dados de uma Instância Gerida Azure SQL é atualizada com os dados mais recentes e está em sintonia com a base de dados de origem, pode efetuar um cutover.

O exemplo a seguir completará a redução\migração. Os utilizadores invocam este comando a seu critério.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Eliminação do caso do Serviço de Migração da Base de Dados de Azure

Após a conclusão da migração, pode eliminar a instância do Serviço de Migração da Base de Dados Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre cenários migratórios adicionais (pares de origem/alvo), consulte o [Guia de Migração da Base de Dados da](https://datamigration.microsoft.com/)Microsoft .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Serviço de Migração da Base de Dados Azure no artigo [O que é o Serviço de Migração de Bases de Dados Azure?](./dms-overview.md)