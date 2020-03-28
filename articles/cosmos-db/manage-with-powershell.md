---
title: Criar e gerir o Azure Cosmos DB usando o PowerShell
description: Use o Azure Powershell gerir as suas contas Azure Cosmos, bases de dados, contentores e entrada.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: c8e833a4ba18520d8e354398cfd0d00525594d15
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365758"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Gerir os recursos da API Da Azure Cosmos DB SQL utilizando a PowerShell

O guia a seguir descreve como utilizar o PowerShell para gerar scripts e automatizar a gestão de recursos do Azure Cosmos DB, incluindo a conta, a base de dados, o contentor e o débito.

> [!NOTE]
> Amostras neste `Get-AzResource` artigo `Set-AzResource` utilizam e cmdlets Powershell para operações de recursos Azure, bem como cmdlets de gestão [Az.CosmosDB.](https://docs.microsoft.com/powershell/module/az.cosmosdb) `Az.CosmosDB`os cmdlets ainda estão em pré-visualização e podem mudar antes de estarem geralmente disponíveis. Consulte a página de referência da [API Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) para obter quaisquer atualizações aos comandos.

Para ver todas as propriedades que `Get-Resource` / `Set-AzResource` podem ser geridas usando cmdlets PowerShell, consulte o esquema de fornecedor de [recursos da Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Para gestão transversal do `Az` Azure Cosmos DB, `Az.CosmosDB` pode utilizar os cmdlets e cmdlets com [powershell cross-platform](https://docs.microsoft.com/powershell/scripting/install/installing-powershell), bem como o [Azure CLI,](manage-with-cli.md)o [REST API,][rp-rest-api]ou o [portal Azure.](create-sql-api-dotnet.md#create-account)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução

Siga as instruções em Como instalar e configurar o [Azure PowerShell][powershell-install-configure] para instalar e iniciar sessão na sua conta Azure no Powershell.

* `Set-AzureResource`é usado abaixo. Pedirá a confirmação do utilizador.  Se preferir executar sem necessitar de confirmação do utilizador, adere a `-Force` bandeira ao comando.

## <a name="azure-cosmos-accounts"></a>Contas azure Cosmos

As seguintes secções demonstram como gerir a conta Azure Cosmos, incluindo:

* [Criar uma conta do Azure Cosmos](#create-account)
* [Atualizar uma conta Azure Cosmos](#update-account)
* [Listar todas as contas da Azure Cosmos numa subscrição](#list-accounts)
* [Obtenha uma conta Azure Cosmos](#get-account)
* [Apagar uma conta Azure Cosmos](#delete-account)
* [Etiquetas de atualização para uma conta Azure Cosmos](#update-tags)
* [Lista de chaves para uma conta Azure Cosmos](#list-keys)
* [Chaves regeneradas para uma conta Azure Cosmos](#regenerate-keys)
* [Lista de cordas de ligação para uma conta Azure Cosmos](#list-connection-strings)
* [Modificar prioridade de failover para uma conta Azure Cosmos](#modify-failover-priority)
* [Desencadear uma falha manual para uma conta Azure Cosmos](#trigger-manual-failover)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Criar uma conta Azure Cosmos

Este comando cria uma conta de base de dados Azure Cosmos DB com [múltiplas regiões,][distribute-data-globally]falha [automática](how-to-manage-database-account.md#automatic-failover) e política de [consistência](consistency-levels.md)limitada.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000

New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName`O grupo de recursos Azure para implementar a conta Cosmos. Já deve existir.
* `$locations`As regiões da conta de base de dados, a começar pela região de escrita e ordenadas por prioridade de failover.
* `$accountName`O nome da conta azure cosmos. Deve ser único, minúsculo, incluir apenas caracteres alfanuméricos e '-' e entre 3 e 31 caracteres de comprimento.
* `$apiKind`O tipo de conta cosmos para criar. Para mais informações, consulte [APIs em Cosmos DB](introduction.md#develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis).
* `$consistencyPolicy`, `$maxStalenessInterval`e `$maxStalenessPrefix` o nível de consistência padrão e as configurações da conta Azure Cosmos. Para mais informações, consulte [Níveis de Consistência em Azure Cosmos DB](consistency-levels.md).

As contas da Azure Cosmos podem ser configuradas com IP Firewall, pontos finais de serviço de Rede Virtual e pontos finais privados. Para obter informações sobre como configurar o FIREWALL IP para Azure Cosmos DB, consulte [Configure IP Firewall](how-to-configure-firewall.md). Para obter informações sobre como ativar pontos finais de serviço para O Azure Cosmos DB, consulte o [acesso configure a partir de redes virtuais](how-to-configure-vnet-service-endpoint.md). Para obter informações sobre como ativar pontos finais privados para O Azure Cosmos DB, consulte o [acesso configure a partir de pontos finais privados](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a>Listar todas as contas da Azure Cosmos num Grupo de Recursos

Este comando lista todas as contas da Azure Cosmos num Grupo de Recursos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a>Obtenha as propriedades de uma conta Azure Cosmos

Este comando permite-lhe obter as propriedades de uma conta Azure Cosmos existente.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a>Atualizar uma conta Azure Cosmos

Este comando permite-lhe atualizar as propriedades da sua conta de base de dados Azure Cosmos DB. As propriedades que podem ser atualizadas incluem:

* Adicionar ou remover regiões
* Alteração da política de consistência por defeito
* Alteração do filtro de gama IP
* Alterando as configurações da Rede Virtual
* Habilitar o Multi-mestre

> [!NOTE]
> Não é possível adicionar `locations` ou remover simultaneamente regiões e alterar outras propriedades para uma conta Azure Cosmos. As regiões modificantes devem ser executadas como uma operação separada de qualquer outra alteração à conta.
> [!NOTE]
> Este comando permite-lhe adicionar e remover regiões, mas não permite modificar as prioridades de failover ou desencadear uma falha manual. Consulte [modificar a prioridade de failover](#modify-failover-priority) e [acionar o manual de falhas](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Region operations
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$apiVersion = "2020-03-01"

# Add a region to the account
$locations2 = @("West US 2", "East US 2", "South Central US")
$locationObjects2 = @()
$i = 0
ForEach ($location in $locations2) { $locationObjects2 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects2;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove a region from the account
$locations3 = @("West US 2", "South Central US")
$locationObjects3 = @()
$i = 0
ForEach ($location in $locations3) { $locationObjects3 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects3;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```
### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-master"></a>Ativar várias regiões de escrita para uma conta Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-master
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a>Apagar uma conta Azure Cosmos

Este comando elimina uma conta Azure Cosmos existente.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -PassThru
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a>Etiquetas atualizadas de uma conta Azure Cosmos

Este comando define as etiquetas de [recursos Azure][azure-resource-tags] para uma conta Azure Cosmos. As etiquetas podem ser definidas tanto na criação de conta como `New-AzCosmosDBAccount` na atualização da conta utilizando `Update-AzCosmosDBAccount`.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a>Lista de Chaves da Conta

Ao criar uma conta Azure Cosmos, o serviço gera duas chaves de acesso master que podem ser usadas para autenticação quando a conta Azure Cosmos é acedida. São também geradas chaves de leitura para autenticação de operações de leitura.
Ao fornecer duas teclas de acesso, o Azure Cosmos DB permite-lhe regenerar e rodar uma tecla de cada vez sem interrupção na sua conta Azure Cosmos.
As contas do Cosmos DB têm duas teclas de leitura (primária e secundária) e duas chaves de leitura (primária seleto e secundária).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a>Cadeias de ligação de lista

O comando seguinte recupera cordas de ligação para ligar aplicações à conta Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a>Chaves de conta regeneradas

As chaves de acesso a uma conta Azure Cosmos devem ser periodicamente regeneradas para ajudar a manter as ligações seguras. As chaves de acesso primária e secundária são atribuídas à conta. Isto permite que os clientes mantenham o acesso enquanto uma chave de cada vez é regenerada.
Existem quatro tipos de chaves para uma conta Azure Cosmos (Primary, Secondary, PrimaryReadonly e SecondaryReadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a>Ativar falha automática

O comando seguinte define uma conta Cosmos DB para falhar automaticamente na sua região secundária caso a região primária fique indisponível.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-master - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a>Modificar prioridade de failover

Para contas configuradas com Failover Automático, pode alterar a ordem em que a Cosmos promoverá réplicas secundárias para as primárias caso as primárias fiquem indisponíveis.

Para o exemplo abaixo, assuma `West US 2 = 0` `East US 2 = 1`que `South Central US = 2`a atual prioridade de failover é, . O comando vai `West US 2 = 0`mudar `South Central US = 1` `East US 2 = 2`isto para. .

> [!CAUTION]
> Mudar a `failoverPriority=0` localização para irá desencadear uma falha manual para uma conta Azure Cosmos. Quaisquer outras alterações prioritárias não desencadearão uma falha.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a>Falha manual do gatilho

Para contas configuradas com Failover Manual, pode falhar e promover qualquer réplica `failoverPriority=0`secundária para primária modificando para . Esta operação pode ser usada para iniciar um exercício de recuperação de desastres para testar o planeamento de recuperação de desastres.

Para o exemplo abaixo, assuma que `West US 2 = 0` a `East US 2 = 1` conta tem uma prioridade atual de falha e e virar as regiões.

> [!CAUTION]
> A `locationName` `failoverPriority=0` mudança irá desencadear uma falha manual para uma conta Azure Cosmos. Qualquer outra mudança prioritária não irá desencadear uma falha.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

## <a name="azure-cosmos-db-database"></a>Base de Dados Azure Cosmos DB

As seguintes secções demonstram como gerir a base de dados Azure Cosmos DB, incluindo:

* [Criar uma base de dados Azure Cosmos DB](#create-db)
* [Criar uma base de dados Azure Cosmos DB com entrada partilhada](#create-db-ru)
* [Obtenha a entrada de uma base de dados Azure Cosmos DB](#get-db-ru)
* [Listar todas as bases de dados da Azure Cosmos DB numa conta](#list-db)
* [Obtenha uma única base de dados Azure Cosmos DB](#get-db)
* [Eliminar uma base de dados Azure Cosmos DB](#delete-db)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Criar uma base de dados Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Criar uma base de dados Azure Cosmos DB com entrada partilhada

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Obtenha a entrada de uma base de dados Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Obtenha todas as bases de dados do Azure Cosmos DB numa conta

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Obtenha uma única base de dados Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Eliminar uma base de dados Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="azure-cosmos-db-container"></a>Recipiente DB Azure Cosmos

As seguintes secções demonstram como gerir o recipiente Azure Cosmos DB, incluindo:

* [Crie um recipiente Azure Cosmos DB](#create-container)
* [Crie um recipiente Azure Cosmos DB com uma grande chave de partição](#create-container-big-pk)
* [Obtenha a entrada de um recipiente Azure Cosmos DB](#get-container-ru)
* [Crie um recipiente Azure Cosmos DB com indexação personalizada](#create-container-custom-index)
* [Crie um recipiente Azure Cosmos DB com indexação desligada](#create-container-no-index)
* [Crie um recipiente Azure Cosmos DB com chave única e TTL](#create-container-unique-key-ttl)
* [Criar um contentor Azure Cosmos DB com resolução de conflitos](#create-container-lww)
* [Liste todos os contentores DoM Azure Cosmos numa base de dados](#list-containers)
* [Obtenha um único recipiente Azure Cosmos DB numa base de dados](#get-container)
* [Eliminar um recipiente Azure Cosmos DB](#delete-container)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Crie um recipiente Azure Cosmos DB

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Crie um recipiente Azure Cosmos DB com um grande tamanho da chave da divisória

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Obtenha a entrada de um recipiente Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Crie um recipiente Azure Cosmos DB com política de índice personalizado

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Crie um recipiente Azure Cosmos DB com indexação desligada

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Crie um recipiente Azure Cosmos DB com uma política chave única e TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Criar um contentor Azure Cosmos DB com resolução de conflitos

Criar uma política de resolução de conflitos para utilizar um procedimento armazenado, `"mode"="custom"` definir `"conflictResolutionPath"="myResolverStoredProcedure"`e definir o caminho de resolução como o nome do procedimento armazenado, Para escrever todos os conflitos para o ConflictsFeed e lidar separadamente, conjunto `"mode"="custom"` e`"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Liste todos os contentores DoM Azure Cosmos numa base de dados

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Obtenha um único recipiente Azure Cosmos DB numa base de dados

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Eliminar um recipiente Azure Cosmos DB

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

## <a name="next-steps"></a>Passos seguintes

* [Todas as amostras powershell](powershell-samples.md)
* [Como gerir a conta Azure Cosmos](how-to-manage-database-account.md)
* [Crie um recipiente Azure Cosmos DB](how-to-create-container.md)
* [Configure o tempo para viver em Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
