---
title: Gerir recursos API Azure Cosmos DB Core (SQL) utilizando o PowerShell
description: Gerir os recursos API da Azure Cosmos DB Core (SQL) utilizando o PowerShell.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: c56f3b561dbced9f00bb2616f5709a51fa02fbcb
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92481011"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>Gerir recursos API DB Core (SQL) da Azure Cosmos (SQL) utilizando o PowerShell

O seguinte guia descreve como usar o PowerShell para script e automatizar a gestão dos recursos API do Azure Cosmos DB Core (SQL), incluindo a conta Cosmos, base de dados, contentor e produção. Para os cmdlets PowerShell para outras APIs ver [amostras de powerShell para Cassandra,](powershell-samples-cassandra.md) [Amostras de PowerShell para API mongodb,](powershell-samples-mongodb.md) [amostras de energia para Gremlin,](powershell-samples-gremlin.md) [Amostras de Energia para tabela](powershell-samples-table.md)

> [!NOTE]
> As amostras deste artigo utilizam cmdlets de gestão [Az.CosmosDB.](/powershell/module/az.cosmosdb) Consulte a página de referência da [Az.CosmosDB](/powershell/module/az.cosmosdb) para obter as últimas alterações.

Para a gestão transversal da Azure Cosmos DB, pode utilizar os `Az` `Az.CosmosDB` cmdlets e os cmdlets com [plataformas cruzadas PowerShell,](/powershell/scripting/install/installing-powershell)bem como o [Azure CLI,](manage-with-cli.md)a [API REST,][rp-rest-api]ou o [portal Azure.](create-sql-api-dotnet.md#create-account)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução

Siga as instruções em [Como instalar e configurar a Azure PowerShell][powershell-install-configure] para instalar e iniciar sôm na sua conta Azure no PowerShell.

> [!IMPORTANT]
> Os recursos DB da Azure Cosmos não podem ser renomeados, uma vez que isto viola a forma como o Azure Resource Manager funciona com URIs de recursos.

## <a name="azure-cosmos-accounts"></a>Contas de Azure Cosmos

As seguintes secções demonstram como gerir a conta Azure Cosmos, incluindo:

* [Criar uma conta do Azure Cosmos](#create-account)
* [Atualize uma conta Azure Cosmos](#update-account)
* [Listar todas as contas da Azure Cosmos numa subscrição](#list-accounts)
* [Obtenha uma conta Azure Cosmos](#get-account)
* [Apagar uma conta Azure Cosmos](#delete-account)
* [Atualização de etiquetas para uma conta Azure Cosmos](#update-tags)
* [Listar chaves para uma conta Azure Cosmos](#list-keys)
* [Chaves regeneração para uma conta Azure Cosmos](#regenerate-keys)
* [Listar cordas de conexão para uma conta Azure Cosmos](#list-connection-strings)
* [Modifique a prioridade de failover para uma conta Azure Cosmos](#modify-failover-priority)
* [Desencadear uma falha manual para uma conta Azure Cosmos](#trigger-manual-failover)
* [Listar bloqueios de recursos numa conta DB da Azure Cosmos](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> Criar uma conta Azure Cosmos

Este comando cria uma conta de base de dados DB Azure Cosmos com [múltiplas regiões,][distribute-data-globally] [falha automática](how-to-manage-database-account.md#automatic-failover) e política de [consistência](consistency-levels.md)limitada.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName` O grupo de recursos Azure para implantar a conta Cosmos. Já deve existir.
* `$locations` As regiões para a conta de base de dados, a região com `FailoverPriority 0` é a região de escrita.
* `$accountName` O nome da conta de Azure Cosmos. Deve ser único, minúsculo, incluir apenas caracteres alfanuméricos e '-', e entre 3 e 31 caracteres de comprimento.
* `$apiKind` O tipo de conta Cosmos para criar. Para mais informações, consulte [APIs em Cosmos DB](introduction.md#develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis).
* `$consistencyPolicy`, `$maxStalenessInterval` e `$maxStalenessPrefix` o nível de consistência padrão e as configurações da conta Azure Cosmos. Para mais informações, consulte [os Níveis de Consistência em Azure Cosmos DB](consistency-levels.md).

As contas do Azure Cosmos podem ser configuradas com IP Firewall, pontos finais de serviço de rede virtual e pontos finais privados. Para obter informações sobre como configurar a firewall IP para Azure Cosmos DB, consulte [Configure IP Firewall](how-to-configure-firewall.md). Para obter informações sobre como ativar os pontos finais do serviço para Azure Cosmos DB, consulte [o acesso configure a partir de Redes virtuais](how-to-configure-vnet-service-endpoint.md). Para obter informações sobre como permitir pontos finais privados para Azure Cosmos DB, consulte [o acesso de configuração a partir de pontos finais privados](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> Listar todas as contas da Azure Cosmos num Grupo de Recursos

Este comando lista todas as contas da Azure Cosmos num Grupo de Recursos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> Obtenha as propriedades de uma conta Azure Cosmos

Este comando permite-lhe obter as propriedades de uma conta Azure Cosmos existente.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Atualize uma conta Azure Cosmos

Este comando permite-lhe atualizar as propriedades da sua conta de base de dados Azure Cosmos DB. As propriedades que podem ser atualizadas incluem o seguinte:

* Adicionar ou remover regiões
* Alterar a política de coerência padrão
* Filtro de gama IP de mudança
* Alterar configurações de Rede Virtual
* Habilitando várias regiões escreve

> [!NOTE]
> Não é possível simultaneamente adicionar ou remover regiões `locations` () e alterar outras propriedades para uma conta Azure Cosmos. As regiões modificadoras devem ser efetuadas como uma operação separada de qualquer outra alteração à conta.
> [!NOTE]
> Este comando permite-lhe adicionar e remover regiões, mas não permite modificar as prioridades de failover ou desencadear uma falha manual. Consulte [a prioridade de failover](#modify-failover-priority) de modificação e o manual do gatilho [falha](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a> Ativar várias regiões de escrita para uma conta Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Apagar uma conta Azure Cosmos

Este comando elimina uma conta Azure Cosmos existente.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Atualização tags de uma conta Azure Cosmos

Este comando define as [etiquetas de recursos Azure][azure-resource-tags] para uma conta Azure Cosmos. As etiquetas podem ser definidas tanto na criação de conta `New-AzCosmosDBAccount` como na atualização de conta utilizando `Update-AzCosmosDBAccount` .

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> Chaves de conta de lista

Quando cria uma conta Azure Cosmos, o serviço gera duas chaves de acesso primária que podem ser usadas para autenticação quando a conta Azure Cosmos é acedida. São também geradas as chaves de leitura para autenticação de operações só de leitura.
Ao fornecer duas teclas de acesso, a Azure Cosmos DB permite-lhe regenerar e rodar uma chave de cada vez sem interrupção na sua conta Azure Cosmos.
As contas do Cosmos DB têm duas teclas de leitura (primária e secundária) e duas teclas apenas de leitura (primária e secundária).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> Cadeias de ligação de lista

O seguinte comando recupera as cordas de ligação para ligar aplicações à conta Cosmos DB.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> Chaves de conta regeneração

As chaves de acesso a uma conta Azure Cosmos devem ser regeneradas periodicamente para ajudar a manter as ligações seguras. Uma chave de acesso primário e secundário é atribuída à conta. Isto permite que os clientes mantenham o acesso enquanto uma chave de cada vez é regenerada.
Existem quatro tipos de chaves para uma conta Azure Cosmos (Primária, Secundária, Primária e Segunda Leituraonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> Ativar a falha automática

O comando seguinte define uma conta Cosmos DB para falhar automaticamente na sua região secundária caso a região primária fique indisponível.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
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

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> Modificar a prioridade de failover

Para contas configuradas com Falha Automática, pode alterar a ordem em que a Cosmos promoverá réplicas secundárias para primárias caso as primárias fiquem indisponíveis.

Por exemplo abaixo, assuma que a atual prioridade de failover `West US 2 = 0` `East US 2 = 1` é, `South Central US = 2` . . O comando mudará isto `West US 2 = 0` `South Central US = 1` para, `East US 2 = 2` . .

> [!CAUTION]
> Mudar a localização para `failoverPriority=0` irá desencadear uma falha manual para uma conta Azure Cosmos. Quaisquer outras alterações prioritárias não desencadearão um fracasso.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> Falha manual do gatilho

Para contas configuradas com Failover Manual, pode falhar e promover qualquer réplica secundária para primária modificando para `failoverPriority=0` . Esta operação pode ser usada para iniciar um exercício de recuperação de desastres para testar o planeamento de recuperação de desastres.

Por exemplo abaixo, assuma que a conta tem uma prioridade corrente de `West US 2 = 0` e e virar as `East US 2 = 1` regiões.

> [!CAUTION]
> A mudança `locationName` para irá desencadear uma falha manual para uma conta `failoverPriority=0` Azure Cosmos. Qualquer outra mudança de prioridade não irá desencadear um fracasso.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> Listar bloqueios de recursos numa conta DB da Azure Cosmos

As fechaduras de recursos podem ser colocadas nos recursos DB da Azure Cosmos, incluindo bases de dados e coleções. O exemplo abaixo mostra como listar todas as fechaduras de recursos Azure numa conta DB Azure Cosmos.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Base de Dados DB Azure Cosmos

As seguintes secções demonstram como gerir a base de dados DB da Azure Cosmos, incluindo:

* [Criar uma base de dados do Azure Cosmos DB](#create-db)
* [Criar uma base de dados DB Azure Cosmos com produção partilhada](#create-db-ru)
* [Obtenha o resultado de uma base de dados DB Azure Cosmos](#get-db-ru)
* [Migrar a base de dados para autoescala](#migrate-db-ru)
* [Listar todas as bases de dados DB da Azure Cosmos numa conta](#list-db)
* [Obtenha uma única base de dados DB Azure Cosmos](#get-db)
* [Apagar uma base de dados DB do Azure Cosmos](#delete-db)
* [Crie um bloqueio de recursos numa base de dados DB Azure Cosmos para evitar a eliminação](#create-db-lock)
* [Remova um bloqueio de recursos numa base de dados DB do Azure Cosmos](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Criar uma base de dados do Azure Cosmos DB

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Criar uma base de dados DB Azure Cosmos com produção partilhada

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Obtenha o resultado de uma base de dados DB Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>Migrar a base de dados para autoescala

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Obtenha todas as bases de dados DB da Azure Cosmos numa conta

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Obtenha uma única base de dados DB Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Apagar uma base de dados DB do Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>Crie um bloqueio de recursos numa base de dados DB Azure Cosmos para evitar a eliminação

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a>Remova um bloqueio de recursos numa base de dados DB do Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Recipiente DB Azure Cosmos

As seguintes secções demonstram como gerir o contentor Azure Cosmos DB, incluindo:

* [Criar um recipiente DB Azure Cosmos](#create-container)
* [Criar um recipiente DB Azure Cosmos com autoescala](#create-container-autoscale)
* [Crie um recipiente DB Azure Cosmos com uma grande chave de partição](#create-container-big-pk)
* [Obtenha a produção de um contentor Azure Cosmos DB](#get-container-ru)
* [Produção de contentores migrar para autoescala](#migrate-container-ru)
* [Crie um recipiente DB Azure Cosmos com indexação personalizada](#create-container-custom-index)
* [Criar um recipiente DB Azure Cosmos com indexação desligada](#create-container-no-index)
* [Crie um recipiente DB Azure Cosmos com chave única e TTL](#create-container-unique-key-ttl)
* [Criar um contentor DB Azure Cosmos com resolução de conflitos](#create-container-lww)
* [Listar todos os recipientes DB do Azure Cosmos numa base de dados](#list-containers)
* [Obtenha um único contentor DB Azure Cosmos numa base de dados](#get-container)
* [Apagar um recipiente DB Azure Cosmos](#delete-container)
* [Crie um bloqueio de recursos num recipiente DB Azure Cosmos para evitar a eliminação](#create-container-lock)
* [Remova um bloqueio de recursos num recipiente DB Azure Cosmos](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Criar um recipiente DB Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>Criar um recipiente DB Azure Cosmos com autoescala

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Crie um recipiente DB Azure Cosmos com um grande tamanho de chave de partição

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Obtenha a produção de um contentor Azure Cosmos DB

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

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>Produção de contentores migrar para autoescala

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Criar um recipiente DB Azure Cosmos com política de índice personalizado

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

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Criar um recipiente DB Azure Cosmos com indexação desligada

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Crie um recipiente DB Azure Cosmos com uma política chave única e TTL

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

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Criar um contentor DB Azure Cosmos com resolução de conflitos

Para escrever todos os conflitos para o ConflictsFeed e lidar separadamente, passe `-Type "Custom" -Path ""` .

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

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

Criar uma política de resolução de conflitos para utilizar um procedimento armazenado, ligar `New-AzCosmosDBSqlConflictResolutionPolicy` e passar parâmetros `-Type` e `-ConflictResolutionProcedure` .

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Listar todos os recipientes DB do Azure Cosmos numa base de dados

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

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Obtenha um único contentor DB Azure Cosmos numa base de dados

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

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Apagar um recipiente DB Azure Cosmos

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
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>Crie um bloqueio de recursos num recipiente DB Azure Cosmos para evitar a eliminação

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>Remova um bloqueio de recursos num recipiente DB Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>Passos seguintes

* [Todas as amostras powershell](powershell-samples.md)
* [Como gerir a conta da Azure Cosmos](how-to-manage-database-account.md)
* [Criar um recipiente DB Azure Cosmos](how-to-create-container.md)
* [Configurar o tempo para viver em Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell/azure/
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: ../azure-resource-manager/management/overview.md#resource-groups
[azure-resource-tags]: ../azure-resource-manager/management/tag-resources.md
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/