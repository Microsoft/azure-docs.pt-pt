---
title: Migrar SQL Server para Instância Gerenciada do Banco de Dados SQL do Azure com o serviço de migração de banco de dados e o PowerShell | Microsoft Docs
description: Saiba como migrar do SQL Server local para o BD SQL do Azure Instância Gerenciada usando Azure PowerShell.
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
ms.openlocfilehash: 426285340a9401aa6c84a7ee07f172eee6791d9e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163962"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Migre SQL Server locais para uma instância gerenciada do banco de dados SQL do Azure usando Azure PowerShell
Neste artigo, você migra o banco de dados **Adventureworks2016** restaurado para uma instância local do SQL Server 2005 ou superior para uma instância gerenciada do banco de dados SQL do Azure usando Microsoft Azure PowerShell. É possível migrar bancos de dados de uma instância de SQL Server local para uma instância gerenciada do banco de dados SQL do Azure usando o módulo `Az.DataMigration` no Microsoft Azure PowerShell.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Crie um grupo de recursos.
> * Crie uma instância do serviço de migração de banco de dados do Azure.
> * Crie um projeto de migração em uma instância do serviço de migração de banco de dados do Azure.
> * Executar a migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo inclui detalhes sobre como executar migrações online e offline.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisará de:

* [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição).
* Uma cópia local do banco de dados **AdventureWorks2016** , que está disponível para download [aqui](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Para habilitar o protocolo TCP/IP, que é desabilitado por padrão com a instalação do SQL Server Express. Habilite o protocolo TCP/IP seguindo o artigo [habilitar ou desabilitar um protocolo de rede do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Para configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma instância gerenciada do banco de dados SQL do Azure. Você pode criar uma instância gerenciada do banco de dados SQL do Azure seguindo os detalhes no artigo [criar uma instância gerenciada do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Para baixar e instalar o [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 ou posterior.
* Uma VNet (rede virtual) do Azure criada usando o modelo de implantação Azure Resource Manager, que fornece o serviço de migração de banco de dados do Azure com conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Uma avaliação completa de seu banco de dados local e migração de esquema usando o Assistente de Migração de Dados, conforme descrito no artigo [executando uma avaliação de migração de SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Para baixar e instalar o módulo `Az.DataMigration` (versão 0.7.2 ou posterior) do Galeria do PowerShell usando o [cmdlet Install-Module PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Para garantir que as credenciais usadas para se conectar à instância de SQL Server de origem tenham a permissão [Control Server](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* Para garantir que as credenciais usadas para se conectar à instância gerenciada do banco de dados SQL do Azure de destino tenham a permissão CONTROL DATABASE nos bancos de dados de instância gerenciada do banco de dados SQL do Azure de destino.

    > [!IMPORTANT]
    > Para migrações online, você deve ler suas credenciais de Azure Active Directory. Para obter mais informações, consulte o artigo [usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Entrar em sua assinatura do Microsoft Azure

Entre em sua assinatura do Azure usando o PowerShell. Para obter mais informações, consulte o artigo [entrar com Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos usando o comando [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) .

O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group na região *leste dos EUA* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do serviço de migração de banco de dados do Azure

Você pode criar uma nova instância do serviço de migração de banco de dados do Azure usando o cmdlet `New-AzDataMigrationService`.
Esse cmdlet espera os seguintes parâmetros obrigatórios:

* *Nome do grupo de recursos do Azure*. Você pode usar [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) comando para criar um grupo de recursos do Azure como mostrado anteriormente e fornecer seu nome como um parâmetro.
* *Nome do serviço*. Cadeia de caracteres que corresponde ao nome de serviço exclusivo desejado para o serviço de migração de banco de dados do Azure.
* *Localização*. Especifica o local do serviço. Especifique um local de data center do Azure, como oeste dos EUA ou sudeste asiático.
* *SKU*. Esse parâmetro corresponde ao nome do SKU do DMS. Atualmente, os nomes de SKU com suporte são *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Identificador de sub-rede virtual*. Você pode usar o cmdlet [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede.

O exemplo a seguir cria um serviço chamado *mydms* no grupo de recursos *MyDMSResourceGroup* localizado na região *leste dos EUA* usando uma rede virtual chamada *MyVNET* e uma sub-rede chamada *mysubnet*.

> [!IMPORTANT]
> O trecho de código a seguir é para uma migração offline, que não requer uma instância do serviço de migração de banco de dados do Azure com base em uma SKU Premium. Para uma migração online, o valor do parâmetro-SKU deve incluir um SKU Premium.

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

Depois de criar uma instância do serviço de migração de banco de dados do Azure, crie um projeto de migração. Um projeto de serviço de migração de banco de dados do Azure requer informações de conexão para as instâncias de origem e de destino, bem como uma lista de bancos de dados que você deseja migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de informações de conexão de banco de dados para as conexões de origem e de destino

Você pode criar um objeto de informações de conexão de banco de dados usando o cmdlet `New-AzDmsConnInfo`, que espera os seguintes parâmetros:

* *ServerType*. O tipo de conexão de banco de dados solicitada, por exemplo, SQL, Oracle ou MySQL. Use o SQL para SQL Server e o SQL do Azure.
* *Fonte de fontes*. O nome ou IP de uma instância do SQL Server ou do banco de dados SQL do Azure.
* *AuthType*. O tipo de autenticação para conexão, que pode ser sqlauthentication ou WindowsAuthentication.
* *TrustServerCertificate*. Esse parâmetro define um valor que indica se o canal é criptografado ao ignorar a cadeia de certificados para validar a confiança. O valor pode ser `$true` ou `$false`.

O exemplo a seguir cria um objeto de informações de conexão para um SQL Server de origem chamado *MySourceSQLServer* usando a autenticação do SQL:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O exemplo a seguir mostra a criação de informações de conexão para um servidor de instância gerenciada do banco de dados SQL do Azure chamado ' targetmanagedinstance.database.windows.net ' usando a autenticação do SQL:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer bancos de dados para o projeto de migração

Crie uma lista de objetos de `AzDataMigrationDatabaseInfo` que especificam bancos de dados como parte do projeto do serviço de migração de banco de dados do Azure, que pode ser fornecido como parâmetro para a criação do projeto. Você pode usar o cmdlet `New-AzDataMigrationDatabaseInfo` para criar `AzDataMigrationDatabaseInfo`.

O exemplo a seguir cria o projeto `AzDataMigrationDatabaseInfo` para o banco de dados **AdventureWorks2016** e o adiciona à lista a ser fornecida como parâmetro para a criação do projeto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto

Por fim, você pode criar um projeto de serviço de migração de banco de dados do Azure chamado *MyDMSProject* localizado no *leste dos EUA* usando `New-AzDataMigrationProject` e adicionar as conexões de origem e de destino criadas anteriormente e a lista de bancos de dados a serem migrados.

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

Em seguida, crie e inicie uma tarefa do serviço de migração de banco de dados do Azure. Essa tarefa requer informações de credenciais de conexão para a origem e o destino, bem como a lista de tabelas de banco de dados a serem migradas e as informações já fornecidas com o projeto criado como um pré-requisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credencial para origem e destino

Crie credenciais de segurança de conexão como um objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) .

O exemplo a seguir mostra a criação de objetos *PSCredential* para as conexões de origem e de destino, fornecendo senhas como variáveis de cadeia de caracteres *$sourcePassword* e *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Criar um objeto FileShare de backup

Agora crie um objeto FileShare que representa o compartilhamento de rede SMB local para o qual o serviço de migração de banco de dados do Azure pode pegar os backups de banco de dados de origem usando o cmdlet `New-AzDmsFileShare`

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Criar objeto de banco de dados selecionado

A próxima etapa é selecionar os bancos de dados de origem e de destino usando o cmdlet `New-AzDmsSelectedDB`.

O exemplo a seguir é para migrar um banco de dados individual do SQL Server para uma instância gerenciada do banco de dados SQL do Azure:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Se uma instância de SQL Server inteira precisar de um deslocamento e mudança para uma instância gerenciada do banco de dados SQL do Azure, um loop para obter todos os bancos de dados da fonte será fornecido abaixo. No exemplo a seguir, para $Server, $SourceUserName e $SourcePassword, forneça os detalhes de SQL Server de origem.

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

### <a name="sas-uri-for-azure-storage-container"></a>URI de SAS para o contêiner de armazenamento do Azure

Crie a variável que contém o URI de SAS que fornece ao serviço de migração de banco de dados do Azure acesso ao contêiner da conta de armazenamento para o qual o serviço carrega os arquivos de backup.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Requisitos de configuração adicionais

Há alguns requisitos adicionais que você precisa abordar, mas eles diferem dependendo se você está executando uma migração offline ou online.

#### <a name="offline-migrations"></a>Migrações offline

Somente para migrações offline, execute as seguintes tarefas de configuração adicionais.

* **Selecione logons**. Crie uma lista de logons a serem migrados, conforme mostrado no exemplo a seguir:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Atualmente, o serviço de migração de banco de dados do Azure dá suporte apenas à migração de logons SQL

* **Selecione trabalhos do agente**. Crie a lista de trabalhos do Agent a serem migrados, conforme mostrado no exemplo a seguir:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Atualmente, o serviço de migração de banco de dados do Azure só dá suporte a trabalhos com etapas de trabalho do subsistema T-SQL.

#### <a name="online-migrations"></a>Migrações online

Para migrações online somente, execute as seguintes tarefas de configuração adicionais.

* **Configurar Azure Active Directory aplicativo**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Configurar recurso de armazenamento**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Criar e iniciar a tarefa de migração

Use o cmdlet `New-AzDataMigrationTask` para criar e iniciar uma tarefa de migração.

#### <a name="specify-parameters"></a>Especificar parâmetros

##### <a name="common-parameters"></a>Parâmetros comuns

Independentemente de você estar executando uma migração offline ou online, o cmdlet `New-AzDataMigrationTask` espera os seguintes parâmetros:

* *TaskType*. Tipo de tarefa de migração a ser criada para SQL Server para Instância Gerenciada do Banco de Dados SQL do Azure tipo de migração *MigrateSqlServerSqlDbMi* é esperado. 
* *Nome do grupo de recursos*. Nome do grupo de recursos do Azure no qual criar a tarefa.
* *ServiceName*. Instância do serviço de migração de banco de dados do Azure na qual criar a tarefa.
* *ProjectName*. Nome do projeto do serviço de migração de banco de dados do Azure no qual criar a tarefa. 
* *Nome_Tarefa*. Nome da tarefa a ser criada. 
* *SourceConnection*. Objeto AzDmsConnInfo que representa a conexão SQL Server de origem.
* *TargetConnection*. Objeto AzDmsConnInfo que representa a conexão de Instância Gerenciada do Banco de Dados SQL do Azure de destino.
* *SourceCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para se conectar ao servidor de origem.
* *TargetCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para se conectar ao servidor de destino.
* *SelectedDatabase*. Objeto AzDataMigrationSelectedDB que representa o mapeamento de banco de dados de origem e destino.
* *BackupFileShare*. Objeto FileShare que representa o compartilhamento de rede local para o qual o serviço de migração de banco de dados do Azure pode pegar os backups de banco de dados.
* *BackupBlobSasUri*. O URI de SAS que fornece ao serviço de migração de banco de dados do Azure acesso ao contêiner da conta de armazenamento para o qual o serviço carrega os arquivos de backup. Saiba como obter o URI de SAS para o contêiner de BLOB.
* *SelectedLogins*. Lista de logons selecionados a serem migrados.
* *SelectedAgentJobs*. Lista de trabalhos de agente selecionados a serem migrados.

##### <a name="additional-parameters"></a>Parâmetros adicionais

O cmdlet `New-AzDataMigrationTask` também espera parâmetros que são exclusivos para o tipo de migração, offline ou online, que você está executando.

* **Migrações offline**. Para migrações offline, o cmdlet `New-AzDataMigrationTask` também espera os seguintes parâmetros:

  * *SelectedLogins*. Lista de logons selecionados a serem migrados.
  * *SelectedAgentJobs*. Lista de trabalhos de agente selecionados a serem migrados.

* **Migrações online**. Para migrações online, o cmdlet `New-AzDataMigrationTask` também espera os parâmetros a seguir.

* *AzureActiveDirectoryApp*. Active Directory aplicativo.
* *StorageResourceID*. ID de recurso da conta de armazenamento.

#### <a name="create-and-start-an-offline-migration-task"></a>Criar e iniciar uma tarefa de migração offline

O exemplo a seguir cria e inicia uma tarefa de migração offline chamada **myDMSTask**:

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

O exemplo a seguir cria e inicia uma tarefa de migração online chamada **myDMSTask**:

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

Para monitorar a migração, execute as seguintes tarefas.

1. Consolide todos os detalhes de migração em uma variável chamada $CheckTask.

    Para combinar detalhes de migração, como propriedades, estado e informações de banco de dados associadas à migração, use o seguinte trecho de código:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Use a variável `$CheckTask` para obter o estado atual da tarefa de migração.

    Para usar a variável `$CheckTask` para obter o estado atual da tarefa de migração, você pode monitorar a tarefa de migração em execução consultando a propriedade State da tarefa, conforme mostrado no exemplo a seguir:

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

## <a name="performing-the-cutover-online-migrations-only"></a>Executando a transferência (somente migrações online)

Com uma migração online, um backup completo e a restauração de bancos de dados são executados e, em seguida, o trabalho prossegue na restauração dos logs de transações armazenados no BackupFileShare.

Quando o banco de dados em uma instância gerenciada do banco de dados SQL do Azure é atualizado com os últimos data e está em sincronia com o banco de dado de origem, você pode executar uma transferência.

O exemplo a seguir concluirá o cutover\migration. Os usuários chamam esse comando a seu critério.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Excluindo a instância do serviço de migração de banco de dados do Azure

Depois que a migração for concluída, você poderá excluir a instância do serviço de migração de banco de dados do Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre cenários de migração adicionais (pares de origem/destino), consulte o [Guia de migração de banco de dados](https://datamigration.microsoft.com/)da Microsoft.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o serviço de migração de banco de dados do Azure no artigo o [que é o serviço de migração de banco de dados do Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
