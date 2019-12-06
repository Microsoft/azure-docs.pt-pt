---
title: Criar e gerenciar Azure Cosmos DB com modelos de Azure Resource Manager
description: Usar modelos de Azure Resource Manager para criar e configurar o Azure Cosmos DB para a API do SQL (Core)
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 62c04fed03ad2346d0f548a4a8028f2d7d6b3486
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850470"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-with-azure-resource-manager-templates"></a>Gerenciar recursos de API do Azure Cosmos DB SQL (núcleo) com modelos de Azure Resource Manager

Neste artigo, você aprenderá a usar os modelos de Azure Resource Manager para ajudar a automatizar o gerenciamento de suas contas de Azure Cosmos DB, bancos de dados e contêineres.

Este artigo mostra apenas Azure Resource Manager exemplos de modelo para contas da API do SQL. Você também pode encontrar exemplos de modelo para [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)e APIs de [tabela](manage-table-with-resource-manager.md) .

<a id="create-resource"></a>

## <a name="create-an-azure-cosmos-account-database-and-container"></a>Criar uma conta, um banco de dados e um contêiner do Azure Cosmos

O modelo a seguir Azure Resource Manager cria uma conta do Azure cosmos com:

* Dois contêineres que compartilham a taxa de transferência de 400 unidades solicitadas por segundo (RU/s) no nível do banco de dados.
* Um contêiner com taxa de transferência dedicada de 400 RU/s.

Para criar os recursos de Azure Cosmos DB, copie o modelo de exemplo a seguir e implante-o conforme descrito, seja por meio do [PowerShell](#deploy-via-powershell) ou [CLI do Azure](#deploy-via-azure-cli).

* Opcionalmente, você pode visitar a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) e implantar o modelo do portal do Azure.
* Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o parâmetro `--template-file`.

> [!IMPORTANT]
>
> * Ao adicionar ou remover locais de uma conta do Azure Cosmos, você não pode modificar outras propriedades simultaneamente. Essas operações devem ser feitas separadamente.
> * Os nomes de conta são limitados a 44 caracteres, tudo em minúsculas.
> * Para alterar os valores de taxa de transferência, envie novamente o modelo com RU/s atualizado.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

> [!NOTE]
> Para criar um contêiner com chave de partição grande, modifique o modelo anterior para incluir a propriedade `"version":2` dentro do objeto `partitionKey`.

### <a name="deploy-via-powershell"></a>Implantar por meio do PowerShell

Para usar o PowerShell para implantar o modelo de Azure Resource Manager:

1. **Copie** o script.
2. Selecione **experimentar** para abrir Azure cloud Shell.
3. Clique com o botão direito do mouse na janela Azure Cloud Shell e selecione **colar**.

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

Você pode optar por implantar o modelo com uma versão instalada localmente do PowerShell em vez de Azure Cloud Shell. Você precisará [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Execute `Get-Module -ListAvailable Az` para localizar a versão necessária.

### <a name="deploy-via-azure-cli"></a>Implantar via CLI do Azure

Para usar CLI do Azure para implantar o modelo de Azure Resource Manager:

1. **Copie** o script.
2. Selecione **experimentar** para abrir Azure cloud Shell.
3. Clique com o botão direito do mouse na janela Azure Cloud Shell e selecione **colar**.

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

O comando `az cosmosdb show` mostra a conta recém-criada do Azure Cosmos depois de ser provisionada. Você pode optar por implantar o modelo com uma versão instalada localmente do CLI do Azure em vez Azure Cloud Shell. Para obter mais informações, consulte o artigo da [CLI (interface de linha de comando) do Azure](/cli/azure/) .

<a id="create-sproc"></a>

## <a name="create-an-azure-cosmos-db-container-with-server-side-functionality"></a>Criar um contêiner de Azure Cosmos DB com a funcionalidade do lado do servidor

Você pode usar um modelo de Azure Resource Manager para criar um contêiner de Azure Cosmos DB com um procedimento armazenado, um gatilho e uma função definida pelo usuário.

Copie o modelo de exemplo a seguir e implante-o conforme descrito, seja com o [PowerShell](#deploy-with-powershell) ou [CLI do Azure](#deploy-with-azure-cli).

* Opcionalmente, você pode visitar a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) e implantar o modelo do portal do Azure.
* Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o parâmetro `--template-file`.

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-with-powershell"></a>Implementar com o Powershell

Para usar o PowerShell para implantar o modelo de Azure Resource Manager:

1. **Copie** o script.
1. Selecione **experimentar** para abrir Azure cloud Shell.
1. Clique com o botão direito do mouse na janela Azure Cloud Shell e selecione **colar**.

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

Você pode optar por implantar o modelo com uma versão instalada localmente do PowerShell em vez de Azure Cloud Shell. Você precisará [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Execute `Get-Module -ListAvailable Az` para localizar a versão necessária.

### <a name="deploy-with-azure-cli"></a>Implementar com o CLI do Azure

Para usar CLI do Azure para implantar o modelo de Azure Resource Manager:

1. **Copie** o script.
2. Selecione **experimentar** para abrir Azure cloud Shell.
3. Clique com o botão direito do mouse na janela Azure Cloud Shell e selecione **colar**.

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

* [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
* [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de início rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Solucionar erros comuns de implantação de Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
