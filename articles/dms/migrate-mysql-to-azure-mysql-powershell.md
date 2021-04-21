---
title: 'PowerShell: Executar migração offline da base de dados MySQL para Azure Database for MySQL usando DMS'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar uma base de dados MySQL no local para a Base de Dados Azure para o MySQL utilizando o Serviço de Migração da Base de Dados Azure através do script PowerShell.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 424443288dddcb09d15b7d00ffe9a2840f602959
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819785"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-offline-with-powershell--azure-database-migration-service"></a>Migrar o MySQL para a base de dados Azure para o MySQL offline com o PowerShell & Serviço de Migração da Base de Dados Azure

Neste artigo, migra uma base de dados MySQL restaurada para uma instância no local para Azure Database for MySQL, utilizando a capacidade de migração offline do Azure Database Migration Service através do Microsoft Azure PowerShell. O artigo documenta uma coleção de scripts PowerShell que podem ser executados em sequência para realizar a migração offline da base de dados MySQL para Azure.

> [!NOTE]
> Atualmente não é possível executar a migração completa da base de dados usando o módulo Az.DataMigration. Entretanto, a amostra do script PowerShell é fornecida "as-is" que utiliza a [API de Repouso DMS](https://docs.microsoft.com/rest/api/datamigration/tasks/get) e permite automatizar a migração. Este script será modificado ou depreciado, uma vez que o suporte oficial é adicionado no módulo Az.DataMigration e Azure CLI. 

> [!IMPORTANT]
> O cenário de migração online "MySQL to Azure Database for MySQL" está a ser substituído por um cenário de migração offline paralelo e altamente performante a partir de 1 de junho de 2021. Para migrações on-line, você pode usar esta nova oferta juntamente com [a replicação de dados.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) Em alternativa, utilize ferramentas de código aberto como [MyDumper/MyLoader](https://centminmod.com/mydumper.html) com replicação de dados para migrações online. 

O artigo ajuda a automatizar o cenário em que os nomes das bases de dados de origem e alvo podem ser iguais ou diferentes e como parte da migração, todas ou poucas tabelas na base de dados-alvo precisam de ser migradas com o mesmo nome e estrutura de tabela. Embora os artigos assumam a origem como uma instância de base de dados MySQL e alvo para ser Azure Database para o MySQL, pode ser usado para migrar de uma Base de Dados Azure para MySQL apenas alterando o nome e credenciais do servidor de origem. Além disso, a migração de servidores MySQL de versão inferior (v5.6 e acima) para versões mais altas também é suportada.

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Migrar esquema de base de dados.
> * Crie um grupo de recursos.
> * Criar uma instância do Azure Database Migration Service.
> * Crie um projeto de migração num caso do Serviço de Migração da Base de Dados Azure.
> * Configure o projeto de migração para usar a capacidade de migração offline para o MySQL.
> * Executar a migração.

## <a name="prerequisites"></a>Pré-requisitos

Para completar estes passos, você precisa:

* Tenha uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free)
* Tenha uma base de dados MySQL no local com a versão 5.6 ou superior. Caso contrário, em seguida, faça o download e instale [a edição comunitária mySQL](https://dev.mysql.com/downloads/mysql/) 5.6 ou superior.
* [Criar uma instância na Base de Dados do Azure para MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Consulte o artigo Use a [bancada de trabalho MySQL para ligar e consultar dados](../mysql/connect-workbench.md) para obter detalhes sobre como conectar e criar uma base de dados utilizando a aplicação Workbench. A base de dados Azure para a versão MySQL deve ser igual ou superior à versão MySQL no local . Por exemplo, o MySQL 5.7 pode migrar para a Base de Dados Azure para o MySQL 5.7 ou atualizado para 8.  
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos servidores de origem no local, utilizando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou [o VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração virtual da redeNet, se utilizar o ExpressRoute com o ponto de vista da rede para a Microsoft, adicione o [ponto final](../virtual-network/virtual-network-service-endpoints-overview.md) do serviço *Microsoft.Sql* à sub-rede em que o serviço será alojado. Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet.

* Certifique-se de que as regras do Grupo de Segurança da Rede virtual não bloqueiam a porta de saída 443 do ServiceTag para armazenamento e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Abra a firewall do Windows para permitir que as ligações da Rede Virtual para o Serviço de Migração da Base de Dados Azure acedam à fonte MySQL Server, que por padrão é a porta TCP 3306.
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que as ligações da Rede Virtual para o Serviço de Migração da Base de Dados de Azure acedam à base de dados de origem para migração.
* Crie uma regra de [firewall](../azure-sql/database/firewall-configure.md) ao nível do servidor ou [configuure pontos finais de serviço VNET](../mysql/howto-manage-vnet-using-portal.md) para a base de dados target Azure para o MySQL para permitir o acesso da Rede Virtual para o Serviço de Migração de Bases de Dados Azure às bases de dados-alvo.
* O MySQL de origem tem de estar na edição de comunidade do MySQL suportada. Para determinar a versão da instância do MySQL, no utilitário MySQL ou no MySQL Workbench, execute o seguinte comando:

    ```
    SELECT @@version;
    ```

* A Base de Dados do Azure para MySQL suporta apenas tabelas InnoDB. Para converter tabelas MyISAM para InnoDB, veja o artigo [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) (Converter Tabelas de MyISAM para InnoDB)
* O utilizador deve ter o privilégio de ler dados na base de dados de origem.
* O guia utiliza PowerShell v7.1 com NÚCLEO de Edição PS que pode ser instalado de acordo com o guia de [instalação](/powershell/scripting/install/installing-powershell?view=powershell-7.1&preserve-view=true)
* Descarregue e instale os seguintes módulos da PowerShell Gallery utilizando o [cmdlet Install-Module PowerShell](/powershell/module/powershellget/Install-Module); Certifique-se de abrir a janela de comando PowerShell utilizando o funcionamento como administrador:
    * Az.Resources
    * Az.Network
    * Az.DataMigration

```powershell
Install-Module Az.Resources
Install-Module Az.Network
Install-Module Az.DataMigration
Import-Module Az.Resources
Import-Module Az.Network
Import-Module Az.DataMigration
```

## <a name="migrate-database-schema"></a>Migrar esquema de base de dados

Para transferir todos os objetos da base de dados, como esquemas de mesa, índices e procedimentos armazenados, precisamos extrair esquemas da base de dados de origem e aplicar na base de dados-alvo. Para extrair o esquema, pode utilizar mysqldump com o parâmetro `--no-data`. Para isso, precisa de uma máquina que possa ligar-se tanto à base de dados MySQL de origem como à base de dados Azure target para o MySQL.

Para exportar o esquema utilizando o mysqldump, executar o seguinte comando:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Por exemplo:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Para importar esquemas para direcionar a Base de Dados Azure para o MySQL, executar o seguinte comando:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Por exemplo:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Se tiver chaves estrangeiras no seu esquema, a carga de dados paralela durante a migração será tratada pela tarefa de migração. Não há necessidade de largar chaves estrangeiras durante a migração do esquema.

Se tiver gatilhos na base de dados, irá impor a integridade dos dados no alvo antes da migração completa de dados da fonte. A recomendação é desativar os gatilhos em todas as tabelas do alvo durante a migração e, em seguida, ativar os gatilhos após a migração.

Execute o seguinte script na bancada mySQL workbench na base de dados alvo para extrair o script do gatilho de gota e adicionar script de gatilho.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Executar a consulta gerada do gatilho de queda (coluna DropQuery) no resultado para lançar os gatilhos na base de dados alvo. A consulta de adição do gatilho pode ser guardada, para ser usada após a conclusão da migração de dados.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Faça login na subscrição do Microsoft Azure

Utilize o [comando Connect-AzAccount PowerShell](/powershell/module/az.accounts/connect-azaccount) para iniciar sessão na sua subscrição Azure utilizando o PowerShell, de acordo com as instruções do artigo [Iniciar sessão com Azure PowerShell](/powershell/azure/authenticate-azureps).

O seguinte script define a subscrição predefinida para o início de sessão powerShell e cria uma função de registo de registo de ajuda para registos de consolas formatados.

```powershell
[string] $SubscriptionName = "mySubscription"
$ErrorActionPreference = "Stop";

Connect-AzAccount
Set-AzContext -Subscription $SubscriptionName
$global:currentSubscriptionId = (Get-AzContext).Subscription.Id;

function LogMessage([string] $Message, [bool] $IsProcessing = $false) {
    if ($IsProcessing) {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Yellow
    }
    else {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Green
    }    
}
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

O registo do fornecedor de recursos tem de ser feito apenas uma vez em cada subscrição da Azure. Sem o registo, não poderá criar uma instância do Serviço de **Migração da Base de Dados Azure.**

Registe o fornecedor de recursos utilizando o comando [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider) O seguinte script regista o fornecedor de recursos necessário para **o Serviço de Migração da Base de Dados Azure**

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos antes de criar quaisquer recursos DMS.

Crie um grupo de recursos utilizando o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *oeste dos EUA 2* sob a subscrição padrão *mySubscription*.

```powershell
# Get the details of resource group
[string] $Location = "westus2"
[string] $ResourceGroupName = "myResourceGroup"

$resourceGroup = Get-AzResourceGroup -Name $ResourceGroupName
if (-not($resourceGroup)) {
    LogMessage -Message "Creating resource group $ResourceGroupName..." -IsProcessing $true
    $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
    LogMessage -Message "Created resource group - $($resourceGroup.ResourceId)."
}
else { LogMessage -Message "Resource group $ResourceGroupName exists." }
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Azure Database Migration Service

Pode criar uma nova instância do Serviço de Migração de Bases de Dados Azure utilizando o comando [New-AzDataMigrationService.](/powershell/module/az.datamigration/new-azdatamigrationservice) Este comando espera os seguintes parâmetros necessários:
* *Nome do Grupo de Recursos Azure*. Pode utilizar o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) para criar o grupo de Recursos Azure, como anteriormente mostrado e fornecer o seu nome como parâmetro.
* *Nome de serviço*. Cadeia que corresponde ao nome de serviço único desejado para O Serviço de Migração de Bases de Dados Azure 
* *Localização*. Especifica a localização do serviço. Especificar uma localização do centro de dados Azure, como o Oeste dos EUA ou o Sudeste Asiático
* *Sku.* Este parâmetro corresponde ao nome DMS Sku. O nome Sku atualmente suportado é *Standard_1vCore*, *Standard_2vCores,* *Standard_4vCores*, *Premium_4vCores*.
* *Identificador de sub-rede virtual.* Pode utilizar o comando [Get-AzVirtualNetSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) para obter a informação de uma sub-rede. 

O seguinte script espera que a rede virtual *myVirtualNetwork* exista com uma sub-rede chamada *predefinido* e, em seguida, cria um Serviço de Migração de Bases de Dados com o nome *myDmService* no grupo de recursos criado no **Passo 3** e na mesma região.

```powershell
# Get a reference to the DMS service - Create if not exists
[string] $VirtualNetworkName = "myVirtualNetwork"
[string] $SubnetName = "default"
[string] $ServiceName = "myDmService"

$dmsServiceResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$ResourceGroupName/providers/Microsoft.DataMigration/services/$ServiceName"
$dmsService = Get-AzResource -ResourceId $dmsServiceResourceId -ErrorAction SilentlyContinue

# Create Azure DMS service if not existing
# Possible values for SKU currently are Standard_1vCore,Standard_2vCores,Standard_4vCores,Premium_4vCores
if (-not($dmsService)) {   
    $virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VirtualNetworkName
    if (-not ($virtualNetwork)) { throw "ERROR: Virtual Network $VirtualNetworkName does not exists" }

    $subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $virtualNetwork -Name $SubnetName
    if (-not ($subnet)) { throw "ERROR: Virtual Network $VirtualNetworkName does not contains Subnet $SubnetName" }

    LogMessage -Message "Creating Azure Data Migration Service $ServiceName..." -IsProcessing $true
    $dmsService = New-AzDataMigrationService `
        -ResourceGroupName $ResourceGroupName `
        -Name $ServiceName `
        -Location $resourceGroup.Location `
        -Sku Premium_4vCores `
        -VirtualSubnetId $Subnet.Id
    
    $dmsService = Get-AzResource -ResourceId $dmsServiceResourceId
    LogMessage -Message "Created Azure Data Migration Service - $($dmsService.ResourceId)."
}
else { LogMessage -Message "Azure Data Migration Service $ServiceName exists." }
```

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois de criar uma instância do Serviço de Migração da Base de Dados Azure, irá criar um projeto de migração. Um projeto de migração especifica o tipo de migração que tem de ser feita.

O seguinte script cria um projeto de migração chamado *myfirstmysqlofflineproject* para migração offline do MySQL para Azure Database for MySQL ao abrigo da instância do Serviço de Migração da Base de Dados criada no **Passo 4** e na mesma região.

```powershell
# Get a reference to the DMS project - Create if not exists
[string] $ProjectName = "myfirstmysqlofflineproject"

$dmsProjectResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($dmsService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($dmsService.Name)/projects/$projectName"
$dmsProject = Get-AzResource -ResourceId $dmsProjectResourceId -ErrorAction SilentlyContinue

# Create Azure DMS Project if not existing
if (-not($dmsProject)) {
    LogMessage -Message "Creating Azure DMS project $projectName for MySQL migration ..." -IsProcessing $true

    $newProjectProperties = @{"sourcePlatform" = "MySQL"; "targetPlatform" = "AzureDbForMySQL" }
    $dmsProject = New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $dmsService.Location `
        -ResourceId $dmsProjectResourceId `
        -Properties $newProjectProperties `
        -Force

    LogMessage -Message "Created Azure DMS project $projectName - $($dmsProject.ResourceId)."
}
else { LogMessage -Message "Azure DMS project $projectName exists." }
```

## <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de informação de ligação de base de dados para as ligações de origem e alvo

Após a criação do projeto de migração, irá criar a informação de ligação da base de dados. Esta informação de ligação será usada para ligar aos servidores de origem e alvo durante o processo de migração.

O seguinte script toma o nome do servidor, nome de utilizador e palavra-passe para a origem e direciona as instâncias mySQL e cria os objetos de informação de ligação. O script solicita ao utilizador que introduza a palavra-passe para a origem e direcione as instâncias mySQL. Para scripts silenciosos, as credenciais podem ser recolhidas a partir do Cofre da Chave Azure. 

```powershell
# Initialize the source and target database server connections
[string] $SourceServerName = "13.66.136.192"
[string] $SourceUserName = "docadmin@mysqlserver"
[securestring] $SourcePassword = Read-Host "Enter MySQL Source Server Password" -AsSecureString

[string] $TargetServerName = "migdocdevwus2mysqlsstrgt.mysql.database.azure.com"
[string] $TargetUserName = "docadmin@migdocdevwus2mysqlsstrgt"
[securestring] $TargetPassword = Read-Host "Enter MySQL Target Server Password" -AsSecureString

function InitConnection(
    [string] $ServerName,
    [string] $UserName,
    [securestring] $Password) {
    $connectionInfo = @{
        "dataSource"             = "";
        "serverName"             = "";
        "port"                   = 3306;
        "userName"               = "";
        "password"               = "";
        "authentication"         = "SqlAuthentication";
        "encryptConnection"      = $true;
        "trustServerCertificate" = $true;
        "additionalSettings"     = "";
        "type"                   = "MySqlConnectionInfo" 
    }

    $connectionInfo.dataSource = $ServerName;
    $connectionInfo.serverName = $ServerName;
    $connectionInfo.userName = $UserName;
    $connectionInfo.password = (ConvertFrom-SecureString -AsPlainText $password).ToString();
    $connectionInfo;
}

# Initialize the source and target connections
LogMessage -Message "Initializing source and target connection objects ..." -IsProcessing $true
$sourceConnInfo = InitConnection `
    $SourceServerName `
    $SourceUserName `
    $SourcePassword;

$targetConnInfo = InitConnection `
    $TargetServerName `
    $TargetUserName `
    $TargetPassword;

LogMessage -Message "Source and target connection object initialization complete."
```

## <a name="extract-the-list-of-table-names-from-the-target-database"></a>Extrair a lista de nomes de tabelas da base de dados-alvo

A lista de tabelas de bases de dados pode ser extraída usando uma informação de migração e ligação. A lista de tabelas será extraída tanto da base de dados de origem como da base de dados-alvo, de modo a que o mapeamento e validação adequados possam ser feitos. 

O seguinte script pega nos nomes das bases de dados de origem e alvo e, em seguida, extrai a lista de tabelas das bases de dados utilizando a tarefa de migração *GetUserTablesMySql.* 

```powershell
# Run scenario to get the tables from the target database to build
# the migration table mapping
[string] $TargetDatabaseName = "migtargetdb"
[string] $SourceDatabaseName = "migsourcedb"

function RunScenario([object] $MigrationService, 
    [object] $MigrationProject, 
    [string] $ScenarioTaskName, 
    [object] $TaskProperties, 
    [bool] $WaitForScenario = $true) {
    # Check if the scenario task already exists, if so remove it
    LogMessage -Message "Removing scenario if already exists..." -IsProcessing $true
    Remove-AzDataMigrationTask `
        -ResourceGroupName $MigrationService.ResourceGroupName `
        -ServiceName $MigrationService.Name `
        -ProjectName $MigrationProject.Name `
        -TaskName $ScenarioTaskName `
        -Force;

    # Start the new scenario task using the provided properties
    LogMessage -Message "Initializing scenario..." -IsProcessing $true
    New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $MigrationService.Location `
        -ResourceId "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($MigrationService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($MigrationService.Name)/projects/$($MigrationProject.Name)/tasks/$($ScenarioTaskName)" `
        -Properties $TaskProperties `
        -Force | Out-Null;
    
    LogMessage -Message "Waiting for $ScenarioTaskName scenario to complete..." -IsProcessing $true
    if ($WaitForScenario) {
        $progressCounter = 0;
        do {
            if ($null -ne $scenarioTask) {
                Start-Sleep 10;
            }

            # Get calls can time out and will return a cancellation exception in that case
            $scenarioTask = Get-AzDataMigrationTask `
                -ResourceGroupName $MigrationService.ResourceGroupName `
                -ServiceName $MigrationService.Name `
                -ProjectName $MigrationProject.Name `
                -TaskName $ScenarioTaskName `
                -Expand `
                -ErrorAction Ignore;

            Write-Progress -Activity "Scenario Run $ScenarioTaskName  (Marquee Progress Bar)" `
                -Status $scenarioTask.ProjectTask.Properties.State `
                -PercentComplete $progressCounter
            
            $progressCounter += 10;
            if ($progressCounter -gt 100) { $progressCounter = 10 }
        }
        while (($null -eq $scenarioTask) -or ($scenarioTask.ProjectTask.Properties.State -eq "Running") -or ($scenarioTask.ProjectTask.Properties.State -eq "Queued"))
    }
    Write-Progress -Activity "Scenario Run $ScenarioTaskName" `
        -Status $scenarioTask.ProjectTask.Properties.State `
        -Completed
                 
    # Now get it using REST APIs so we can expand the output  
    LogMessage -Message "Getting expanded task results ..." -IsProcessing $true  
    $psToken = (Get-AzAccessToken -ResourceUrl https://management.azure.com).Token;
    $token = ConvertTo-SecureString -String $psToken -AsPlainText -Force;
    $taskResource = Invoke-RestMethod `
        -Method GET `
        -Uri "https://management.azure.com$($scenarioTask.ProjectTask.Id)?api-version=2018-03-31-preview&`$expand=output" `
        -ContentType "application/json" `
        -Authentication Bearer `
        -Token $token;
    
    $taskResource.properties;
}

# create the get table task properties by initializing the connection and 
# database name
$getTablesTaskProperties = @{
    "input"    = @{
        "connectionInfo"    = $null;
        "selectedDatabases" = $null;
    };
    "taskType" = "GetUserTablesMySql";
};

LogMessage -Message "Running scenario to get the list of tables from the target database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $targetConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($TargetDatabaseName);
# Create a name for the task
$getTableTaskName = "$($TargetDatabaseName)GetUserTables"
# Get the list of tables from the source
$getTargetTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getTargetTablesTask)) { throw "ERROR: Could not get target database $TargetDatabaseName table information." }
LogMessage -Message "List of tables from the target database acquired."

LogMessage -Message "Running scenario to get the list of tables from the source database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $sourceConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($SourceDatabaseName);
# Create a name for the task
$getTableTaskName = "$($SourceDatabaseName)GetUserTables"
# Get the list of tables from the source
$getSourceTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getSourceTablesTask)) { throw "ERROR: Could not get source database $SourceDatabaseName table information." }
LogMessage -Message "List of tables from the source database acquired."

