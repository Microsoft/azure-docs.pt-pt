---
title: Modelos de Gestor de Recursos para API mesa DB Azure Cosmos
description: Utilize modelos do Gestor de Recursos Azure para criar e configurar a API da tabela Db Do Azure Cosmos.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588764"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Gerir os recursos da API da tabela Db da Azure Cosmos utilizando modelos de Gestor de Recursos Azure

Este artigo descreve como realizar diferentes operações para automatizar a gestão das suas contas, bases de dados e contentores Do MB D SAL utilizando modelos de Gestor de Recursos Azure. Este artigo tem exemplos apenas para contas De Tabela API, para encontrar exemplos para outras contas do tipo API ver: use modelos de Gestor de Recursos Azure com API do Azure Cosmos DB para [Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB,](manage-mongodb-with-resource-manager.md) [Artigos SQL.](manage-sql-with-resource-manager.md)

## Criar a conta e a tabela Azure Cosmos<a id="create-resource"></a>

Crie recursos DB Azure Cosmos utilizando um modelo de Gestor de Recursos Azure. Este modelo criará uma conta Azure Cosmos para a Tabela API com uma tabela a 400 RU/s. Copie o modelo e desloque-se como mostrado abaixo ou visite a [Galeria Azure Quickstart](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) e desloque-se a partir do portal Azure. Também pode descarregar o modelo para o seu computador local ou criar um novo modelo e especificar o caminho local com o parâmetro `--template-file`.

> [!NOTE]
> Os nomes das contas devem ser minúsculos e 44 ou menos caracteres.
> Para atualizar o RU/s, reenvie o modelo com valores de propriedade de entrada atualizados.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>Implantação via PowerShell

Para implementar o modelo de Gestor de Recursos utilizando powerShell, **copie** o script e selecione **Experimente-o** para abrir a Azure Cloud Shell. Para colar o script, clique na concha e, em seguida, selecione **Pasta:**

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

Se optar por utilizar uma versão instalada localmente do PowerShell em vez da concha Azure Cloud, tem de [instalar](/powershell/azure/install-az-ps) o módulo PowerShell Azure. Executar `Get-Module -ListAvailable Az` para localizar a versão.

### <a name="deploy-via-the-azure-cli"></a>Implantação através do Azure CLI

Para implementar o modelo de Gestor de Recursos Azure utilizando o AZURE CLI, **copie** o script e selecione **Experimente-o** para abrir a Azure Cloud Shell. Para colar o script, clique na concha e, em seguida, selecione **Pasta:**

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

O comando `az cosmosdb show` mostra a recém-criada conta Azure Cosmos depois de ter sido provisionada. Se optar por utilizar uma versão instalada localmente do Azure CLI em vez de utilizar a Cloud Shell, consulte o artigo [do Azure CLI.](/cli/azure/)

## <a name="next-steps"></a>Passos seguintes

Aqui estão alguns recursos adicionais:

- [Documentação do Gestor de Recursos Azure](/azure/azure-resource-manager/)
- [Esquema de fornecedor de recursos Da Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de Quickstart Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Problemas de resolução de erros comuns de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/common-deployment-errors.md)