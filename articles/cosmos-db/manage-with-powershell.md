---
title: Criar e gerenciar Azure Cosmos DB usando o PowerShell
description: Use o Azure PowerShell para gerenciar suas contas, bancos de dados, contêineres e taxa de transferência do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/05/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 45f5e21e05cf627d418cb66418cf305833a73891
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965097"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Gerenciar Azure Cosmos DB recursos da API do SQL usando o PowerShell

O guia a seguir descreve como usar o PowerShell para gerar scripts e automatizar o gerenciamento de recursos de Azure Cosmos DB, incluindo conta, banco de dados, contêiner e taxa de transferência. O gerenciamento de Azure Cosmos DB é manipulado por meio do cmdlet AzResource diretamente para o provedor de recursos Azure Cosmos DB. Para exibir todas as propriedades que podem ser gerenciadas usando o PowerShell para o provedor de recursos Azure Cosmos DB, consulte [Azure Cosmos DB esquema do provedor de recursos](/azure/templates/microsoft.documentdb/allversions)

Para o gerenciamento de plataforma cruzada do Azure Cosmos DB, você pode usar [CLI do Azure](manage-with-cli.md), a [API REST][rp-rest-api]ou a [portal do Azure](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução

Siga as instruções em [como instalar e configurar o Azure PowerShell][powershell-install-configure] para instalar e entrar em sua conta do Azure no PowerShell.

* Se desejar executar os seguintes comandos sem a necessidade de confirmação do usuário, acrescentar o `-Force` sinalizador para o comando.
* Todos os comandos seguintes são síncronos.

## <a name="azure-cosmos-accounts"></a>Contas do Azure Cosmos

As seções a seguir demonstram como gerenciar a conta do Azure Cosmos, incluindo:

* [Criar uma conta do Azure Cosmos](#create-account)
* [Atualizar uma conta do Azure Cosmos](#update-account)
* [Listar todas as contas do Azure Cosmos em uma assinatura](#list-accounts)
* [Obter uma conta do Azure Cosmos](#get-account)
* [Excluir uma conta do Azure Cosmos](#delete-account)
* [Atualizar marcas para uma conta do Azure Cosmos](#update-tags)
* [Listar chaves para uma conta do Azure Cosmos](#list-keys)
* [Regenerar chaves para uma conta do Azure Cosmos](#regenerate-keys)
* [Listar cadeias de conexão para uma conta do Azure Cosmos](#list-connection-strings)
* [Modificar a prioridade de failover para uma conta do Azure Cosmos](#modify-failover-priority)

### <a id="create-account"></a>Criar uma conta do Azure Cosmos

Este comando cria uma conta de banco de dados Azure Cosmos DB com [várias regiões][distribute-data-globally], [política de consistência](consistency-levels.md)de desatualização limitada.

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

* `$accountName`O nome da conta do Azure Cosmos. Deve estar em letras minúsculas, aceita caracteres alfanuméricos e '-' e entre 3 e 31 caracteres.
* `$location`O local para o recurso de conta do Azure Cosmos.
* `$locations`As regiões de réplica para a conta do banco de dados. Deve haver uma região de gravação por conta de banco de dados com um valor de prioridade de failover 0.
* `$consistencyPolicy`O nível de consistência padrão da conta do Azure Cosmos. Para obter mais informações, consulte [níveis de consistência no Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties`Os valores de propriedade passados para o provedor de Azure Resource Manager Cosmos DB para provisionar a conta.

As contas do Azure Cosmos podem ser configuradas com o firewall IP, bem como pontos de extremidade de serviço de rede virtual. Para obter informações sobre como configurar o firewall IP para Azure Cosmos DB, consulte [Configurar o firewall de IP](how-to-configure-firewall.md).  Para obter mais informações sobre como habilitar pontos de extremidade de serviço para Azure Cosmos DB, consulte [Configurar o acesso de redes virtuais](how-to-configure-vnet-service-endpoint.md).

### <a id="list-accounts"></a>Listar todas as contas do Azure Cosmos em uma assinatura

Esse comando permite listar todas as contas do Azure Cosmos em uma assinatura.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a>Obter as propriedades de uma conta do Azure Cosmos

Esse comando permite que você obtenha as propriedades de uma conta existente do Azure Cosmos.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a>Atualizar uma conta do Azure Cosmos

Este comando permite-lhe atualizar as propriedades da conta de base de dados do Azure Cosmos DB. As propriedades que podem ser atualizadas incluem o seguinte:

* Adicionando ou removendo regiões
* Alterando a política de consistência padrão
* Alterando o filtro de intervalo de IP
* Alterando as configurações de rede virtual
* Habilitando vários mestres

> [!NOTE]
> Esse comando permite que você adicione e remova regiões, mas não permite que você modifique as prioridades de failover ou altere a `failoverPriority=0`região com. Para modificar a prioridade de failover, consulte [Modificar a prioridade de failover para uma conta do Azure Cosmos](#modify-failover-priority).

```azurepowershell-interactive
# Get an Azure Cosmos Account (assume it has two regions currently West US 2 and East US 2) and add a third region

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="South Central US"; "failoverPriority"=2 }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a>Excluir uma conta do Azure Cosmos

Esse comando permite que você exclua uma conta existente do Azure Cosmos.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a>Atualizar marcas de uma conta do Azure Cosmos

O exemplo a seguir descreve como definir [marcas de recurso do Azure][azure-resource-tags] para uma conta do Azure Cosmos.

> [!NOTE]
> Este comando pode ser combinado com os comandos de criar ou atualizar, acrescentando a `-Tags` sinalizador com o parâmetro correspondente.

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

### <a id="list-keys"></a> Listar chaves de conta

Quando cria uma conta do Azure Cosmos DB, o serviço gera duas chaves de acesso mestre que podem ser utilizadas para autenticação quando aceder a conta do Azure Cosmos DB. Ao fornecer duas chaves de acesso, o Azure Cosmos DB permite-lhe voltar a gerar as chaves sem interrupções na sua conta do Azure Cosmos DB. Chaves só de leitura para a autenticação de operações só de leitura também estão disponíveis. Existem duas chaves de leitura / escrita (primárias e secundárias) e duas chaves só de leitura (primário e secundário).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> Lista cadeias de ligação

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

### <a id="regenerate-keys"></a>Regenerar chaves de conta

As chaves de acesso para uma conta do Azure Cosmos devem ser regeneradas periodicamente para ajudar a manter as conexões mais seguras. As chaves de acesso primária e secundária são atribuídas à conta. Isso permite que os clientes mantenham o acesso enquanto o outro é regenerado. Há quatro tipos de chaves para uma conta do Azure Cosmos (primário, secundário, PrimaryReadonly e SecondaryReadonly)

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

### <a id="modify-failover-priority"></a>Modificar prioridade de failover

Para contas de banco de dados de várias regiões, você pode alterar a ordem em que uma conta do cosmos promoverá réplicas secundárias de leitura se ocorrer um failover regional na réplica de gravação primária. A `failoverPriority=0` modificação também pode ser usada para iniciar uma análise de recuperação de desastre para testar o planejamento da recuperação de desastre.

Para o exemplo a seguir, suponha que a conta tenha uma prioridade de `West US 2 = 0` failover `East US 2 = 1` atual de e e inverta as regiões.

> [!CAUTION]
> A `locationName` alteração `failoverPriority=0` de para irá disparar um failover manual para uma conta do Azure Cosmos. Qualquer outra alteração de prioridade não disparará um failover.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="East US 2"; "failoverPriority"=0 },
    @{ "locationName"="West US 2"; "failoverPriority"=1 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Banco de dados Cosmos do Azure

As seções a seguir demonstram como gerenciar o banco de dados Cosmos do Azure, incluindo:

* [Criar um banco de dados Cosmos do Azure](#create-db)
* [Criar um banco de dados Cosmos do Azure com taxa de transferência compartilhada](#create-db-ru)
* [Obter a taxa de transferência de um banco de dados Cosmos do Azure](#get-db-ru)
* [Listar todos os bancos de dados do Azure Cosmos em uma conta](#list-db)
* [Obter um único banco de dados Cosmos do Azure](#get-db)
* [Excluir um banco de dados Cosmos do Azure](#delete-db)

### <a id="create-db"></a>Criar um banco de dados Cosmos do Azure

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

### <a id="create-db-ru"></a>Criar um banco de dados Cosmos do Azure com taxa de transferência compartilhada

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

### <a id="get-db-ru"></a>Obter a taxa de transferência de um banco de dados Cosmos do Azure

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

### <a id="list-db"></a>Obter todos os bancos de dados do Azure Cosmos em uma conta

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Obter um único banco de dados Cosmos do Azure

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

### <a id="delete-db"></a>Excluir um banco de dados Cosmos do Azure

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Contêiner Cosmos do Azure

As seções a seguir demonstram como gerenciar o contêiner Cosmos do Azure, incluindo:

* [Criar um contêiner Cosmos do Azure](#create-container)
* [Criar um contêiner Cosmos do Azure com uma chave de partição grande](#create-container-big-pk)
* [Obter a taxa de transferência de um contêiner Cosmos do Azure](#get-container-ru)
* [Criar um contêiner Cosmos do Azure com taxa de transferência compartilhada](#create-container-ru)
* [Criar um contêiner Cosmos do Azure com indexação personalizada](#create-container-custom-index)
* [Criar um contêiner Cosmos do Azure com a indexação desativada](#create-container-no-index)
* [Criar um contêiner Cosmos do Azure com chave exclusiva e TTL](#create-container-unique-key-ttl)
* [Criar um contêiner Cosmos do Azure com resolução de conflitos](#create-container-lww)
* [Listar todos os contêineres de Cosmos do Azure em um banco de dados](#list-containers)
* [Obter um único contêiner Cosmos do Azure em um banco de dados](#get-container)
* [Excluir um contêiner Cosmos do Azure](#delete-container)

### <a id="create-container"></a>Criar um contêiner Cosmos do Azure

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

### <a id="create-container-big-pk"></a>Criar um contêiner Cosmos do Azure com um tamanho de chave de partição grande

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

### <a id="get-container-ru"></a>Obter a taxa de transferência de um contêiner Cosmos do Azure

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

### <a id="create-container-ru"></a>Criar um contêiner Cosmos do Azure com taxa de transferência compartilhada

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

### <a id="create-container-custom-index"></a>Criar um contêiner Cosmos do Azure com política de índice personalizada

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

### <a id="create-container-no-index"></a>Criar um contêiner Cosmos do Azure com a indexação desativada

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

### <a id="create-container-unique-key-ttl"></a>Criar um contêiner Cosmos do Azure com política de chave exclusiva e TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
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
        "defaultTtl"= 100;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Criar um contêiner Cosmos do Azure com resolução de conflitos

Para criar uma política de resolução de conflitos para usar um procedimento armazenado `"mode"="custom"` , defina e defina o caminho de resolução como o nome do procedimento `"conflictResolutionPath"="myResolverStoredProcedure"`armazenado,. Para gravar todos os conflitos no ConflictsFeed e manipular separadamente, defina `"mode"="custom"` e`"conflictResolutionPath"=""`

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

### <a id="list-containers"></a>Listar todos os contêineres de Cosmos do Azure em um banco de dados

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

### <a id="get-container"></a>Obter um único contêiner Cosmos do Azure em um banco de dados

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

### <a id="delete-container"></a>Excluir um contêiner Cosmos do Azure

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

## <a name="next-steps"></a>Passos seguintes

* [Todos os exemplos do PowerShell](powershell-samples.md)
* [Como gerenciar a conta do Azure Cosmos](how-to-manage-database-account.md)
* [Criar um contêiner Cosmos do Azure](how-to-create-container.md)
* [Configurar o tempo de vida no Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
