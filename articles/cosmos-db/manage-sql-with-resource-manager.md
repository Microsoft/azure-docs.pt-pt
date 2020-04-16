---
title: Criar e gerir o Azure Cosmos DB com modelos de Gestor de Recursos
description: Utilize modelos do Gestor de Recursos Azure para criar e configurar o Azure Cosmos DB para API SQL (Core)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 3514b3e77781010fd56b43229f87854ea2d591e8
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390895"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Gerir os recursos da API Azure Cosmos DB SQL (Core) com modelos de Gestor de Recursos Azure

Neste artigo, vai aprender a utilizar os modelos do Azure Resource Manager para ajudar a automatizar a gestão das contas, das bases de dados e dos contentores do Azure Cosmos DB.

Este artigo apenas mostra exemplos de modelo sql resource manager para contas SQL API. Você também pode encontrar exemplos de modelo para [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md)e APIs [de mesa.](manage-table-with-resource-manager.md)

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Criar uma conta Azure Cosmos, base de dados e contentor

O seguinte modelo de Gestor de Recursos Azure cria uma conta Azure Cosmos com:

* Dois recipientes que partilham 400 Unidades Requeridas por segundo (RU/s) ao nível da base de dados.
* Um recipiente com entrada dedicada de 400 RU/s.

Para criar os recursos DB Azure Cosmos, copie o seguinte modelo de exemplo e implemente-o conforme descrito, seja através [do PowerShell](#deploy-via-powershell) ou [do Azure CLI](#deploy-via-azure-cli).

* Opcionalmente, pode visitar a [Galeria Azure Quickstart](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) e implementar o modelo a partir do portal Azure.
* Também pode descarregar o modelo para o seu computador local ou `--template-file` criar um novo modelo e especificar o caminho local com o parâmetro.

> [!IMPORTANT]
>
> * Quando adiciona ou remove localizações a uma conta Azure Cosmos, não pode simultaneamente modificar outras propriedades. Estas operações devem ser feitas separadamente.
> * Os nomes das contas estão limitados a 44 caracteres, todos minúsculos.
> * Para alterar os valores de entrada, reenvie o modelo com RU/s atualizado.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

> [!NOTE]
> Para criar um recipiente com grande chave de `"version":2` partição, modifique o modelo anterior para incluir a propriedade dentro do `partitionKey` objeto.

### <a name="deploy-via-powershell"></a>Implantação via PowerShell

Para utilizar o PowerShell para implementar o modelo de Gestor de Recursos Azure:

1. **Copie** o guião.
2. Selecione **Experimente** para abrir a Casca de Nuvem Azure.
3. Clique à direita na janela Azure Cloud Shell e, em seguida, selecione **Paste**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Pode optar por implementar o modelo com uma versão instalada localmente do PowerShell em vez de Azure Cloud Shell. Terá de [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Corra `Get-Module -ListAvailable Az` para encontrar a versão necessária.

### <a name="deploy-via-azure-cli"></a>Implantação via Azure CLI

Para utilizar o Azure CLI para implementar o modelo de Gestor de Recursos Azure:

1. **Copie** o guião.
2. Selecione **Experimente** para abrir a Casca de Nuvem Azure.
3. Clique à direita na janela Azure Cloud Shell e, em seguida, selecione **Paste**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O `az cosmosdb show` comando mostra a recém-criada conta Azure Cosmos depois de ser provisionada. Pode optar por implementar o modelo com uma versão instalada localmente do Azure CLI em vez de Azure Cloud Shell. Para mais informações, consulte o artigo Interface [de Linha de Comando Azure (CLI).](/cli/azure/)

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Crie um recipiente Azure Cosmos DB com funcionalidade do lado do servidor

Pode utilizar um modelo de Gestor de Recursos Azure para criar um recipiente Azure Cosmos DB com um procedimento armazenado, gatilho e função definida pelo utilizador.

Copie o seguinte modelo de exemplo e desimplante-o conforme descrito, quer com [powerShell](#deploy-with-powershell) quer [Azure CLI](#deploy-with-azure-cli).

* Opcionalmente, pode visitar a [Galeria Azure Quickstart](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) e implementar o modelo a partir do portal Azure.
* Também pode descarregar o modelo para o seu computador local ou `--template-file` criar um novo modelo e especificar o caminho local com o parâmetro.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

### <a name="deploy-with-powershell"></a>Implementar com o PowerShell

Para utilizar o PowerShell para implementar o modelo de Gestor de Recursos Azure:

1. **Copie** o guião.
1. Selecione **Experimente** para abrir a Casca de Nuvem Azure.
1. Clique na janela Azure Cloud Shell e, em seguida, selecione **Paste**.

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Pode optar por implementar o modelo com uma versão instalada localmente do PowerShell em vez de Azure Cloud Shell. Terá de [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Corra `Get-Module -ListAvailable Az` para encontrar a versão necessária.

### <a name="deploy-with-azure-cli"></a>Implementar com a CLI do Azure

Para utilizar o Azure CLI para implementar o modelo de Gestor de Recursos Azure:

1. **Copie** o guião.
2. Selecione **Experimente** para abrir a Casca de Nuvem Azure.
3. Clique à direita na janela Azure Cloud Shell e, em seguida, selecione **Paste**.

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Passos seguintes

Seguem-se alguns recursos adicionais:

* [Documentação do Gestor de Recursos Azure](/azure/azure-resource-manager/)
* [Esquema de fornecedor de recursos Da Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de Quickstart Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Problemas de resolução de erros comuns de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/common-deployment-errors.md)
