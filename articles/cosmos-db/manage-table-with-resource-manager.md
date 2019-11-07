---
title: Modelos de Azure Resource Manager para Azure Cosmos DB API de Tabela
description: Use modelos de Azure Resource Manager para criar e configurar Azure Cosmos DB API de Tabela.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: aa169ed7001ad858a2a0373f9d5bbbe770a2a727
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73604470"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Gerenciar Azure Cosmos DB API de Tabela recursos usando modelos de Azure Resource Manager

Este artigo descreve como executar operações diferentes para automatizar o gerenciamento de suas contas de Azure Cosmos DB, bancos de dados e contêineres usando modelos de Azure Resource Manager. Este artigo tem exemplos somente para contas de API de Tabela, para encontrar exemplos para outras contas de tipo de API, consulte: usar modelos do Resource Manager com a API do Azure Cosmos DB para [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), artigos do [SQL](manage-sql-with-resource-manager.md) .

## Criar conta e tabela do Azure Cosmos<a id="create-resource"></a>

Crie Azure Cosmos DB recursos usando um modelo de Azure Resource Manager. Este modelo criará uma conta do Azure Cosmos para API de Tabela com uma tabela à taxa de transferência de 400 RU/s. Copie o modelo e implante-o conforme mostrado abaixo ou visite a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) e implante do portal do Azure. Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o parâmetro `--template-file`.

> [!NOTE]
> Os nomes de conta devem ter letras minúsculas e < 31 caracteres.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Implantar por meio do PowerShell

Para implantar o modelo do Resource Manager usando o PowerShell, **Copie** o script e selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

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

Se você optar por usar uma versão instalada localmente do PowerShell em vez do Azure cloud Shell, será necessário [instalar](/powershell/azure/install-az-ps) o módulo Azure PowerShell. Executar `Get-Module -ListAvailable Az` para localizar a versão.

### <a name="deploy-via-azure-cli"></a>Implantar via CLI do Azure

Para implantar o modelo do Resource Manager usando CLI do Azure, **Copie** o script e selecione **Experimente** para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

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

O comando `az cosmosdb show` mostra a conta recém-criada do Azure Cosmos depois que ela é provisionada. Se você optar por usar uma versão instalada localmente do CLI do Azure em vez de usar o CloudShell, consulte o artigo [CLI (interface de linha de comando) do Azure](/cli/azure/) .

## Atualizar taxa de transferência (RU/s) em uma tabela<a id="table-ru-update"></a>

O modelo a seguir atualizará a taxa de transferência de uma tabela. Copie o modelo e implante-o conforme mostrado abaixo ou visite a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/) e implante do portal do Azure. Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o parâmetro `--template-file`.

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>Implantar a taxa de transferência de tabela por meio do PowerShell

Para implantar o modelo do Resource Manager usando o PowerShell, **Copie** o script e selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

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

### <a name="deploy-table-template-via-azure-cli"></a>Implantar modelo de tabela via CLI do Azure

Para implantar o modelo do Resource Manager usando CLI do Azure, selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

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
- [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de início rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Solucionar erros comuns de implantação de Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)