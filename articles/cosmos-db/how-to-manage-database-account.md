---
title: Saiba como gerir contas de base de dados no Azure Cosmos DB
description: Saiba como gerir contas de base de dados no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: db7746bc91935c0385e97d494a45d34819665ced
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093393"
---
# <a name="manage-an-azure-cosmos-account"></a>Gerir uma conta do Azure Cosmos

Este artigo descreve como gerenciar várias tarefas em uma conta do Azure Cosmos usando os modelos portal do Azure, Azure PowerShell, CLI do Azure e Azure Resource Manager.

## <a name="create-an-account"></a>Criar uma conta

### <a id="create-database-account-via-portal"></a>Portal do Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>CLI do Azure

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case and < 31 characters

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=WestUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
```azurepowershell-interactive
# Create an Azure Cosmos account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case and < 31 characters

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

### <a id="create-database-account-via-arm-template"></a>Modelo de Azure Resource Manager

Este modelo de Azure Resource Manager criará uma conta do Azure Cosmos para qualquer API com suporte configurada com duas regiões e opções para selecionar nível de consistência, failover automático e vários mestres. Para implantar esse modelo, clique em implantar no Azure na página Leiame, [criar conta do Azure Cosmos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)

## <a name="addremove-regions-from-your-database-account"></a>Adicionar/remover regiões da conta de base de dados

### <a id="add-remove-regions-via-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Acesse sua conta do Azure Cosmos e abra o menu **replicar dados globalmente** .

1. Para adicionar regiões, selecione os hexágonos no mapa com o **+** rótulo que corresponde às regiões desejadas. Como alternativa, para adicionar uma região, selecione a opção **+ Adicionar região** e escolha uma região no menu suspenso.

1. Para remover regiões, desmarque uma ou mais regiões do mapa selecionando os hexágonos azuis com marcas de seleção. Ou selecione o ícone "lixeira"🗑() ao lado da região no lado direito.

1. Para salvar as alterações, selecione **OK**.

   ![Menu Adicionar ou remover regiões](./media/how-to-manage-database-account/add-region.png)

Em um modo de gravação de região única, não é possível remover a região de gravação. Você deve fazer failover para uma região diferente antes de excluir a região de gravação atual.

Em um modo de gravação de várias regiões, você pode adicionar ou remover qualquer região, se tiver pelo menos uma região.

### <a id="add-remove-regions-via-cli"></a>CLI do Azure

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case and <31 characters

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case and <31 characters

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( 
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East Us"; "failoverPriority"=1 } 
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="configure-multiple-write-regions"></a>Configurar várias regiões de gravação

### <a id="configure-multiple-write-regions-portal"></a>Portal do Azure

Abra a guia **replicar dados globalmente** e selecione **habilitar** para habilitar gravações em várias regiões. Depois de habilitar as gravações de várias regiões, todas as regiões de leitura que você tem atualmente na conta ficarão regiões de leitura e gravação. 

> [!NOTE]
> Depois de habilitar as gravações de várias regiões, você não pode desabilitá-la. 

![A conta do Azure Cosmos configura a captura de tela de vários mestres](./media/how-to-manage-database-account/single-to-multi-master.png)

Entre em contato com o askcosmosdb@microsoft.com alias para obter mais perguntas sobre esse recurso. 

### <a id="configure-multiple-write-regions-cli"></a>CLI do Azure

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>Modelo do Resource Manager

Uma conta pode ser migrada de um mestre único para vários mestres implantando o modelo do Resource Manager usado para criar a conta e a `enableMultipleWriteLocations: true`configuração. O modelo de Azure Resource Manager a seguir é um modelo mínimo que implantará uma conta do Azure Cosmos para a API do SQL com duas regiões e vários locais de gravação habilitados.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Habilitar o failover automático para sua conta do Azure Cosmos

A opção de failover automático permite que Azure Cosmos DB failover para a região com a maior prioridade de failover sem ação do usuário se uma região ficar indisponível. Quando o failover automático estiver habilitado, a prioridade da região poderá ser modificada. A conta deve ter duas ou mais regiões para habilitar o failover automático.

### <a id="enable-automatic-failover-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos, abra o painel **replicar dados globalmente** .

2. Na parte superior do painel, selecione **failover automático**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **failover automático** , verifique se **habilitar failover automático** está definido como **ativado**. 

4. Selecione **Guardar**.

   ![Menu do portal de ativação pós-falha automática](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>CLI do Azure

```azurecli-interactive
# Enable automatic failover on an existing account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-automatic-failover true
```

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Definir prioridades de failover para sua conta do Azure Cosmos

Depois que uma conta do cosmos é configurada para failover automático, a prioridade de failover para regiões pode ser alterada.

> [!IMPORTANT]
> Não é possível modificar a região de gravação (prioridade de failover zero) quando a conta está configurada para failover automático. Para alterar a região de gravação, você deve desabilitar o failover automático e fazer um failover manual.

### <a id="set-failover-priorities-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos, abra o painel **replicar dados globalmente** .

2. Na parte superior do painel, selecione **failover automático**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **failover automático** , verifique se **habilitar failover automático** está definido como **ativado**.

4. Para modificar a prioridade do failover, arraste as regiões de leitura por meio dos três pontos no lado esquerdo da linha que aparece quando você passa o mouse sobre elas.

5. Selecione **Guardar**.

   ![Menu do portal de ativação pós-falha automática](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>CLI do Azure

```azurecli-interactive
# Assume region order is initially eastus=0 westus=1 southeastasia=2 on account creation
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb failover-priority-change --name $accountName --resource-group $resourceGroupName --failover-policies eastus=0 southeastasia=1 westus=2
```

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1, Southeast Asia = 2
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$failoverPolicies = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="Southeast Asia"; "failoverPriority"=1 },
    @{ "locationName"="East US"; "failoverPriority"=2 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a id="manual-failover"></a>Executar failover manual em uma conta do Azure Cosmos

> [!IMPORTANT]
> A conta do Azure Cosmos deve ser configurada para failover manual para que essa operação seja realizada com sucesso.

O processo para executar um failover manual envolve alterar a região de gravação da conta (prioridade de failover = 0) para outra região configurada para a conta.

> [!NOTE]
> Não é possível fazer failover manual das contas de vários mestres. Para aplicativos que usam o SDK do cosmos do Azure, o SDK detectará quando uma região ficar indisponível e, em seguida, redirecionará automaticamente para a região mais próxima se usar a API de hospedagem múltipla no SDK.

### <a id="enable-manual-failover-via-portal"></a>Portal do Azure

1. Acesse sua conta do Azure Cosmos e abra o menu **replicar dados globalmente** .

2. Na parte superior do menu, selecione **failover manual**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No menu **failover manual** , selecione sua nova região de gravação. Marque a caixa de seleção para indicar que você entende que essa opção altera sua região de gravação.

4. Para disparar o failover, selecione **OK**.

   ![Menu do portal de ativação pós-falha manual](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>CLI do Azure

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=eastus failoverPriority=1 isZoneRedundant=False
```

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

$account.Properties.locations=$locations;
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações e exemplos sobre como gerenciar a conta do Azure Cosmos, bem como banco de dados e contêineres, leia os seguintes artigos:

* [Gerenciar Azure Cosmos DB usando Azure PowerShell](manage-with-powershell.md)
* [Manage Azure Cosmos DB using Azure CLI](manage-with-cli.md) (Gerir o Azure Cosmos DB com a CLI do Azure)
