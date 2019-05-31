---
title: Criar e gerir o Azure Cosmos DB com o PowerShell
description: Utilize o Azure Powershell gerir as suas contas do Azure Cosmos DB, bases de dados, contentores e débito.
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/23/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: f720b678f2c7a6e564ef3e8fa9ae071b004ed1a6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243395"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Gerir recursos do Azure Cosmos DB SQL API com o PowerShell

O guia seguinte descreve como utilizar o PowerShell para script e automatizar a gestão do Azure Cosmos DB, incluindo a conta, a base de dados, o contentor e o débito. Gestão do Azure Cosmos DB é não através de cmdlets específicos do Azure Cosmos DB, mas com o fornecedor de recursos diretamente através do cmdlet AzResource. Para ver todas as propriedades que podem ser geridas com o PowerShell para o fornecedor de recursos do Azure Cosmos DB, consulte [esquema de fornecedor de recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Para a gestão de várias plataformas do Azure Cosmos DB, pode utilizar [CLI do Azure](manage-with-cli.md), o [REST API][rp-rest-api], ou o [portal do Azure](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução

Siga as instruções em [como instalar e configurar o Azure PowerShell] [ powershell-install-configure] para instalar e iniciar sessão sua conta do Azure no Powershell.

* Se desejar executar os seguintes comandos sem a necessidade de confirmação do usuário, acrescentar o `-Force` sinalizador para o comando.
* Todos os comandos seguintes são síncronos.

## <a name="azure-cosmos-accounts"></a>Contas do Cosmos do Azure

As secções seguintes demonstram como gerir a conta do Cosmos do Azure, incluindo:

* [Criar uma conta do Cosmos do Azure](#create-account)
* [Atualizar uma conta do Cosmos do Azure](#update-account)
* [Obter uma conta do Cosmos do Azure](#get-account)
* [Eliminar uma conta do Cosmos do Azure](#delete-account)
* [Etiquetas de atualização para uma conta do Cosmos do Azure](#update-tags)
* [Listar chaves para uma conta do Cosmos do Azure](#list-keys)
* [Voltar a gerar chaves de uma conta do Cosmos do Azure](#regenerate-keys)
* [Lista de cadeias de ligação para uma conta do Cosmos do Azure](#list-connection-strings)
* [Modificar a prioridade de ativação pós-falha para uma conta do Cosmos do Azure](#modify-failover-priority)

### <a id="create-account"></a> Criar uma conta do Cosmos do Azure

Este comando permite-lhe criar uma conta de base de dados do Azure Cosmos DB. Configurar a sua nova conta de base de dados como uma única região ou [multirregião] [ distribute-data-globally] com uma determinada [política de consistência](consistency-levels.md).

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
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
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` O nome da conta do Cosmos do Azure. Tem de ser em minúsculas, aceita de alfanuméricos e o "-" caráter e entre 3 e 50 carateres.
* `$location` A localização para a conta do Cosmos do Azure.
* `$locations` As regiões de réplica para a conta de base de dados. Tem de existir uma região de escrita por conta de base de dados com um valor de prioridade de ativação pós-falha de 0.
* `$consistencyPolicy` O nível de consistência predefinida da conta do Cosmos do Azure. Para obter mais informações, consulte [níveis de consistência no Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` Os valores de propriedade passado para o fornecedor do Cosmos DB do Azure Resource Manager para aprovisionar a conta.

Pontos finais de serviço de contas podem ser configuradas com a Firewall do IP, bem como a rede Virtual do Azure Cosmos. Para obter informações sobre como configurar a Firewall do IP para o Azure Cosmos DB, consulte [configurar a Firewall do IP](how-to-configure-firewall.md).  Para obter mais informações sobre como ativar pontos finais de serviço para o Azure Cosmos DB, consulte [configurar o acesso a partir de redes virtuais](how-to-configure-vnet-service-endpoint.md) .

### <a id="get-account"></a> Obter as propriedades de uma conta do Cosmos do Azure

Este comando permite-lhe obter as propriedades de uma conta do Cosmos do Azure existente.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Atualizar uma conta do Cosmos do Azure

Este comando permite-lhe atualizar as propriedades da conta de base de dados do Azure Cosmos DB. Propriedades que podem ser atualizadas incluem o seguinte:

* Adicionar ou remover regiões
* Alterar a política de consistência predefinida
* Alterar a política de ativação pós-falha
* Alterar o filtro de intervalo IP
* Alterar configurações de rede Virtual
* Ativar múltiplos principais

> [!NOTE]
> Este comando permite-lhe adicionar e remover regiões, mas não permite-lhe modificar prioridades de ativação pós-falha. Para modificar as prioridades de ativação pós-falha, consulte [modificar a prioridade de ativação pós-falha para uma conta do Azure Cosmos](#modify-failover-priority).

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Eliminar uma conta do Cosmos do Azure

Este comando permite-lhe eliminar uma conta do Cosmos do Azure existente.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Etiquetas de atualização de uma conta do Cosmos do Azure

O exemplo a seguir descreve como definir [etiquetas de recursos do Azure] [ azure-resource-tags] para uma conta do Cosmos do Azure.

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

### <a id="regenerate-keys"></a> Voltar a gerar chaves de conta

Chaves de acesso a uma conta do Cosmos do Azure devem ser regeneradas periodicamente para ajudar a manter as ligações mais seguro. Chaves de acesso primária e secundária são atribuídos à conta. Isso permite que os clientes manter o acesso, enquanto o outro é regenerado. Há quatro tipos de chaves para uma conta do Cosmos do Azure (principal, secundária, PrimaryReadonly e SecondaryReadonly)

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

### <a id="modify-failover-priority"></a> Modificar a prioridade de ativação pós-falha

Para contas de base de dados de várias regiões, pode alterar a prioridade de ativação pós-falha de várias regiões em que a conta de base de dados do Azure Cosmos DB existe no. Para obter mais informações sobre a ativação pós-falha na sua conta de base de dados do Azure Cosmos DB, consulte [distribuir dados globalmente com o Azure Cosmos DB][distribute-data-globally].

Para o exemplo abaixo, suponha que a conta tem uma prioridade de ativação pós-falha atual de westus = 0 e eastus = 1. O exemplo a seguir, irá inverter as regiões.

> [!CAUTION]
> Esta operação irá acionar uma ativação pós-falha manual para a sua conta para a nova região com uma failoverPriority igual a zero.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Base de dados do Cosmos do Azure

As secções seguintes demonstram como gerir a base de dados do Cosmos do Azure, incluindo:

* [Criar uma base de dados do Cosmos do Azure](#create-db)
* [Criar uma base de dados do Cosmos do Azure com a taxa de transferência partilhada](#create-db-ru)
* [Liste todas as bases de dados do Cosmos do Azure numa conta](#get-all-db)
* [Obter um único banco de dados do Cosmos do Azure](#get-db)
* [Eliminar uma base de dados do Cosmos do Azure](#delete-db)

### <a id="create-db"></a>Criar uma base de dados do Cosmos do Azure

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

### <a id="create-db-ru"></a>Criar uma base de dados do Cosmos do Azure com a taxa de transferência partilhada

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

### <a id="get-all-db"></a>Obter todas as bases de dados do Cosmos do Azure numa conta

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Obter um único banco de dados do Cosmos do Azure

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

### <a id="delete-db"></a>Eliminar uma base de dados do Cosmos do Azure

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos Container

As secções seguintes demonstram como gerir o contentor do Cosmos do Azure, incluindo:

* [Criar um contentor do Cosmos do Azure](#create-container)
* [Criar um contentor do Cosmos do Azure com a taxa de transferência partilhada](#create-container-ru)
* [Criar um contentor do Cosmos do Azure com indexação personalizada](#create-container-custom-index)
* [Criar um contentor do Cosmos do Azure com indexação desativado](#create-container-no-index)
* [Criar um contentor do Cosmos do Azure com a chave exclusiva e o valor de TTL](#create-container-unique-key-ttl)
* [Criar um contentor do Cosmos do Azure com a resolução de conflitos](#create-container-lww)
* [Listar todos os contentores do Azure Cosmos numa base de dados](#list-all-container)
* [Obter um único contentor do Cosmos do Azure numa base de dados](#get-container)
* [Eliminar um contentor do Cosmos do Azure](#delete-container)

### <a id="create-container"></a>Criar um contentor do Cosmos do Azure

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

### <a id="create-container-ru"></a>Criar um contentor do Cosmos do Azure com a taxa de transferência partilhada

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

### <a id="create-container-custom-index"></a>Criar um contentor do Cosmos do Azure com a política de índice personalizado

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
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
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

### <a id="create-container-no-index"></a>Criar um contentor do Cosmos do Azure com indexação desativado

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

### <a id="create-container-unique-key-ttl"></a>Criar um contentor do Cosmos do Azure com a política de chaves exclusivas e TTL

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
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
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

### <a id="create-container-lww"></a>Criar um contentor do Cosmos do Azure com a resolução de conflitos

Para criar uma política de resolução de conflito para utilizar um procedimento armazenado, defina `"mode"="custom"` e defina o caminho de solução como o nome do procedimento armazenado, `"conflictResolutionPath"="myResolverStoredProcedure"`. Para todos os conflitos de escrita para o ConflictsFeed e processar em separado, defina `"mode"="custom"` e `"conflictResolutionPath"=""`

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

### <a id="list-all-container"></a>Listar todos os contentores do Azure Cosmos numa base de dados

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

### <a id="get-container"></a>Obter um único contentor do Cosmos do Azure numa base de dados

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

### <a id="delete-container"></a>Eliminar um contentor do Cosmos do Azure

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

* [Todos os exemplos do PowerShell](powershell-samples.md)
* [Como gerir a conta do Cosmos do Azure](how-to-manage-database-account.md)
* [Criar um contentor do Cosmos do Azure](how-to-create-container.md)
* [Configurar o tempo de vida no Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/