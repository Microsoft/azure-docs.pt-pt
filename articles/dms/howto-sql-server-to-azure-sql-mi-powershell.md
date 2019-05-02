---
title: Migrar o SQL Server para instância gerida de base de dados SQL do Azure com o serviço de migração de base de dados e o PowerShell | Documentos da Microsoft
description: Aprenda a migrar do SQL Server no local para a instância gerida BD SQL do Azure com o Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: 96ee3f5e1b3cfe67cb75e50c6247e41f0d901393
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867922"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Migrar o SQL Server no local para uma instância gerida da base de dados do Azure SQL com o Azure PowerShell
Neste artigo, migra os **Adventureworks2016** base de dados restaurada para uma instância no local do SQL Server 2005 ou superior para uma base de dados do SQL Azure instância gerida com o Microsoft Azure PowerShell. Pode migrar bases de dados de uma instância do SQL Server no local para uma instância gerida da base de dados do Azure SQL usando o `Az.DataMigration` módulo no Microsoft Azure PowerShell.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Crie um grupo de recursos.
> * Crie uma instância do serviço de migração de base de dados do Azure.
> * Crie um projeto de migração numa instância do serviço de migração de base de dados do Azure.
> * Executar a migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo inclui detalhes sobre como efetuar migrações tanto online quanto offline.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir estes passos, terá de:

