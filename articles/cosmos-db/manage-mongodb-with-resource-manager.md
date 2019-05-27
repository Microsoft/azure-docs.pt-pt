---
title: Modelos do Azure Resource Manager para a API MongoDB do Azure Cosmos DB
description: Utilize modelos Azure Resource Manager para criar e configurar a API do Azure Cosmos DB para o MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 99f1e41107c277c8b3f1b21f81952d5d5cadaa29
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968879"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gerir recursos de API do MongoDB do Azure Cosmos DB utilizando modelos do Azure Resource Manager

## Criar API do Azure Cosmos DB para a conta do MongoDB, base de dados e coleção <a id="create-resource"></a>

Crie recursos do Azure Cosmos DB com um modelo Azure Resource Manager. Este modelo irá criar uma conta do Cosmos do Azure para a MongoDB API com duas coleções que partilham 400 débito de RU/s ao nível da base de dados. Copiar o modelo e implementar, conforme mostrado abaixo ou visite [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) e implementar a partir do portal do Azure. Também pode transferir o modelo para o computador local ou criar um novo modelo e especifique o caminho local com o `--template-file` parâmetro.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>Implementar através da CLI do Azure

Para implementar o modelo do Resource Manager com CLI do Azure, **cópia** de script e selecione **experimente** para abrir o Azure Cloud shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O `az cosmosdb show` comando mostra a conta do Cosmos do Azure recentemente criada após ele ter sido aprovisionado. Se optar por utilizar uma versão instalada localmente da CLI do Azure em vez de usar CloudShell, consulte [Interface de linha de comandos (CLI do Azure)](/cli/azure/) artigo.

## Atualizar o débito (RU/s) num banco de dados <a id="database-ru-update"></a>

O modelo seguinte irá atualizar o débito das bases de dados. Copiar o modelo e implementar, conforme mostrado abaixo ou visite [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/) e implementar a partir do portal do Azure. Também pode transferir o modelo para o computador local ou criar um novo modelo e especifique o caminho local com o `--template-file` parâmetro.

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Implementar o modelo de banco de dados através da CLI do Azure

Para implementar o modelo do Resource Manager com CLI do Azure, selecione **experimente** para abrir o Azure Cloud shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Atualizar o débito (RU/s) numa coleção <a id="collection-ru-update"></a>

O modelo seguinte irá atualizar o débito de uma coleção. Copiar o modelo e implementar, conforme mostrado abaixo ou visite [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/) e implementar a partir do portal do Azure. Também pode transferir o modelo para o computador local ou criar um novo modelo e especifique o caminho local com o `--template-file` parâmetro.

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>Implementar o modelo de conjunto através da CLI do Azure

Para implementar o modelo do Resource Manager com CLI do Azure, selecione **experimente** para abrir o Azure Cloud shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the collection name: ' collectionName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName collectionName=$collectionName throughput=$throughput
```

## <a name="next-steps"></a>Próximos Passos

Aqui estão alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do fornecedor de recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de início rápido do Cosmos DB do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Resolver erros de implementação comuns do Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)