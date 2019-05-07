---
title: Modelos do Azure Resource Manager para a API de tabela do Azure Cosmos DB
description: Utilize modelos Azure Resource Manager para criar e configurar a API de tabela do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 33e47d67365e76142d5b584d49d8e7265445bf03
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077609"
---
# <a name="create-azure-cosmos-db-table-api-resources-from-a-resource-manager-template"></a>Criar recursos de API de tabela do Azure Cosmos DB a partir de um modelo do Resource Manager

Saiba como criar um recursos de API de tabela do Azure Cosmos DB com um modelo Azure Resource Manager. O exemplo seguinte cria uma API de tabela do Azure Cosmos DB de uma [modelo de início rápido do Azure](https://aka.ms/table-arm-qs). Este modelo irá criar uma conta do Cosmos do Azure para a API de tabela com uma tabela com um débito de 400 RU/s.

Esta é uma cópia do modelo:

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

## <a name="deploy-via-powershell"></a>Implementar através do PowerShell

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

No exemplo anterior, tem de referenciar um modelo armazenado no GitHub. Também pode transferir o modelo para o computador local ou criar um novo modelo e especifique o caminho local com o `--template-file` parâmetro.


## <a name="deploy-via-azure-cli"></a>Implementar através da CLI do Azure

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

No exemplo anterior, tem de referenciar um modelo armazenado no GitHub. Também pode transferir o modelo para o computador local ou criar um novo modelo e especifique o caminho local com o `--template-file` parâmetro.


## <a name="next-steps"></a>Próximos Passos

Aqui estão alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do fornecedor de recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de início rápido do Cosmos DB do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Resolver erros de implementação comuns do Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)