```

## <a name="build-table-mapping-based-on-user-configuration"></a>Construa mapeamento de mesa com base na configuração do utilizador

Como parte da configuração da tarefa de migração, irá criar um mapeamento entre as tabelas de origem e alvo. O mapeamento está ao nível do nome da tabela, mas o pressuposto é que a estrutura da tabela (contagem de colunas, nomes de colunas, tipos de dados, etc.) das tabelas mapeadas é exatamente a mesma.

O seguinte script cria um mapeamento baseado na lista de tabelas de alvo e origem extraída no **passo 7**. Para a carga parcial de dados, o utilizador pode fornecer uma lista de tabelas para filtrar as tabelas. Se não for fornecida nenhuma entrada do utilizador, todas as tabelas-alvo estão mapeadas. O script também verifica se existe ou não uma tabela com o mesmo nome na fonte. Se a nomea de mesa não existir na fonte, então a tabela-alvo é ignorada para a migração. 

```powershell
# Create the source to target table map
# Optional table settings
# DEFAULT: $IncludeTables = $null => include all tables for migration
# DEFAULT: $ExcludeTables = $null => exclude no tables from migration
# Exclude list has higher priority than include list
# Array of qualified source table names which should be migrated
[string[]] $IncludeTables = @("migsourcedb.coupons", "migsourcedb.daily_cash_sheets");
[string[]] $ExcludeTables = $null;

