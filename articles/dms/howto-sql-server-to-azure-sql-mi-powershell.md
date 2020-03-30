---
title: 'PowerShell: Migrar o Servidor SQL para a SQL gerido'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar do SQL Server para a Azure SQL Database gerida através da utilização do Azure PowerShell e do Serviço de Migração da Base de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 9ea9f55681b93e79eec836f5808d2c6feaa6bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650729"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>Migrar o Servidor SQL para a Base de Dados SQL gerido com powerShell & Serviço de Migração de Bases de Dados Azure

Neste artigo, migra a base de dados **Adventureworks2016** restaurada para uma instância no local do SQL Server 2005 ou superior a uma base de dados Azure SQL gerida através da utilização do Microsoft Azure PowerShell. Pode migrar bases de dados de uma instância do SQL Server no local para uma `Az.DataMigration` instância gerida pela Azure SQL, utilizando o módulo no Microsoft Azure PowerShell.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Crie um grupo de recursos.
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração em caso de Serviço de Migração de Bases de Dados Azure.
> * Executar a migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo inclui detalhes sobre como realizar migrações online e offline.

## <a name="prerequisites"></a>Pré-requisitos

Para completar estes passos, precisa de:

* [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição).
* Uma cópia local da base de dados **AdventureWorks2016,** que está disponível para download [aqui.](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)
* Para ativar o protocolo TCP/IP, que é desativado por padrão com a instalação Do SQL Server Express. Ative o protocolo TCP/IP seguindo o artigo [Ativar ou Desativar um Protocolo](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)de Rede de Servidores .
* Para configurar o [seu Windows Firewall para acesso](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)ao motor de base de dados .
* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma base de dados Azure SQL gerida. Pode criar uma instância gerida pela Base de Dados Azure SQL seguindo os detalhes do artigo Criar uma instância gerida pela Base de [Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Para descarregar e instalar [o Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou mais tarde.
* Uma rede virtual do Microsoft Azure criada utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece ao Serviço de Migração de Bases de Dados Azure a conectividade site-a-site para os seus servidores de origem no local, utilizando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [o VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Uma avaliação completa da sua base de dados no local e migração de esquemas utilizando o Data Migration Assistant, conforme descrito no artigo Realização de uma avaliação de [migração do Servidor SQL](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Para descarregar e `Az.DataMigration` instalar o módulo (versão 0.7.2 ou posterior) da PowerShell Gallery utilizando o [cmdlet de Instalação powerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Para garantir que as credenciais utilizadas para se ligar à instância de origem do Servidor SQL têm a permissão [CONTROL SERVER.](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)
* Para garantir que as credenciais utilizadas para se ligar em direção à base de dados Azure SQL têm a permissão de BASE DE DADOS DE CONTROLO nas bases de dados geridas pelo Azure SQL.

    > [!IMPORTANT]
    > Para migrações online, já deve ter configurado as suas credenciais de Diretório Ativo Azure. Para mais informações, consulte o artigo [Utilize o portal para criar uma aplicação e diretor de serviço seletiva azure que possa aceder a recursos.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Inscreva-se na subscrição do Microsoft Azure

Inscreva-se na subscrição do Azure utilizando o PowerShell. Para mais informações, consulte o artigo Iniciar sessão com a [Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos Azure é um recipiente lógico no qual os recursos azure são implantados e geridos.

Crie um grupo [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) de recursos utilizando o comando.

O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na região *leste dos EUA.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Serviço de Migração de Bases de Dados Azure

Pode criar uma nova instância do Serviço `New-AzDataMigrationService` de Migração de Bases de Dados Azure utilizando o cmdlet.
Este cmdlet espera os seguintes parâmetros necessários:

* *Nome do Grupo de Recursos Azure*. Pode utilizar [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) o comando para criar um grupo de Recursos Azure, como anteriormente mostrado e fornecer o seu nome como parâmetro.
* *Nome de serviço*. Corda que corresponde ao nome de serviço único desejado para o Serviço de Migração de Bases de Dados Azure.
* *Localização*. Especifica a localização do serviço. Especifique uma localização do centro de dados Azure, como os EUA Ocidentais ou o Sudeste Asiático.
* *Sku.* Este parâmetro corresponde ao nome DMS Sku. Atualmente, os nomes Sku suportados são *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Identificador de sub-rede virtual.* Pode utilizar o cmdlet [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede.

O exemplo seguinte cria um serviço chamado *MyDMS* no grupo de recursos *MyDMSResourceGroup* localizado na região *leste dos EUA* usando uma rede virtual chamada *MyVNET* e uma subnet chamada *MySubnet*.

> [!IMPORTANT]
> O código abaixo é para uma migração offline, que não requer uma instância do Serviço de Migração de Bases de Dados Azure com base num SKU Premium. Para uma migração on-line, o valor do parâmetro -Sku deve incluir um SKU Premium.

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

Depois de criar uma instância azure Database Migration Service, crie um projeto de migração. Um projeto do Serviço de Migração de Bases de Dados Azure requer informações de ligação tanto para as instâncias de origem como para as ocorrências-alvo, bem como uma lista de bases de dados que pretende migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de informação de ligação de base de dados para a origem e ligações-alvo

Pode criar um objeto Informação `New-AzDmsConnInfo` de Ligação à Base de Dados utilizando o cmdlet, que espera os seguintes parâmetros:

* *Tipo de servidor*. O tipo de ligação de base de dados solicitada, por exemplo, SQL, Oracle ou MySQL. Utilize O SQL para O Servidor SQL e o Azure SQL.
* *DataSource*. O nome ou IP de uma instância do Servidor SQL ou da instância de Base de Dados Azure SQL.
* *AuthType*. O tipo de autenticação para ligação, que pode ser SqlAuthentication ou WindowsAuthentication.
* *Certificado trustserver*. Este parâmetro define um valor que indica se o canal está encriptado enquanto contorna a cadeia de certificados para validar a confiança. O valor `$true` pode `$false`ser ou .

O exemplo seguinte cria um objeto De Ligação Informação para uma fonte Do Servidor SQL chamada *MySourceSQLServer* utilizando a autenticação sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O exemplo seguinte mostra a criação de Info de ligação para um servidor de instância gerido por Uma Base de Dados Azure SQL chamado 'targetmanagedinstance.database.windows.net' utilizando a autenticação sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer bases de dados para o projeto de migração

Crie uma `AzDataMigrationDatabaseInfo` lista de objetos que especifiquem bases de dados como parte do projeto do Serviço de Migração da Base de Dados Azure, que pode ser fornecido como parâmetro para a criação do projeto. Pode utilizar o `New-AzDataMigrationDatabaseInfo` cmdlet `AzDataMigrationDatabaseInfo`para criar .

O exemplo seguinte `AzDataMigrationDatabaseInfo` cria o projeto para a base de dados **AdventureWorks2016** e adiciona-o à lista a ser fornecida como parâmetro para a criação de projetos.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto

Finalmente, pode criar um projeto do Serviço de Migração de Bases `New-AzDataMigrationProject` de Dados Azure chamado *MyDMSProject* localizado no Leste dos *EUA* usando e adicionar as ligações de origem e alvo anteriormente criadas e a lista de bases de dados para migrar.

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

Em seguida, crie e inicie uma tarefa do Serviço de Migração de Bases de Dados Azure. Esta tarefa requer informações credenciais de ligação tanto para a origem como para o alvo, bem como para a lista de tabelas de bases de dados a migrar e as informações já fornecidas com o projeto criado como pré-requisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros credenciais para fonte e alvo

Crie credenciais de segurança de ligação como um objeto [PSCredential.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

O exemplo seguinte mostra a criação de objetos *PSCredential* tanto para a origem como para as ligações-alvo, fornecendo palavras-passe como variáveis de cadeia *$sourcePassword* e *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Criar um objeto de backup FileShare

Agora crie um objeto FileShare que represente a quota de rede SMB local `New-AzDmsFileShare` para a qual o Serviço de Migração de Bases de Dados Azure pode levar as cópias de segurança da base de dados de origem utilizando o cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Criar objeto de base de dados selecionado

O próximo passo é selecionar as bases de `New-AzDmsSelectedDB` dados de origem e alvo utilizando o cmdlet.

O exemplo que se segue é a migração de uma única base de dados do SQL Server para uma instância gerida pela Base de Dados Azure SQL:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Se toda uma instância do SQL Server necessitar de um lift-and-shift para uma instância gerida pela Base de Dados SQL Azure, então um loop para retirar todas as bases de dados da fonte é fornecido abaixo. No exemplo seguinte, para $Server, $SourceUserName e $SourcePassword, forneça os detalhes do SQL Server de origem.

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

### <a name="sas-uri-for-azure-storage-container"></a>SAS URI para recipiente de armazenamento azure

Crie variável contendo o SAS URI que fornece ao Serviço de Migração de Bases de Dados Azure o acesso ao contentor da conta de armazenamento para o qual o serviço carrega os ficheiros de backup.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Requisitos adicionais de configuração

Existem alguns requisitos adicionais que precisa de resolver, mas diferem consoante esteja a realizar uma migração offline ou online.

#### <a name="offline-migrations"></a>Migrações offline

Apenas para migrações offline, execute as seguintes tarefas adicionais de configuração.

* **Selecione logins**. Crie uma lista de logins a migrar, como mostra o seguinte exemplo:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Atualmente, o Serviço de Migração de Bases de Dados Azure apenas suporta logins SQL migratórios.

* **Selecione trabalhos de agente**. Criar lista de postos de trabalho dos agentes a migrar, como mostra o seguinte exemplo:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Atualmente, o Azure Database Migration Service apenas suporta postos de trabalho com etapas de emprego do subsistema T-SQL.

#### <a name="online-migrations"></a>Migrações online

Apenas para migrações on-line, execute as seguintes tarefas adicionais de configuração.

* **Configure Azure Ative Directory App**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Configure recurso de armazenamento**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Criar e iniciar a tarefa de migração

Utilize `New-AzDataMigrationTask` o cmdlet para criar e iniciar uma tarefa de migração.

#### <a name="specify-parameters"></a>Especificar parâmetros

##### <a name="common-parameters"></a>Parâmetros comuns

Independentemente de estar a realizar uma migração `New-AzDataMigrationTask` offline ou online, o cmdlet espera os seguintes parâmetros:

* *Tipo de tarefa*. Tipo de tarefa de migração para criar para o Servidor SQL para azure SQL Base de Dados Gerida tipo migração migrar o tipo *migratório MigrateSqlServerSqlDbMi* é esperado. 
* *Nome do grupo de recursos*. Nome do grupo de recursos Azure para criar a tarefa.
* *Nome de serviço*. Instância do Serviço de Migração da Base de Dados Azure para criar a tarefa.
* *Nome do projeto.* Nome do projeto Azure Database Migration Service para criar a tarefa. 
* *Nome de tarefa*. Nome da tarefa a ser criado. 
* *Ligação de origem*. Objeto AzDmsConnInfo representando a ligação fonte Do Servidor SQL.
* *Ligação de alvos*. Objeto AzDmsConnInfo que representa a ligação de instância gerida pela base de dados Azure SQL.
* *FonteCred*. [Objeto PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para ligar ao servidor de origem.
* *TargetCred*. [Objeto PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para ligar ao servidor alvo.
* *Base de Dados Selecionado*. Objeto AzDataMigrationSelectedDB que representa o mapeamento da base de dados de origem e alvo.
* *BackupFileShare*. Objeto FileShare que representa a partilha de rede local a que o Serviço de Migração de Bases de Dados Azure pode levar as cópias de segurança da base de dados de origem.
* *BackupBlobSasUri*. O SAS URI que fornece ao Serviço de Migração de Bases de Dados Azure acesso ao contentor da conta de armazenamento para o qual o serviço envia os ficheiros de backup. Veja Learn how to get the SAS URI for blob container (Saiba como obter o URI da SAS para o contentor de blobs).
* *SelectLogins*. Lista de logins selecionados para migrar.
* *SelectedAgentJobs*. Lista de trabalhos de agente selecionados para migrar.

##### <a name="additional-parameters"></a>Parâmetros adicionais

O `New-AzDataMigrationTask` cmdlet também espera parâmetros que são exclusivos do tipo de migração, offline ou online, que você está realizando.

* **Migrações offline.** Para migrações offline, `New-AzDataMigrationTask` o cmdlet também espera os seguintes parâmetros:

  * *SelectLogins*. Lista de logins selecionados para migrar.
  * *SelectedAgentJobs*. Lista de trabalhos de agente selecionados para migrar.

* **Migrações online.** Para migrações online, `New-AzDataMigrationTask` o cmdlet também espera os seguintes parâmetros.

* *AzureActiveDirectoryApp*. Aplicação de Diretório Ativo.
* *StorageResourceID*. Identificação de recursos da Conta de Armazenamento.

#### <a name="create-and-start-an-offline-migration-task"></a>Criar e iniciar uma tarefa de migração offline

O exemplo seguinte cria e inicia uma tarefa de migração offline chamada **myDMSTask:**

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

O exemplo seguinte cria e inicia uma tarefa de migração on-line chamada **myDMSTask:**

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

Para monitorizar a migração, execute as seguintes tarefas.

1. Consolidar todos os detalhes da migração numa variável chamada $CheckTask.

    Para combinar detalhes da migração, tais como propriedades, informações estatais e de base de dados associadas à migração, utilize o seguinte código:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Use `$CheckTask` a variável para obter o estado atual da tarefa de migração.

    Para utilizar `$CheckTask` a variável para obter o estado atual da tarefa de migração, pode monitorizar a tarefa de migração em execução consultando a propriedade do Estado da tarefa, como mostra o seguinte exemplo:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Realizar o cutover (apenas migrações online)

Com uma migração on-line, é realizada uma cópia de segurança completa e restauro de bases de dados, e, em seguida, trabalhar no restauro dos Registos de Transações armazenados no BackupFileShare.

Quando a base de dados de uma base de dados Azure SQL gerida é atualizada com dados mais recentes e está em sincronização com a base de dados de origem, pode efetuar um cutover.

O exemplo seguinte completará a redução da migração. Os utilizadores invocam este comando a seu critério.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Apagar a instância do Serviço de Migração da Base de Dados Azure

Após a migração estar concluída, pode eliminar a instância do Serviço de Migração da Base de Dados Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre cenários de migração adicionais (par de fonte/alvo), consulte o Guia de [Migração](https://datamigration.microsoft.com/)da Base de Dados da Microsoft .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Serviço de Migração da Base de Dados Azure no artigo O que é o Serviço de Migração da Base de [Dados Azure?](https://docs.microsoft.com/azure/dms/dms-overview)
