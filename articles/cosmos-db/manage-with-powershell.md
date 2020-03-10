---
title: Criar e gerir o Azure Cosmos DB usando o PowerShell
description: Use o Azure Powershell gerir as suas contas Azure Cosmos DB, bases de dados, contentores e entrada.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/23/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 978f37d08275de704dd01c0251dde42665fca552
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364505"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Gerir os recursos da API Da Azure Cosmos DB SQL utilizando a PowerShell

O guia a seguir descreve como utilizar o PowerShell para gerar scripts e automatizar a gestão de recursos do Azure Cosmos DB, incluindo a conta, a base de dados, o contentor e o débito. A gestão do Azure Cosmos DB é processada através do cmdlet AzResource diretamente no fornecedor de recursos do Azure Cosmos DB. Para ver todas as propriedades que podem ser geridas usando powerShell para o fornecedor de recursos Azure Cosmos DB, consulte o esquema de fornecedor de [recursos da Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Para gestão transversal do Azure Cosmos DB, pode utilizar [o Azure CLI,](manage-with-cli.md)o [REST API][rp-rest-api]ou o [portal Azure.](create-sql-api-dotnet.md#create-account)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Getting Started

Siga as instruções em Como instalar e configurar o [Azure PowerShell][powershell-install-configure] para instalar e iniciar sessão na sua conta Azure no Powershell.

* Se quiser executar os seguintes comandos sem necessitar de confirmação do utilizador, acomode a bandeira `-Force` ao comando.
* Todos os comandos seguintes são síncronos.

## <a name="azure-cosmos-accounts"></a>Contas Azure Cosmos

As seguintes secções demonstram como gerir a conta Azure Cosmos, incluindo:

* [Criar uma conta Azure Cosmos](#create-account)
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

### <a id="create-account"></a>Criar uma conta Azure Cosmos

Este comando cria uma conta de base de dados Azure Cosmos com [múltiplas regiões,][distribute-data-globally]política de [consistência](consistency-levels.md)limitada.

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` O nome da conta Azure Cosmos. Deve ser minúscula, aceita o carácter alfanumérico e o '-' e entre 3 e 31 caracteres.
* `$location` A localização do recurso da conta Azure Cosmos.
* `$locations` as regiões réplicas para a conta de base de dados. Deve haver uma região de escrita por conta de base de dados com um valor prioritário de 0.
* `$consistencyPolicy` O nível de consistência padrão da conta Azure Cosmos. Para mais informações, consulte [Níveis de Consistência em Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` Os valores imobiliários passaram para o Fornecedor de Gestão de Recursos Da Cosmos DB Azure para fornecer a conta.

As contas da Azure Cosmos podem ser configuradas com ip firewall, bem como pontos finais de serviço de Rede Virtual. Para obter informações sobre como configurar o FIREWALL IP para Azure Cosmos DB, consulte [Configure IP Firewall](how-to-configure-firewall.md).  Para obter mais informações sobre como ativar os pontos finais de serviço para o Azure Cosmos DB, consulte o [acesso configure a partir de redes virtuais](how-to-configure-vnet-service-endpoint.md).

### <a id="list-accounts"></a>Listar todas as contas da Azure Cosmos numa subscrição

Este comando permite-lhe listar todas as contas da Azure Cosmos numa subscrição.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a>Obtenha as propriedades de uma conta Azure Cosmos

Este comando permite-lhe obter as propriedades de uma conta Azure Cosmos existente.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a>Atualizar uma conta Azure Cosmos

Este comando permite-lhe atualizar as propriedades da sua conta de base de dados Azure Cosmos. As propriedades que podem ser atualizadas incluem:

* Adicionar ou remover regiões
* Alteração da política de consistência por defeito
* Alteração do filtro de gama IP
* Alterando as configurações da Rede Virtual
* Habilitar o Multi-mestre

> [!NOTE]
> Não é possível adicionar ou remover simultaneamente regiões `locations` e alterar outras propriedades para uma conta Azure Cosmos. As regiões modificantes devem ser executadas como uma operação separada do que qualquer outra alteração ao recurso da conta.
> [!NOTE]
> Este comando permite-lhe adicionar e remover regiões, mas não permite modificar as prioridades de failover ou desencadear uma falha manual. Consulte [modificar a prioridade de failover](#modify-failover-priority) e [acionar o manual de falhas](#trigger-manual-failover).

```azurepowershell-interactive
# Create an account with 2 regions
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$accountName = "mycosmosaccount" # must be lower case and < 31 characters

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false },
    @{ "locationName"="South Central US"; "failoverPriority"=2, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```
### <a id="multi-master"></a>Ativar várias regiões de escrita para uma conta Azure Cosmos

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a>Apagar uma conta Azure Cosmos

Este comando permite-lhe apagar uma conta Azure Cosmos existente.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a>Etiquetas atualizadas de uma conta Azure Cosmos

O exemplo seguinte descreve como definir etiquetas de [recursos Azure][azure-resource-tags] para uma conta Azure Cosmos.

> [!NOTE]
> Este comando pode ser combinado com os comandos de criação ou atualização, afuntando a bandeira `-Tags` com o parâmetro correspondente.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a>Lista de Chaves da Conta

Quando cria uma conta do Azure Cosmos DB, o serviço gera duas chaves de acesso mestre que podem ser utilizadas para autenticação quando aceder a conta do Azure Cosmos DB. Ao fornecer duas chaves de acesso, o Azure Cosmos DB permite-lhe voltar a gerar as chaves sem interrupções na sua conta do Azure Cosmos DB. Chaves só de leitura para a autenticação de operações só de leitura também estão disponíveis. Existem duas chaves de leitura / escrita (primárias e secundárias) e duas chaves só de leitura (primário e secundário).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Write-Host "PrimaryKey =" $keys.primaryMasterKey
Write-Host "SecondaryKey =" $keys.secondaryMasterKey
```

### <a id="list-connection-strings"></a>Cadeias de ligação de lista

Para contas de MongoDB, a cadeia de ligação para ligar a sua aplicação MongoDB à conta de base de dados pode ser obtida com o seguinte comando.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a>Chaves de conta regeneradas

As chaves de acesso a uma conta Azure Cosmos devem ser periodicamente regeneradas para ajudar a manter as ligações mais seguras. As chaves de acesso primária e secundária são atribuídas à conta. Isto permite que os clientes mantenham o acesso enquanto o outro é regenerado. Existem quatro tipos de chaves para uma conta Azure Cosmos (Primary, Secondary, PrimaryReadonly e SecondaryReadonly)

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="enable-automatic-failover"></a>Ativar falha automática

Permite que uma conta Cosmos falhe na sua região secundária caso a região primária fique indisponível.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="modify-failover-priority"></a>Modificar prioridade de failover

Para contas configuradas com Failover Automático, pode alterar a ordem em que a Cosmos promoverá réplicas secundárias para as primárias caso as primárias fiquem indisponíveis.

Por exemplo abaixo, assuma a atual prioridade de failover, `West US 2 = 0`, `East US 2 = 1`, `South Central US = 2`.

> [!CAUTION]
> Mudar `locationName` para `failoverPriority=0` irá desencadear uma falha manual para uma conta Azure Cosmos. Quaisquer outras alterações prioritárias não desencadearão uma falha.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="South Central US"; "failoverPriority"=1 },
    @{ "locationName"="East US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

### <a id="trigger-manual-failover"></a>Falha manual do gatilho

Para contas configuradas com Failover Manual, pode falhar e promover qualquer réplica secundária para primária, modificando para `failoverPriority=0`. Esta operação pode ser usada para iniciar um exercício de recuperação de desastres para testar o planeamento de recuperação de desastres.

Para o exemplo abaixo, assuma que a conta tem uma prioridade atual de falha de `West US 2 = 0` e `East US 2 = 1` e virar as regiões.

> [!CAUTION]
> Mudar `locationName` para `failoverPriority=0` irá desencadear uma falha manual para uma conta Azure Cosmos. Qualquer outra mudança prioritária não irá desencadear uma falha.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="South Central US"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="West US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Base de Dados Azure Cosmos

As seguintes secções demonstram como gerir a base de dados Azure Cosmos, incluindo:

* [Criar uma base de dados Azure Cosmos](#create-db)
* [Criar uma base de dados Azure Cosmos com entrada partilhada](#create-db-ru)
* [Obtenha a entrada de uma base de dados Azure Cosmos](#get-db-ru)
* [Listar todas as bases de dados da Azure Cosmos numa conta](#list-db)
* [Obtenha uma única base de dados azure cosmos](#get-db)
* [Eliminar uma base de dados Azure Cosmos](#delete-db)

### <a id="create-db"></a>Criar uma base de dados Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Criar uma base de dados Azure Cosmos com entrada partilhada

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>Obtenha a entrada de uma base de dados Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>Obtenha todas as bases de dados da Azure Cosmos numa conta

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Obtenha uma única base de dados azure cosmos

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Eliminar uma base de dados Azure Cosmos

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Recipiente Azure Cosmos

As seguintes secções demonstram como gerir o contentor Azure Cosmos, incluindo:

* [Crie um recipiente Azure Cosmos](#create-container)
* [Crie um recipiente Azure Cosmos com uma grande chave de partição](#create-container-big-pk)
* [Obtenha a entrada de um recipiente Azure Cosmos](#get-container-ru)
* [Crie um recipiente Azure Cosmos com entrada partilhada](#create-container-ru)
* [Crie um recipiente Azure Cosmos com indexação personalizada](#create-container-custom-index)
* [Criar um recipiente Azure Cosmos com indexação desligada](#create-container-no-index)
* [Crie um recipiente Azure Cosmos com chave única e TTL](#create-container-unique-key-ttl)
* [Criar um contentor Azure Cosmos com resolução de conflitos](#create-container-lww)
* [Liste todos os contentores Azure Cosmos numa base de dados](#list-containers)
* [Coma um único contentor Azure Cosmos numa base de dados](#get-container)
* [Apagar um recipiente Azure Cosmos](#delete-container)

### <a id="create-container"></a>Crie um recipiente Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-big-pk"></a>Crie um recipiente Azure Cosmos com uma grande chave de partição

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>Obtenha a entrada de um recipiente Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>Crie um recipiente Azure Cosmos com entrada partilhada

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Crie um recipiente Azure Cosmos com política de índice personalizado

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Criar um recipiente Azure Cosmos com indexação desligada

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Crie um recipiente Azure Cosmos com uma política chave única e TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 86400;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Criar um contentor Azure Cosmos com resolução de conflitos

Criar uma política de resolução de conflitos para utilizar um procedimento armazenado, definir `"mode"="custom"` e definir o caminho de resolução como o nome do procedimento armazenado, `"conflictResolutionPath"="myResolverStoredProcedure"`. Para escrever todos os conflitos no ConflictsFeed e manusear separadamente, estabeleça `"mode"="custom"` e `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-containers"></a>Liste todos os contentores Azure Cosmos numa base de dados

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Coma um único contentor Azure Cosmos numa base de dados

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Apagar um recipiente Azure Cosmos

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Passos Seguintes

* [Todas as amostras powershell](powershell-samples.md)
* [Como gerir a conta Azure Cosmos](how-to-manage-database-account.md)
* [Crie um recipiente Azure Cosmos](how-to-create-container.md)
* [Configure o tempo para viver em Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