LogMessage -Message "Creating the table map based on the user input and database table information ..." `
    -IsProcessing $true

$targetTables = $getTargetTablesTask.Output.DatabasesToTables."$TargetDatabaseName";
$sourceTables = $getSourceTablesTask.Output.DatabasesToTables."$SourceDatabaseName";
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]';

$schemaPrefixLength = $($SourceDatabaseName + ".").Length;
$tableMappingError = $false
foreach ($srcTable in $sourceTables) {
    # Removing the database name prefix from the table name so that comparison
    # can be done in cases where database name given are different
    $tableName = $srcTable.Name.Substring($schemaPrefixLength, `
            $srcTable.Name.Length - $schemaPrefixLength)

    # In case the table is part of exclusion list then ignore the table
    if ($null -ne $ExcludeTables -and $ExcludeTables -contains $srcTable.Name) {
        continue;
    }

    # Either the include list is null or the table is part of the include list then add it in the mapping
    if ($null -eq $IncludeTables -or $IncludeTables -contains $srcTable.Name) {
        # Check if the table exists in the target. If not then log TABLE MAPPING ERROR
        if (-not ($targetTables | Where-Object { $_.name -ieq "$($TargetDatabaseName).$tableName" })) {
            $tableMappingError = $true
            Write-Host "TABLE MAPPING ERROR: $($targetTables.name) does not exists in target." -ForegroundColor Red
            continue;
        }  

        $tableMap.Add("$($SourceDatabaseName).$tableName", "$($TargetDatabaseName).$tableName");
    }     
}

