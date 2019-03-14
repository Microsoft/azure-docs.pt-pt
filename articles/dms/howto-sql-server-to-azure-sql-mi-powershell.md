---
title: Migrar o SQL Server para instância gerida de base de dados SQL do Azure com o serviço de migração de base de dados e o PowerShell | Documentos da Microsoft
description: Aprenda a migrar do SQL Server no local para a instância gerida BD SQL do Azure com o Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 3543b7c7410b31b50a6654d3d9807128fff9fd19
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791635"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-managed-instance-using-azure-powershell"></a>Migrar o SQL Server no local para o Azure SQL Database Managed Instance com o Azure PowerShell
Neste artigo, migra os **Adventureworks2012** base de dados restaurada para uma instância no local do SQL Server 2005 ou superior para uma instância de gerida de base de dados do Azure SQL com o Microsoft Azure PowerShell. Pode migrar bases de dados de uma instância do SQL Server no local para a instância gerida da base de dados SQL do Azure utilizando o `AzureRM.DataMigration` módulo no Microsoft Azure PowerShell.

Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Crie um grupo de recursos.
> * Criar uma instância do Azure Database Migration Service.
> * Crie um projeto de migração numa instância de serviço de migração de base de dados do Azure.
> * Executar a migração.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir estes passos, terá de:

