---
title: Modelos do Azure Resource Manager para a API de tabela do Azure Cosmos DB
description: Utilize modelos Azure Resource Manager para criar e configurar a API de tabela do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 82e2a436bf6b25b6164d845d234896390a262292
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968817"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Gerir recursos de API de tabela do Azure Cosmos DB utilizando modelos do Azure Resource Manager

## Criar conta do Cosmos do Azure e uma tabela <a id="create-resource"></a>

Crie recursos do Azure Cosmos DB com um modelo Azure Resource Manager. Este modelo irá criar uma conta do Cosmos do Azure para a API de tabela com uma tabela na taxa de transferência de RU/s 400. Copiar o modelo e implementar, conforme mostrado abaixo ou visite [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) e implementar a partir do portal do Azure. Também pode transferir o modelo para o computador local ou criar um novo modelo e especifique o caminho local com o `--template-file` parâmetro.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Implementar através do PowerShell

Para implementar o modelo do Resource Manager com o PowerShell, **cópia** de script e selecione **experimente** para abrir o Azure Cloud shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Se optar por utilizar uma versão instalada localmente do PowerShell, em vez de partir do Azure Cloud shell, terá [instalar](/powershell/azure/install-az-ps) o módulo Azure PowerShell. Executar `Get-Module -ListAvailable Az` para localizar a versão.

### <a name="deploy-via-azure-cli"></a>Implementar através da CLI do Azure

Para implementar o modelo do Resource Manager com CLI do Azure, **cópia** de script e selecione **experimente** para abrir o Azure Cloud shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O `az cosmosdb show` comando mostra a conta do Cosmos do Azure recentemente criada após ele ter sido aprovisionado. Se optar por utilizar uma versão instalada localmente da CLI do Azure em vez de usar CloudShell, consulte [Interface de linha de comandos (CLI do Azure)](/cli/azure/) artigo.

## Atualizar o débito (RU/s) numa tabela <a id="table-ru-update"></a>

O modelo seguinte irá atualizar o débito de uma tabela. Copiar o modelo e implementar, conforme mostrado abaixo ou visite [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/) e implementar a partir do portal do Azure. Também pode transferir o modelo para o computador local ou criar um novo modelo e especifique o caminho local com o `--template-file` parâmetro.

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>Implementar o débito de tabela através do PowerShell

Para implementar o modelo do Resource Manager com o PowerShell, **cópia** de script e selecione **experimente** para abrir o Azure Cloud shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$tableName = Read-Host -Prompt "Enter the table name"
$throughput = Read-Host -Prompt "Enter new throughput for table"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -tableName $tableName `
    -throughput $throughput
```

### <a name="deploy-table-template-via-azure-cli"></a>Implementar o modelo de tabela através da CLI do Azure

Para implementar o modelo do Resource Manager com CLI do Azure, selecione **experimente** para abrir o Azure Cloud shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>Próximos Passos

Aqui estão alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do fornecedor de recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de início rápido do Cosmos DB do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Resolver erros de implementação comuns do Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)