# In case of any table mapping errors identified, throw an error and stop the process
if ($tableMappingError) { throw "ERROR: One or more table mapping errors were identified. Please see previous messages." }
# In case no tables are in the mapping then throw error
if ($tableMap.Count -le 0) { throw "ERROR: Could not create table mapping." }
LogMessage -Message "Migration table mapping created for $($tableMap.Count) tables."
```

## <a name="create-and-configure-the-migration-task-inputs"></a>Criar e configurar as entradas de tarefas de migração

Depois de construir o mapeamento da mesa, irá criar as entradas para a tarefa de migração do tipo *Migrate.MySql.AzureDbForMySql* e configurar as propriedades.

O seguinte script cria a tarefa de migração e define as ligações, nomes de bases de dados e mapeamento de tabelas.

```powershell
# Create and configure the migration scenario based on the connections
# and the table mapping
$offlineMigTaskProperties = @{
    "input"    = @{
        "sourceConnectionInfo"  = $null;
        "targetConnectionInfo"  = $null;
        "selectedDatabases"     = $null;
        "optionalAgentSettings" = @{
            "EnableCacheBatchesInMemory"         = $true;
            "DisableIncrementalRowStatusUpdates" = $true;
        };
        "startedOn"             = $null;
    };
    "taskType" = "Migrate.MySql.AzureDbForMySql";
};
$offlineSelectedDatabase = @{
    "name"               = $null;
    "targetDatabaseName" = $null;
    "tableMap"           = $null;
};

