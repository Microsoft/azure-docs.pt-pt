---
title: 'PowerShell: Migrar o SqL Server para o SQL Managed Instance offline'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar offline do SQL Server para Azure SQL Managed Instance utilizando a Azure PowerShell e o Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 90663b6beb4f1e3f7ade32e603a53c8b9d9158f5
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697853"
---
# <a name="migrate-sql-server-to-sql-managed-instance-offline-with-powershell--azure-database-migration-service"></a>Migrar o SQL Server para o SQL Gerenciado Instance offline com PowerShell & Serviço de Migração de Bases de Dados Azure

Neste artigo, você offline migrar a base de dados **Adventureworks2016** restaurada para uma instância no local do SQL Server 2005 ou acima para um Azure SQL SQL Managed Instance usando o Microsoft Azure PowerShell. Pode migrar bases de dados de uma instância sql server para uma sql Managed Instance utilizando o `Az.DataMigration` módulo no Microsoft Azure PowerShell.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Crie um grupo de recursos.
> * Crie uma instância do Azure Database Migration Service.
> * Criar um projeto de migração num caso do Serviço de Migração da Base de Dados Azure.
> * Executar a migração offline.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo fornece passos para uma migração offline, mas também é possível migrar [online.](howto-sql-server-to-azure-sql-managed-instance-powershell-online.md)


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


## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Inscreva-se na subscrição do Microsoft Azure