* [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição).
* Uma cópia local dos **AdventureWorks2016** base de dados, o que está disponível para download [aqui](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Para ativar o protocolo de TCP/IP, o que está desabilitado por predefinição com a instalação do SQL Server Express. Ativar o protocolo de TCP/IP, seguindo o artigo [ativar ou desativar um protocolo de rede do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Para configurar seus [Firewall do Windows para acesso ao motor de base de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma instância gerida da base de dados do Azure SQL. Pode criar uma instância gerida da base de dados do Azure SQL, seguindo o detalhe no artigo [criar uma instância gerida da base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Para transferir e instalar [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
* Uma Azure Virtual Network (VNet) criados com o modelo de implementação Azure Resource Manager, que fornece o serviço de migração de base de dados do Azure com a conectividade de site a site aos seus servidores de origem no local, utilizando um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Uma avaliação completa da sua migração de base de dados e esquema de no local com dados Assistente de migração, conforme descrito no artigo [realizar uma avaliação de migração do SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Para transferir e instalar o `Az.DataMigration` módulo (versão 0.7.2 ou posterior) da galeria do PowerShell, utilizando [cmdlet do PowerShell de Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Para garantir que as credenciais utilizadas para ligar à instância do SQL Server de origem têm o [servidor de CONTROLE](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) permissão.
* Para garantir que as credenciais utilizadas para ligar à SQL Database do Azure de destino a instância gerida tem a permissão de base de dados de CONTROLE sobre as bases de dados de instância da base de dados do SQL do Azure geridos de destino.

    > [!IMPORTANT]
    > Para migrações online, tem de já ter configurado as suas credenciais do Azure Active Directory. Para obter mais informações, consulte o artigo [utilizar o portal para criar um Azure AD principal de aplicações e serviço que pode aceder a recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Inicie sessão na sua subscrição do Microsoft Azure

Inicie sessão sua subscrição do Azure com o PowerShell. Para obter mais informações, consulte o artigo [iniciar sessão com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos são implementados e geridos.

Criar um grupo de recursos com o [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando.

O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* no *E.U.A. Leste* região.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do serviço de migração de base de dados do Azure

Pode criar nova instância do serviço de migração de base de dados do Azure utilizando o `New-AzDataMigrationService` cmdlet.
Este cmdlet espera que os parâmetros necessários seguintes:

* *Nome do grupo de recursos do Azure*. Pode usar [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando para criar um grupo de recursos do Azure, conforme mostrado anteriormente e forneça o nome como um parâmetro.
* *Nome do serviço*. Cadeia de caracteres que corresponde ao nome do serviço exclusivo desejado para o Azure Database Migration Service.
* *Localização*. Especifica a localização do serviço. Especifique uma localização de centro de dados do Azure, como EUA ocidental ou Sudeste asiático.
* *Sku*. Este parâmetro corresponde ao nome de Sku de DMS. São atualmente suportados nomes de Sku *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Identificador da sub-rede virtual*. Pode utilizar o cmdlet [ `New-AzVirtualNetworkSubnetConfig` ](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede.

O exemplo seguinte cria um serviço com o nome *MyDMS* no grupo de recursos *MyDMSResourceGroup* localizado no *E.U.A. Leste* região com uma rede virtual denominada  *MyVNET* e uma sub-rede denominada *MySubnet*.

> [!IMPORTANT]
> O trecho de código abaixo é para uma migração offline, o que não necessite de uma instância do serviço de migração de base de dados do Azure com base num SKU Premium. Para uma migração online, o valor do parâmetro - Sku tem de incluir um SKU Premium.

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

Depois de criar uma instância de serviço de migração de base de dados do Azure, crie um projeto de migração. Um projeto de serviço de migração de base de dados do Azure requer informações de ligação para as instâncias de origem e destino, bem como uma lista de bases de dados que pretende migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de informações de ligação de base de dados para as ligações de origem e destino

Pode criar um objeto de informações de ligação de base de dados utilizando o `New-AzDmsConnInfo` cmdlet, que espera os seguintes parâmetros:

* *ServerType*. O tipo de ligação de base de dados solicitada, por exemplo, MySQL, Oracle ou SQL. Utilize o SQL para o SQL Server e o Azure SQL.
* *DataSource*. O nome ou o IP de uma instância do SQL Server ou uma instância de base de dados do Azure SQL.
* *AuthType*. O tipo de autenticação para a ligação, que pode ser SqlAuthentication ou WindowsAuthentication.
* *TrustServerCertificate*. Este parâmetro define um valor que indica se o canal é encriptado ao ignorar a movimentação de cadeia de certificados para validar a confiança. O valor pode ser `$true` ou `$false`.

O exemplo seguinte cria um objeto de informações de ligação para uma origem de SQL Server chamado *MySourceSQLServer* utilizando a autenticação sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O exemplo seguinte mostra a criação de informações de ligação para um servidor de instância gerida da base de dados do Azure SQL com o nome "targetmanagedinstance.database.windows.net' utilizando a autenticação sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer as bases de dados para o projeto de migração

Criar uma lista de `AzDataMigrationDatabaseInfo` objetos que especifica as bases de dados como parte do projeto de serviço de migração de base de dados do Azure, que pode ser fornecido como parâmetro para a criação do projeto. Pode utilizar o cmdlet `New-AzDataMigrationDatabaseInfo` para criar `AzDataMigrationDatabaseInfo`.

O exemplo seguinte cria a `AzDataMigrationDatabaseInfo` do projeto para o **AdventureWorks2016** de base de dados e adiciona-o à lista de ser fornecido como parâmetro para a criação do projeto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto

Por fim, pode criar um projeto de serviço de migração de base de dados do Azure chamado *MyDMSProject* localizado na *E.U.A. Leste* usando `New-AzDataMigrationProject` e adicionar as ligações de origem e destino criadas anteriormente e o lista de bases de dados para migrar.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
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

Em seguida, criar e iniciar uma tarefa de serviço de migração de base de dados do Azure. Esta tarefa requer informações de credenciais de ligação para a origem e destino, os e a lista de tabelas de base de dados a serem migrados e as informações que já é fornecido com o projeto criado como um pré-requisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credencial para a origem e destino

Criar ligação de credenciais de segurança como um [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto.

O exemplo seguinte mostra a criação de *PSCredential* objetos para ligações de origem e de destino, fornecendo as palavras-passe como variáveis de cadeia de caracteres *$sourcePassword* e *$ targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Criar um objeto de partilha de ficheiros de cópia de segurança

Agora, crie um objeto de partilha de ficheiros que representa a partilha de rede local do SMB ao qual a Azure Database Migration Service pode demorar a origem de cópias de segurança da base de dados utilizando o `New-AzDmsFileShare` cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Criar o objeto de base de dados selecionada

A próxima etapa é selecionar as bases de dados de origem e de destino utilizando o `New-AzDmsSelectedDB` cmdlet.

O exemplo seguinte é para migrar uma base de dados do SQL Server para uma instância gerida da base de dados do Azure SQL:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Se precisar de uma instância do SQL Server toda instância gerida de uma migração lift-and-shift para uma base de dados do SQL do Azure, em seguida, um loop para realizar todas as bases de dados da origem é fornecido abaixo. No exemplo a seguir, para $Server $SourceUserName e $SourcePassword, forneça a origem de detalhes do SQL Server.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>URI de SAS para o contentor de armazenamento do Azure

Crie a variável que contém o URI de SAS que fornece o serviço de migração de base de dados do Azure com o acesso ao contentor de conta de armazenamento para o qual o serviço carrega os ficheiros de cópia de segurança.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Requisitos de configuração adicionais

Existem alguns requisitos adicionais, que tem de cumprir, mas diferem dependendo de estar executando uma migração offline ou online.

#### <a name="offline-migrations"></a>Migrações offline

Para apenas migrações offline, efetue as seguintes tarefas de configuração adicionais.

* **Selecione os inícios de sessão**. Crie uma lista de inícios de sessão a ser migrada, conforme mostrado no exemplo a seguir:

    ```powershell
    $selectedLogins = @(“user1”, “user2”)
    ```

    > [!IMPORTANT]
    > Atualmente, o serviço de migração de base de dados do Azure só suporta migrar inícios de sessão do SQL.

* **Selecionar tarefas de agente**. Crie lista do agente de tarefas a serem migradas, conforme mostrado no exemplo a seguir:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Atualmente, o serviço de migração de base de dados do Azure só suporta tarefas com passos de tarefa do subsistema de T-SQL.

#### <a name="online-migrations"></a>Migrações online

Para apenas migrações online, execute as seguintes tarefas de configuração adicionais.

* **Configurar a aplicação do Azure Active Directory**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Configurar recursos de armazenamento**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Criar e iniciar a tarefa de migração

Utilize o `New-AzDataMigrationTask` cmdlet para criar e iniciar uma tarefa de migração.

#### <a name="specify-parameters"></a>Especifique parâmetros

##### <a name="common-parameters"></a>Parâmetros comuns

Independentemente de estar executando uma migração offline ou online, o `New-AzDataMigrationTask` cmdlet espera os seguintes parâmetros:

* *TaskType*. Tipo de tarefa de migração para criar para o SQL Server para o tipo de migração de instância gerida da base de dados SQL do Azure *MigrateSqlServerSqlDbMi* é esperado. 
* *Nome do grupo de recursos*. Nome do grupo de recursos do Azure na qual pretende criar a tarefa.
* *ServiceName*. Instância de serviço de migração de base de dados do Azure na qual pretende criar a tarefa.
* *ProjectName*. Nome do projeto de serviço de migração de base de dados do Azure no qual pretende criar a tarefa. 
* *TaskName*. Nome da tarefa a ser criada. 
* *SourceConnection*. Objeto de AzDmsConnInfo que representa a ligação do SQL Server de origem.
* *TargetConnection*. Objeto de AzDmsConnInfo que representa a ligação de instância gerida da base de dados SQL do Azure de destino.
* *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto para ligar ao servidor de origem.
* *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objeto para ligar ao servidor de destino.
* *SelectedDatabase*. Objeto de AzDataMigrationSelectedDB que representa o mapeamento de banco de dados de origem e de destino.
* *BackupFileShare*. Objeto de partilha de ficheiros que representa a partilha de rede local que o serviço de migração de base de dados do Azure podem tirar a origem a cópias de segurança da base de dados.
* *BackupBlobSasUri*. O URI de SAS que fornece o serviço de migração de base de dados do Azure com o acesso ao contentor de conta de armazenamento para o qual o serviço carrega os ficheiros de cópia de segurança. Saiba como obter o URI de SAS para o contentor de Blobs.
* *SelectedLogins*. Lista de inícios de sessão selecionados para migrar.
* *SelectedAgentJobs*. Lista de tarefas de agente selecionado para migrar.

##### <a name="additional-parameters"></a>Parâmetros adicionais

O `New-AzDataMigrationTask` cmdlet também espera parâmetros que são exclusivos para o tipo de migração, offline ou online, que está a executar.

* **Migrações offline**. Para migrações offline, o `New-AzDataMigrationTask` cmdlet também espera que os seguintes parâmetros:

  * *SelectedLogins*. Lista de inícios de sessão selecionados para migrar.
  * *SelectedAgentJobs*. Lista de tarefas de agente selecionado para migrar.

* **Migrações online**. Para migrações online, o `New-AzDataMigrationTask` cmdlet também espera que os seguintes parâmetros.

* *AzureActiveDirectoryApp*. Aplicação do Active Directory.
* *StorageResourceID*. ID de recurso da conta de armazenamento.

#### <a name="create-and-start-an-offline-migration-task"></a>Criar e iniciar uma tarefa de migração offline

O exemplo seguinte cria e inicia uma tarefa de migração offline com o nome **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Criar e iniciar uma tarefa de migração online

O exemplo seguinte cria e inicia uma tarefa de migração online com o nome **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Monitorizar a migração

Para monitorizar a migração, efetue as seguintes tarefas.

1. Consolide todos os detalhes de migração numa variável chamada $CheckTask.

    Para combinar os detalhes de migração como propriedades, estaduais e informações de base de dados associadas com a migração, utilize o seguinte fragmento de código:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Utilize o `$CheckTask` variável para obter o estado atual da tarefa de migração.

    Para utilizar o `$CheckTask` variável para obter o estado atual da tarefa de migração, pode monitorizar a tarefa de migração está em execução, consultando a propriedade de estado da tarefa, conforme mostrado no exemplo seguinte:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      write-host "migration task running"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      write-host "Migration task is completed Successfully"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation"  -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      write-host “Migration Task Failed”
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Efetuar as transição (online migrações apenas)

Com uma migração online, é efetuada uma cópia de segurança completa e restaure as bases de dados e, em seguida, o trabalho continua no restauro de registos de transações armazenadas no BackupFileShare.

Quando a base de dados numa instância gerida da base de dados do Azure SQL é atualizado com dados mais recentes e está sincronizada com a base de dados de origem, é possível efetuar uma transferência.

O exemplo seguinte irá concluir a cutover\migration. Os utilizadores invocam este comando a seu critério.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>A eliminar a instância do serviço de migração de base de dados do Azure

Após a migração estiver concluída, pode eliminar a instância do serviço de migração de base de dados do Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre cenários de migração adicionais (pares origem/destino), consulte o Microsoft [guia de migração de bases de dados](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>Passos Seguintes

Obter mais informações sobre o Azure Database Migration Service no artigo [o que é o serviço de migração de base de dados do Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