LogMessage -Message "Preparing migration scenario configuration ..." -IsProcessing $true

# Select the database to be migrated
$offlineSelectedDatabase.name = $SourceDatabaseName;
$offlineSelectedDatabase.tableMap = New-Object PSObject -Property $tableMap;
$offlineSelectedDatabase.targetDatabaseName = $TargetDatabaseName;

# Set connection info and the database mapping
$offlineMigTaskProperties.input.sourceConnectionInfo = $sourceConnInfo;
$offlineMigTaskProperties.input.targetConnectionInfo = $targetConnInfo;
$offlineMigTaskProperties.input.selectedDatabases = @($offlineSelectedDatabase);
$offlineMigTaskProperties.input.startedOn = [System.DateTimeOffset]::UtcNow.ToString("O");
```

## <a name="configure-performance-tuning-parameters"></a>Configurar parâmetros de afinação do desempenho

Como pert do módulo PowerShell, existem poucos parâmetros opcionais disponíveis, que podem ser sintonizados com base no ambiente. Estes parâmetros podem ser usados para melhorar o desempenho da tarefa de migração. Todos estes parâmetros são opcionais e o seu valor predefinido é NU.

> [!NOTE]
> As seguintes configurações de desempenho mostraram um aumento da produção durante a migração no SKU Premium.
> * EscreverDataRangeBatchTaskCount = 12
> * DelayProgressUpdatesInStorageInterval = 30 segundos
> * ThrottleQueryTableDataRangeTaskAtBatchCount = 36

O seguinte script pega nos valores do utilizador dos parâmetros e define os parâmetros nas propriedades da tarefa de migração.

```powershell
# Setting optional parameters from fine tuning the data transfer rate during migration
# DEFAULT values for all the configurations is $null
LogMessage -Message "Adding optional migration performance tuning configuration ..." -IsProcessing $true
# Partitioning settings
# Optional setting that configures the maximum number of parallel reads on tables located on the source database.
[object] $DesiredRangesCount = 4
# Optional setting that configures that size of the largest batch that will be committed to the target server.
[object] $MaxBatchSizeKb = 4096
# Optional setting that configures the minimum number of rows in each batch written to the target.
[object] $MinBatchRows = $null
# Task count settings
# Optional setting that configures the number of databases that will be prepared for migration in parallel.
[object] $PrepareDatabaseForBulkImportTaskCount = $null
# Optional setting that configures the number of tables that will be prepared for migration in parallel.
[object] $PrepareTableForBulkImportTaskCount = $null
# Optional setting that configures the number of threads available to read ranges on the source.
[object] $QueryTableDataRangeTaskCount = 8
# Optional setting that configures the number of threads available to write batches to the target.
[object] $WriteDataRangeBatchTaskCount = 12
# Batch cache settings
# Optional setting that configures how much memory will be used to cache batches in memory before reads on the source are throttled.
[object] $MaxBatchCacheSizeMb = $null
# Optional setting that configures the amount of available memory at which point reads on the source will be throttled.
[object] $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb = $null
# Optional setting that configures the number of batches cached in memory that will trigger read throttling on the source.
[object] $ThrottleQueryTableDataRangeTaskAtBatchCount = 36
# Performance settings
# Optional setting that configures the delay between updates of result objects in Azure Table Storage.
[object] $DelayProgressUpdatesInStorageInterval = "00:00:30"