- [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição)
- Para ativar o protocolo de TCP/IP, o que está desabilitado por predefinição com a instalação do SQL Server Express. Ativar o protocolo de TCP/IP, seguindo o artigo [ativar ou desativar um protocolo de rede do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Para configurar seus [Firewall do Windows para acesso ao motor de base de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Uma instância de base de dados do Azure SQL. Pode criar uma instância de SQL Database do Azure ao seguir o detalhe no artigo [criar uma base de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
- Para criar uma VNET ao utilizar o modelo de implementação Azure Resource Manager, que fornece o serviço de migração de base de dados do Azure com a conectividade de site a site aos seus servidores de origem no local através de um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Para concluir a avaliação da sua migração de base de dados e esquema de no local com o Assistente de migração de dados, tal como descrito no artigo [realizar uma avaliação de migração do SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Para transferir e instalar o módulo de AzureRM.DataMigration da galeria do PowerShell, utilizando [cmdlet do PowerShell de Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- Para garantir que as credenciais utilizadas para ligar à instância do SQL Server de origem tem o [servidor de CONTROLE](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) permissão.
- Para garantir que as credenciais utilizadas para estabelecer ligação ao destino do Azure SQL DB instância tem a permissão controlar base de dados nas bases de dados de base de dados do Azure SQL de destino.
- Uma subscrição do Azure. Se não tiver uma, crie uma [gratuita](https://azure.microsoft.com/free/) conta antes de começar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Inicie sessão na sua subscrição do Microsoft Azure
Utilize as instruções no artigo [iniciar sessão com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) para iniciar sessão na sua subscrição do Azure com o PowerShell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos antes de poder criar uma máquina virtual.

Criar um grupo de recursos com o [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) comando. 

O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* no *EastUS* região.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Criar uma instância do serviço de migração de base de dados do Azure 
Pode criar nova instância do serviço de migração de base de dados do Azure utilizando o `New-AzureRmDataMigrationService` cmdlet. Este cmdlet espera que os parâmetros necessários seguintes:
- *Nome do grupo de recursos do Azure*. Pode usar [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) comando para criar o grupo de recursos do Azure, conforme mostrado anteriormente e forneça o nome como um parâmetro.
- *Nome do serviço*. Cadeia de caracteres que corresponde ao nome do serviço exclusivo desejado para o Azure Database Migration Service 
- *Localização*. Especifica a localização do serviço. Especifique uma localização de centro de dados do Azure, como EUA ocidental ou Sudeste asiático
- *Sku*. Este parâmetro corresponde ao nome de Sku de DMS. São atualmente suportados nomes de Sku *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Identificador da sub-rede virtual*. Pode utilizar o cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) para criar uma sub-rede. 

O exemplo seguinte cria um serviço com o nome *MyDMS* no grupo de recursos *MyDMSResourceGroup* localizado no *E.U.A. Leste* região com uma rede virtual denominada  *MyVNET* e uma sub-rede denominada *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Depois de criar uma instância de serviço de migração de base de dados do Azure, crie um projeto de migração. Um projeto de serviço de migração de base de dados do Azure requer informações de ligação para as instâncias de origem e destino, bem como uma lista de bases de dados que pretende migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de informações de ligação de base de dados para as ligações de origem e destino
Pode criar um objeto de informações de ligação de base de dados utilizando o `New-AzureRmDmsConnInfo` cmdlet. Este cmdlet espera os seguintes parâmetros:
- *ServerType*. O tipo de ligação de base de dados solicitada, por exemplo, MySQL, Oracle ou SQL. Utilize o SQL para o SQL Server e o Azure SQL.
- *DataSource*. O nome ou o IP de uma instância do SQL Server ou a base de dados SQL do Azure.
- *AuthType*. O tipo de autenticação para a ligação, que pode ser SqlAuthentication ou WindowsAuthentication.
- *TrustServerCertificate* parâmetro define um valor que indica se o canal é encriptado ao ignorar a movimentação de cadeia de certificados para validar a confiança. Valor pode ser true ou false.

O exemplo seguinte cria o objeto de informações de ligação para a origem do SQL Server chamado MySourceSQLServer utilizando a autenticação sql: 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O exemplo seguinte mostra a criação de informações de ligação para um servidor de instância gerida da base de dados SQL do Azure chamado "targetmanagedinstance.database.windows.net' utilizando a autenticação sql:

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer as bases de dados para o projeto de migração
Criar uma lista de `AzureRmDataMigrationDatabaseInfo` objetos que especifica a bases de dados como parte da migração de base de dados do Azure de projeto que podem ser fornecidos como parâmetro para a criação do projeto. O Cmdlet `New-AzureRmDataMigrationDatabaseInfo` pode ser utilizado para criar AzureRmDataMigrationDatabaseInfo. 

O exemplo seguinte cria `AzureRmDataMigrationDatabaseInfo` do projeto para o **AdventureWorks** de base de dados e adiciona-o à lista de ser fornecido como parâmetro para a criação do projeto.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto
Por fim pode criar o projeto de migração de base de dados do Azure chamado *MyDMSProject* localizado na *E.U.A. Leste* usando `New-AzureRmDataMigrationProject` e adicionar as ligações de origem e destino criadas anteriormente e a lista de a migração de bases de dados.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração
Por fim, crie e inicie a tarefa de migração de base de dados do Azure. Tarefa de migração de base de dados do Azure requer informações de credenciais de ligação para a origem e destino e lista de tabelas de base de dados a serem migrados além das informações já é fornecidas com o projeto criado como um pré-requisito. 

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credencial para a origem e destino
As credenciais de segurança da ligação podem ser criadas como um [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto. 

O exemplo seguinte mostra a criação de *PSCredential* objetos para ligações de origem e destino fornecer palavras-passe como variáveis de cadeia de caracteres *$sourcePassword* e *$ targetPassword*. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-backup-fileshare-object"></a>Criar o objeto de partilha de ficheiros de cópia de segurança
Agora crie o objeto de partilha de ficheiros que representa a partilha de rede SMB local que o serviço de migração de base de dados do Azure pode levar a origem de cópias de segurança da base de dados para utilizar o cmdlet New-AzureRmDmsFileShare.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzureRmDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Criar o objeto de base de dados selecionada
A próxima etapa é selecionar as bases de dados de origem e de destino utilizando o cmdlet New-AzureRmDmsSelectedDB, conforme mostrado no exemplo a seguir:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

### <a name="sas-uri-for-azure-storage-container"></a>URI de SAS para o contentor de armazenamento do Azure
Crie a variável que contém o URI de SAS que fornece o serviço de migração de base de dados do Azure com o acesso ao contentor de conta de armazenamento para o qual o serviço carrega os ficheiros de cópia de segurança.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="select-logins"></a>Selecionar os inícios de sessão
Crie lista de inícios de sessão a ser migrada, conforme mostrado no exemplo abaixo:  Tenha em atenção que atualmente suporta DMS migrar apenas inícios de sessão SQL. 

```powershell
$selectedLogins = @("user1", "user2")
```

### <a name="select-agent-jobs"></a>Selecionar tarefas de agente
Crie lista de tarefas de agente a ser migrada, conforme mostrado no exemplo abaixo:

>[!NOTE]
>O DMS suporta atualmente os trabalhos com passos de tarefa de subsistema de T-SQL apenas.

```powershell
$selectedAgentJobs = @("agentJob1", "agentJob2")
```

### <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração

Utilize o `New-AzureRmDataMigrationTask` cmdlet para criar e iniciar uma tarefa de migração. Este cmdlet espera os seguintes parâmetros:
- *TaskType*. Tipo de tarefa de migração para criar para o SQL Server para o tipo de migração de instância gerida da base de dados SQL do Azure *MigrateSqlServerSqlDbMi* é esperado. 
- *Nome do grupo de recursos*. Nome do grupo de recursos do Azure na qual pretende criar a tarefa.
- *ServiceName*. Instância de serviço de migração de base de dados do Azure na qual pretende criar a tarefa.
- *ProjectName*. Nome do projeto de serviço de migração de base de dados do Azure no qual pretende criar a tarefa. 
- *TaskName*. Nome da tarefa a ser criada. 
- *SourceConnection*. Objeto de AzureRmDmsConnInfo que representa a ligação do SQL Server de origem.
- *TargetConnection*. Objeto de AzureRmDmsConnInfo que representa a ligação de instância gerida da base de dados SQL do Azure de destino.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto para ligar ao servidor de origem.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto para ligar ao servidor de destino.
- *SelectedDatabase*. Objeto de AzureRmDataMigrationSelectedDB que representa o mapeamento de banco de dados de origem e de destino.
- *BackupFileShare*. Objeto de partilha de ficheiros que representa a partilha de rede local que o serviço de migração de base de dados do Azure podem tirar a origem a cópias de segurança da base de dados.
- *BackupBlobSasUri*. O URI de SAS que fornece o serviço de migração de base de dados do Azure com o acesso ao contentor de conta de armazenamento para o qual o serviço carrega os ficheiros de cópia de segurança. Saiba como obter o URI de SAS para o contentor de Blobs.
- *SelectedLogins*. Lista de inícios de sessão selecionados para migrar.
- *SelectedAgentJobs*. Lista de tarefas de agente selecionado para migrar.

O exemplo seguinte cria e inicia uma tarefa de migração com o nome myDMSTask:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

## <a name="monitor-the-migration"></a>Monitorizar a migração
Pode monitorizar a tarefa de migração está em execução, consultando a propriedade de estado da tarefa, conforme mostrado no exemplo a seguir:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>A eliminar a instância DMS
Após a migração estiver concluída, pode eliminar a instância do Azure DMS:

```powershell
Remove-AzureRmDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-steps"></a>Passos Seguintes
- Reveja as orientações de migração no Microsoft [guia de migração de bases de dados](https://datamigration.microsoft.com/).