Inscreva-se na sua subscrição Azure utilizando o PowerShell. Para mais informações, consulte o artigo [Iniciar sação com a Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos Azure é um recipiente lógico no qual os recursos da Azure são implantados e geridos.

Criar um grupo de recursos utilizando o [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) comando.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *leste dos EUA.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Azure Database Migration Service

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

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de informação de ligação de base de dados para as ligações de origem e alvo

Pode criar um objeto info de ligação de base de dados utilizando o `New-AzDmsConnInfo` cmdlet, que espera os seguintes parâmetros:

* *ServerType*. O tipo de ligação de base de dados solicitado, por exemplo, SQL, Oracle ou MySQL. Utilize SQL para SQL Server e Azure SQL.
* *DataSource*. O nome ou IP de uma instância sql server ou a instância de base de dados Azure SQL.
* *AuthType.* O tipo de autenticação para ligação, que pode ser ou a Autorização de SqlAuthentication ou a Versão Desafetação do Windows.
* *TrustServerCertificate*. Este parâmetro define um valor que indica se o canal está encriptado enquanto contorna a cadeia de certificados para validar a confiança. O valor pode ser `$true` `$false` ou. .

O exemplo a seguir cria um objeto De Informação de Conexão para um servidor SQL de origem chamado *MySourceSQLServer* utilizando a autenticação sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O exemplo seguinte mostra a criação de Informações de Conexão para uma Instância Gerida Azure SQL denominada "targetmanagedinstance":

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer bases de dados para o projeto de migração

Crie uma lista de `AzDataMigrationDatabaseInfo` objetos que especifica bases de dados como parte do projeto Azure Database Migration Service, que pode ser fornecido como parâmetro para a criação do projeto. Pode utilizar o cmdlet `New-AzDataMigrationDatabaseInfo` para criar `AzDataMigrationDatabaseInfo` .

O exemplo a seguir cria o `AzDataMigrationDatabaseInfo` projeto para a base de dados **AdventureWorks2016** e adiciona-o à lista a fornecer como parâmetro para a criação de projetos.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto

Finalmente, pode criar um projeto do Azure Database Migration Service chamado *MyDMSProject* localizado nos *EUA orientais* usando `New-AzDataMigrationProject` e adicionar as ligações de origem e destino previamente criadas e a lista de bases de dados para migrar.

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

Em seguida, crie e inicie uma tarefa do Serviço de Migração da Base de Dados Azure. Esta tarefa requer informações credenciais de ligação tanto para a origem como para o alvo, bem como a lista de tabelas de bases de dados a migrar e as informações já fornecidas com o projeto criado como pré-requisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credencial para a origem e o alvo

Crie credenciais de segurança de ligação como um objeto [PSCredential.](/dotnet/api/system.management.automation.pscredential)

O exemplo a seguir mostra a criação de objetos *PSCredential* para as ligações de origem e alvo, fornecendo palavras-passe como variáveis de cordas *$sourcePassword* e *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Criar um objeto de backup FileShare

Agora crie um objeto FileShare que represente a partilha de rede SMB local para a qual o Azure Database Migration Service pode obter as cópias de dados de base de dados de origem utilizando o `New-AzDmsFileShare` cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Criar objeto de base de dados selecionado

O próximo passo é selecionar as bases de dados de origem e alvo utilizando o `New-AzDmsSelectedDB` cmdlet.

O exemplo a seguir é a migração de uma única base de dados do SQL Server para uma Instância Gerida Azure SQL:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Se uma instância inteira do SQL Server necessitar de um lift-and-shift para uma Instância Gerida Azure SQL, então um loop para tirar todas as bases de dados da fonte é fornecido abaixo. No exemplo seguinte, para $Server, $SourceUserName e $SourcePassword, forneça os detalhes do seu SqL Server de origem.

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

### <a name="sas-uri-for-azure-storage-container"></a>SAS URI para recipiente de armazenamento Azure

Criar variável contendo o SAS URI que fornece ao Serviço de Migração da Base de Dados Azure o acesso ao contentor da conta de armazenamento para o qual o serviço envia os ficheiros de backup.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> O Serviço de Migração de Bases de Dados Azure não suporta a utilização de um token SAS de nível de conta. Deve utilizar um SAS URI para o recipiente da conta de armazenamento. Veja [Learn how to get the SAS URI for blob container](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container) (Saiba como obter o URI da SAS para o contentor de blobs).

### <a name="additional-configuration-requirements"></a>Requisitos adicionais de configuração

Existem alguns requisitos adicionais que precisa de abordar:


* **Selecione logins**. Crie uma lista de logins a migrar como mostrado no seguinte exemplo:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Atualmente, o Azure Database Migration Service apenas suporta logins SQL migratórios.

* **Selecione trabalhos de agente**. Criar uma lista de empregos de agentes a migrar, como mostra o seguinte exemplo:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Atualmente, o Azure Database Migration Service apenas suporta empregos com passos de trabalho do subsistema T-SQL.



### <a name="create-and-start-the-migration-task"></a>Criar e iniciar a tarefa de migração

Utilize o `New-AzDataMigrationTask` cmdlet para criar e iniciar uma tarefa de migração.

#### <a name="specify-parameters"></a>Especificar parâmetros

O `New-AzDataMigrationTask` cmdlet espera os seguintes parâmetros:

* *TaskType*. É esperado o tipo de tarefa de migração para criar para o SQL Server para Azure SQL Managed Instance do tipo *migração MigrateSqlServerSqlDbMi.* 
* *Nome do grupo de recursos*. Nome do grupo de recursos Azure para criar a tarefa.
* *Nome de serviço*. Exemplo do Serviço de Migração da Base de Dados Azure para criar a tarefa.
* *Nome do Projeto*. Nome do projeto Azure Database Migration Service para criar a tarefa. 
* *Nome de tarefas*. Nome da tarefa a ser criada. 
* *FonteConnection*. Objeto AzDmsConnInfo que representa a ligação do Servidor SQL de origem.
* *TargetConnection*. Objeto AzDmsConnInfo que representa a ligação target Azure SQL Managed Instance.
* *SourceCred*. [Objeto PSCredential](/dotnet/api/system.management.automation.pscredential) para ligar ao servidor de origem.
* *TargetCred*. [Objeto PSCredential](/dotnet/api/system.management.automation.pscredential) para ligar ao servidor alvo.
* *Database selecionada*. AzDataMigrationSelectedDB objeto que representa o mapeamento da base de dados de origem e alvo.
* *BackupFileShare*. O objeto FileShare que representa a partilha de rede local para a ações da Azure Database Migration Service pode levar as cópias de dados de origem.
* *BackupBlobSasUri*. O SAS URI que fornece ao Serviço de Migração da Base de Dados Azure acesso ao contentor da conta de armazenamento para o qual o serviço envia os ficheiros de backup. Veja Learn how to get the SAS URI for blob container (Saiba como obter o URI da SAS para o contentor de blobs).
* *SelecionadosLogins*. Lista de logins selecionados para migrar.
* *Selecionados AgentJobs*. Lista de trabalhos de agente selecionados para migrar.
* *SelecionadosLogins*. Lista de logins selecionados para migrar.
* *Selecionados AgentJobs*. Lista de trabalhos de agente selecionados para migrar.



#### <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração

O exemplo a seguir cria e inicia uma tarefa de migração offline chamada **myDMSTask:**

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


## <a name="monitor-the-migration"></a>Monitorizar a migração

Para monitorizar a migração, execute as seguintes tarefas.

1. Consolidar todos os detalhes da migração numa variável chamada $CheckTask.

    Para combinar detalhes de migração, tais como propriedades, estado e informações de base de dados associadas à migração, utilize o seguinte código:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Use a `$CheckTask` variável para obter o estado atual da tarefa de migração.

    Para utilizar a `$CheckTask` variável para obter o estado atual da tarefa de migração, pode monitorizar a tarefa de migração em execução consultando a propriedade do Estado da tarefa, como mostra o seguinte exemplo:

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


## <a name="delete-the-instance-of-azure-database-migration-service"></a>Eliminar a instância do Serviço de Migração da Base de Dados de Azure

Após a conclusão da migração, pode eliminar a instância do Serviço de Migração da Base de Dados Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```


## <a name="next-steps"></a>Próximos passos

Saiba mais sobre o Serviço de Migração da Base de Dados Azure no artigo [O que é o Serviço de Migração de Bases de Dados Azure?](./dms-overview.md)

Para obter informações sobre cenários migratórios adicionais (pares de origem/alvo), consulte o [Guia de Migração da Base de Dados da](https://datamigration.microsoft.com/)Microsoft .