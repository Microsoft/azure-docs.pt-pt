---
title: Saiba como gerir contas de base de dados no Azure Cosmos DB
description: Saiba como gerir contas de base de dados no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: abd50f4e2ca08bea2af491f4b3991278a6dc3b5e
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399889"
---
# <a name="manage-an-azure-cosmos-account"></a>Gerir uma conta do Cosmos do Azure

Este artigo descreve como gerir várias tarefas numa conta do Cosmos do Azure com o portal do Azure, Azure PowerShell, CLI do Azure e modelos Azure Resource Manager.

## <a name="create-an-account"></a>Criar uma conta

### <a id="create-database-account-via-portal"></a>Portal do Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>CLI do Azure

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case.

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations WestUS=0 EastUS=1 \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
```azurepowershell-interactive
# Create an Azure Cosmos account for Core (SQL) API
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

### <a id="create-database-account-via-arm-template"></a>Modelo Azure Resource Manager

Este modelo do Azure Resource Manager irá criar uma conta do Cosmos do Azure para qualquer API suportada, configurada com duas regiões e as opções para selecionar o nível de consistência, a ativação pós-falha automática e com vários mestres. Para implementar este modelo, clique em implementar no Azure na página Leia-me, [conta do Cosmos do Azure de criar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)

## <a name="addremove-regions-from-your-database-account"></a>Adicionar/remover regiões da conta de base de dados

### <a id="add-remove-regions-via-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

1. Aceda à sua conta do Cosmos do Azure e abra o **replicar dados globalmente** menu.

1. Para adicionar regiões, selecione os Hexágonos no mapa com o **+** etiqueta que corresponda ao seu regiões pretendido. Em alternativa, para adicionar uma região, selecione o **+ adicionar região** opção e selecione uma região no menu pendente.

1. Para remover regiões, limpe um ou mais regiões do mapa de selecionando os Hexágonos azuis com marcas de seleção. Ou selecione "lixeira" (🗑) ícone ao lado de região no lado direito.

1. Para guardar as alterações, selecione **OK**.

   ![Adicionar ou remover o menu de regiões](./media/how-to-manage-database-account/add-region.png)

Numa única região crie modo, que não é possível remover a região de escrita. Deve efetuar a ativação pós-falha para uma região diferente antes de poder eliminar a região de escrita atual.

Numa região multi escreva modo, pode adicionar ou remover qualquer região, se tiver pelo menos uma região.

### <a id="add-remove-regions-via-cli"></a>CLI do Azure

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations westus=0

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

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

## <a id="configure-multiple-write-regions"></a>Configurar várias regiões de escrita

### <a id="configure-multiple-write-regions-portal"></a>Portal do Azure

Abra o **replicar dados globalmente** separador e selecione **ativar** para ativar a escrita de várias regiões. Depois de ativar a escrita de várias regiões, todas as regiões de leitura que já existe na conta irão tornar-se ler e escrever regiões. 

> [!NOTE]
> Depois de ativar a escrita de várias regiões, não poderá desativá-lo. 

![Conta do Cosmos do Azure configura vários mestre captura de ecrã](./media/how-to-manage-database-account/single-to-multi-master.png)

Entre em contacto para o askcosmosdb@microsoft.com alias para mais questões sobre esta funcionalidade. 

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

Uma conta pode ser migrada do principal de único para múltiplos principais ao implementar o modelo do Resource Manager utilizado para criar a conta e a definição `enableMultipleWriteLocations: true`. O modelo Azure Resource Manager seguinte é um modelo de mínimo bare que irá implementar uma conta do Cosmos do Azure para a API de SQL com uma única região e com vários mestres ativada.

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
                "locations": [
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Ativar a ativação pós-falha automática para a sua conta do Cosmos do Azure

A opção de ativação pós-falha automática permite do Azure Cosmos DB para ativação pós-falha para a região com a máxima prioridade de ativação pós-falha com nenhuma ação do utilizador deve uma região ficar indisponível. Quando a ativação pós-falha automática está ativada, a prioridade da região pode ser modificada. Conta tem de ter duas ou mais regiões para ativar a ativação pós-falha automática.

### <a id="enable-automatic-failover-via-portal"></a>Portal do Azure

1. A partir da sua conta do Cosmos do Azure, abra a **replicar dados globalmente** painel.

2. Na parte superior do painel, selecione **a ativação pós-falha automática**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **a ativação pós-falha automática** painel, certifique-se de que **ativar a ativação pós-falha automática** está definida como **ON**. 

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

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Definir prioridades de ativação pós-falha para a sua conta do Cosmos do Azure

Depois de uma conta do Cosmos estiver configurada para ativação pós-falha automática, a prioridade de ativação pós-falha para regiões pode ser alterada.

> [!IMPORTANT]
> Não é possível modificar a região de escrita (prioridade de ativação pós-falha de zero) quando a conta está configurada para ativação pós-falha automática. Para alterar a região de escrita, tem de desativar a ativação pós-falha automática e fazer uma ativação pós-falha manual.

### <a id="set-failover-priorities-via-portal"></a>Portal do Azure

1. A partir da sua conta do Cosmos do Azure, abra a **replicar dados globalmente** painel.

2. Na parte superior do painel, selecione **a ativação pós-falha automática**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **a ativação pós-falha automática** painel, certifique-se de que **ativar a ativação pós-falha automática** está definida como **ON**.

4. Para modificar a prioridade de ativação pós-falha, arraste as regiões de leitura por meio de três pontos no lado esquerdo da linha que aparecem quando paira o rato sobre os mesmos.

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

## <a id="manual-failover"></a>Efetuar ativação pós-falha manual numa conta do Cosmos do Azure

> [!IMPORTANT]
> A conta do Cosmos do Azure tem de ser configurada para ativação pós-falha manual para esta operação seja bem-sucedida.

O processo para executar uma ativação pós-falha manual envolve a mudar a região de escrita da conta (a prioridade de ativação pós-falha = 0) para outra região configurada para a conta.

> [!NOTE]
> Contas de vários mestres não é possível manualmente efetuar a ativação pós-falha. Para aplicações utilizando o SDK do Cosmos do Azure, o SDK irá detectar quando uma região fica indisponível, em seguida, redirecionar automaticamente para a região mais próxima seguinte, se utilizar a API multi homing no SDK.

### <a id="enable-manual-failover-via-portal"></a>Portal do Azure

1. Aceda à sua conta do Cosmos do Azure e abra o **replicar dados globalmente** menu.

2. Na parte superior do menu, selecione **ativação pós-falha do Manual**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Sobre o **ativação pós-falha do Manual** menu, selecione a sua nova região de escrita. Selecione a caixa de verificação para indicar que compreende que esta opção altera a sua região de escrita.

4. Para acionar a ativação pós-falha, selecione **OK**.

   ![Menu do portal de ativação pós-falha manual](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>CLI do Azure

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1
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

Para obter mais informações e exemplos sobre como gerir a conta do Cosmos do Azure, bem como a base de dados e contentores, leia os artigos seguintes:

* [Gerir o Azure Cosmos DB com o Azure PowerShell](manage-with-powershell.md)
* [Gerir o Azure Cosmos DB com a CLI do Azure](manage-with-cli.md)