function AddOptionalSetting($optionalAgentSettings, $settingName, $settingValue) {
    # If no value specified for the setting, don't bother adding it to the input
    if ($null -eq $settingValue) {
        return;
    }

    # Add a new property to the JSON object to capture the setting which will be customized
    $optionalAgentSettings | add-member -MemberType NoteProperty -Name $settingName -Value $settingValue
}

# Set any optional settings in the input based on parameters to this cmdlet
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DesiredRangesCount" $DesiredRangesCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchSizeKb" $MaxBatchSizeKb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MinBatchRows" $MinBatchRows;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareDatabaseForBulkImportTaskCount" $PrepareDatabaseForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareTableForBulkImportTaskCount" $PrepareTableForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "QueryTableDataRangeTaskCount" $QueryTableDataRangeTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "WriteDataRangeBatchTaskCount" $WriteDataRangeBatchTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchCacheSizeMb" $MaxBatchCacheSizeMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb" $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtBatchCount" $ThrottleQueryTableDataRangeTaskAtBatchCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DelayProgressUpdatesInStorageInterval" $DelayProgressUpdatesInStorageInterval;
```

## <a name="creating-and-running-the-migration-task"></a>Criar e executar a tarefa de migração

Depois de configurar a entrada para a tarefa, agora a tarefa será criada e executada no agente. O script desencadeia a execução da tarefa e aguarda a conclusão da migração.

O seguinte guião invoca a tarefa de migração configurada e aguarda que esteja concluída.

```powershell
# Running the migration scenario
[string] $TaskName = "mysqlofflinemigrate"

