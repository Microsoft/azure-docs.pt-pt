---
title: Gerir os recursos DB da Azure Cosmos utilizando o Azure CLI
description: Utilize o Azure CLI para gerir a sua conta DB Azure Cosmos, base de dados e contentores.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: 86045ee05e4acb15e2268dce4f68387b1527e4b6
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509530"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Gerir os recursos da Azure Cosmos utilizando o Azure CLI

O guia a seguir descreve os comandos comuns para automatizar a gestão das contas, das bases de dados e dos contentores do Azure Cosmos DB com a CLI do Azure. As páginas de referência para todos os comandos Azure Cosmos DB CLI estão disponíveis na [Referência Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Você também pode encontrar mais exemplos em [amostras de CLI Azure para Azure Cosmos DB](cli-samples.md), incluindo como criar e gerir contas, bases de dados e contentores da Cosmos para MongoDB, Gremlin, Cassandra e Table API.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tópico requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="azure-cosmos-accounts"></a>Contas Azure Cosmos

As seguintes secções demonstram como gerir a conta Azure Cosmos, incluindo:

* [Criar uma conta do Azure Cosmos](#create-an-azure-cosmos-db-account)
* [Adicionar ou remover regiões](#add-or-remove-regions)
* [Permitir escritas multi-regiões](#enable-multiple-write-regions)
* [Definir prioridade regional de failover](#set-failover-priority)
* [Ativar a falha automática](#enable-automatic-failover)
* [Falha manual do gatilho](#trigger-manual-failover)
* [Chaves da conta da lista](#list-account-keys)
* [Listar chaves de conta apenas para leitura](#list-read-only-account-keys)
* [Cadeias de ligação de lista](#list-connection-strings)
* [Regenerar a chave da conta](#regenerate-account-key)

### <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Crie uma conta DB Azure Cosmos com SQL API, consistência de sessão nas regiões oeste dos EUA 2 e Leste dos EUA 2:

> [!IMPORTANT]
> O nome da conta Azure Cosmos deve ser minúsculo e inferior a 44 caracteres.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 44 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

### <a name="add-or-remove-regions"></a>Adicionar ou remover regiões

Crie uma conta Azure Cosmos com duas regiões, adicione uma região e remova uma região.

> [!NOTE]
> Não é possível simultaneamente adicionar ou remover regiões `locations` e alterar outras propriedades para uma conta Azure Cosmos. As regiões modificadoras devem ser efetuadas como uma operação separada do que qualquer outra alteração ao recurso de conta.
> [!NOTE]
> Este comando permite-lhe adicionar e remover regiões, mas não permite modificar as prioridades de failover ou desencadear uma falha manual. Consulte [a prioridade de failover do set](#set-failover-priority) e o manual do gatilho [falha](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

### <a name="enable-multiple-write-regions"></a>Ativar várias regiões de escrita

Ativar o multi-mestre para uma conta Cosmos

```azurecli-interactive
# Update an Azure Cosmos account from single to multi-master
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

### <a name="set-failover-priority"></a>Definir prioridade de failover

Desafie a prioridade de failover para uma conta Azure Cosmos configurada para falha automática

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2=0' 'South Central US=1' 'East US 2=2'
```

### <a name="enable-automatic-failover"></a>Ativar a falha automática

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

### <a name="trigger-manual-failover"></a>Falha manual do gatilho

> [!CAUTION]
> Mudar a região com prioridade = 0 irá desencadear uma falha manual para uma conta Azure Cosmos. Qualquer outra mudança de prioridade não irá desencadear um fracasso.

```azurecli-interactive
# Assume region order is initially 'West US 2=0' 'East US 2=1' 'South Central US=2' for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2=0' 'South Central US=1' 'West US 2=2'
```

### <a name="list-all-account-keys"></a><a id="list-account-keys"></a>Listar todas as chaves da conta

Arranja todas as chaves para uma conta cosmos.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

### <a name="list-read-only-account-keys"></a>Listar chaves de conta apenas para leitura

Arranja chaves só para ler para uma conta cosmos.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

### <a name="list-connection-strings"></a>Cadeias de ligação de lista

Obtenha as cordas de ligação para uma conta Cosmos.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

### <a name="regenerate-account-key"></a>Regenerar a chave da conta

Regenerar uma nova chave para uma conta cosmos.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="azure-cosmos-db-database"></a>Base de dados DB Azure Cosmos

As seguintes secções demonstram como gerir a base de dados DB da Azure Cosmos, incluindo:

* [Criar uma base de dados](#create-a-database)
* [Criar uma base de dados com produção partilhada](#create-a-database-with-shared-throughput)
* [Alterar a produção da base de dados](#change-database-throughput)
* [Gerir fechaduras numa base de dados](#manage-lock-on-a-database)

### <a name="create-a-database"></a>Criar uma base de dados

Criar uma base de dados cosmos.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

### <a name="create-a-database-with-shared-throughput"></a>Criar uma base de dados com produção partilhada

Crie uma base de dados Cosmos com produção partilhada.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

### <a name="change-database-throughput"></a>Alterar a produção da base de dados

Aumente a produção de uma base de dados cosmos em 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

### <a name="manage-lock-on-a-database"></a>Gerir o bloqueio numa base de dados

Coloque um bloqueio de exclusão numa base de dados. Para saber mais sobre como permitir este ver, [Prevenir alterações dos SDKs](role-based-access-control.md#preventing-changes-from-cosmos-sdk).

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
databaseName='myDatabase'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
databaseLockName="$databaseName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/sqlDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/sqlDatabases \
        --resource $databaseName \
        --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="azure-cosmos-db-container"></a>Recipiente DB Azure Cosmos

As seguintes secções demonstram como gerir o contentor Azure Cosmos DB, incluindo:

* [Criar um contentor](#create-a-container)
* [Criar um recipiente com TTL ativado](#create-a-container-with-ttl)
* [Criar um recipiente com política de índice personalizado](#create-a-container-with-a-custom-index-policy)
* [Alteração da produção de contentores](#change-container-throughput)
* [Gerir fechaduras num recipiente](#manage-lock-on-a-container)

### <a name="create-a-container"></a>Criar um contentor

Crie um recipiente Cosmos com política de índice padrão, chave de partição e RU/s de 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

### <a name="create-a-container-with-ttl"></a>Criar um recipiente com TTL

Crie um recipiente Cosmos com TTL ativado.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

### <a name="create-a-container-with-a-custom-index-policy"></a>Criar um recipiente com uma política de índice personalizado

Crie um recipiente Cosmos com uma política de índice personalizado, um índice espacial, índice composto, uma chave de partição e RU/s de 400.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

### <a name="change-container-throughput"></a>Alteração da produção de contentores

Aumente a produção de um recipiente Cosmos em 1000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

### <a name="manage-lock-on-a-container"></a>Gerir o bloqueio num recipiente

Coloque um cadeado de apagar num recipiente. Para saber mais sobre como permitir este ver, [Prevenir alterações dos SDKs](role-based-access-control.md#preventing-changes-from-cosmos-sdk).

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
databaseName='myDatabase'
containerName='myContainer'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
containerParent="databaseAccounts/$accountName/sqlDatabases/$databaseName"
containerLockName="$containerName-Lock"

# Create a delete lock on container
az lock create --name $containerLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/containers \
    --lock-type $lockType \
    --parent $containerParent \
    --resource $containerName

# Delete lock on container
lockid=$(az lock show --name $containerLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/containers \
        --resource-name $containerName \
        --parent $containerParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure CLI, consulte:

- [Instalar O Azure CLI](/cli/azure/install-azure-cli)
- [Referência Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Amostras adicionais de Azure CLI para Azure Cosmos DB](cli-samples.md)