LogMessage -Message "Running data migration scenario ..." -IsProcessing $true
$summary = @{
    "SourceServer"   = $SourceServerName;
    "SourceDatabase" = $SourceDatabaseName;
    "TargetServer"   = $TargetServerName;
    "TargetDatabase" = $TargetDatabaseName;
    "TableCount"     = $tableMap.Count;
    "StartedOn"      = $offlineMigTaskProperties.input.startedOn;
}

Write-Host "Job Summary:" -ForegroundColor Yellow
Write-Host $(ConvertTo-Json $summary) -ForegroundColor Yellow

$migrationResult = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $TaskName `
    -TaskProperties $offlineMigTaskProperties

LogMessage -Message "Migration completed with status - $($migrationResult.state)"
#Checking for any errors or warnings captured by the task during migration
$dbLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "DatabaseLevelOutput" } 
$migrationLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "MigrationLevelOutput" }
if ($dbLevelResult.exceptionsAndWarnings) {
    Write-Host "Following database errors were captured: $($dbLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationLevelResult.exceptionsAndWarnings) {
    Write-Host "Following migration errors were captured: $($migrationLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationResult.errors.details) {
    Write-Host "Following task level migration errors were captured: $($migrationResult.errors.details)" -ForegroundColor Red
}
```

## <a name="deleting-the-database-migration-service"></a>Eliminação do Serviço de Migração de Bases de Dados

O mesmo Serviço de Migração da Base de Dados pode ser utilizado para múltiplas migrações, pelo que a instância criada pode ser reutilizada. Se não continuar a utilizar o Serviço de Migração da Base de Dados, pode eliminar o serviço utilizando o comando [Remove-AzDataMigrationService.](/powershell/module/az.datamigration/remove-azdatamigrationservice?)

O seguinte script elimina a instância do Serviço de Migração da Base de Dados Azure e os seus projetos associados.

```powershell
Remove-AzDataMigrationService -ResourceId $($dmsService.ResourceId)
```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre questões e limitações conhecidas ao realizar migrações utilizando DMS, consulte o artigo [Questões comuns - Azure Database Migration Service](./known-issues-troubleshooting-dms.md).
* Para resolver problemas de conectividade de base de dados de origem durante a utilização de DMS, consulte o artigo [Problemas que ligam bases de dados de origem](./known-issues-troubleshooting-dms-source-connectivity.md).
* Para obter informações sobre o Serviço de Migração da Base de Dados Azure, consulte o artigo [O que é o Serviço de Migração da Base de Dados Azure?](./dms-overview.md)
* Para obter informações sobre a Base de Dados Azure para o MySQL, consulte o artigo [O que é a Base de Dados Azure para o MySQL?](../mysql/overview.md)
* Para tutorial sobre a utilização de DMS via portal, consulte o artigo [Tutorial: Migrar o MySQL para a base de dados Azure para o MySQL offline utilizando DMS](./tutorial-mysql-azure-mysql-offline-portal.md)