---
title: Modelos de Gestor de Recursos para API Db Cassandra API do Azure Cosmos
description: Utilize modelos do Gestor de Recursos Azure para criar e configurar a API DB Cassandra do Azure Cosmos.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: c4dc97453fe50865db74f8918ef3dffdb4013b4f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251898"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Gerir os recursos da API da Azure Cosmos DB Cassandra utilizando modelos de Gestor de Recursos Azure

Este artigo descreve como realizar diferentes operações para automatizar a gestão das suas contas, bases de dados e contentores Do MB D SAL utilizando modelos de Gestor de Recursos Azure. Este artigo tem exemplos apenas para contas SQL API, para encontrar exemplos para outras contas do tipo API ver: use modelos de Gestor de Recursos Azure com API do Azure Cosmos DB para [SQL,](manage-sql-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB,](manage-mongodb-with-resource-manager.md) [Artigos](manage-table-with-resource-manager.md) de mesa.

## Criar conta Azure Cosmos, keyspace e mesa<a id="create-resource"></a>

Crie recursos DB Azure Cosmos utilizando um modelo de Gestor de Recursos Azure. Este modelo criará uma conta Azure Cosmos para Cassandra API com duas tabelas que partilham 400 RU/s de entrada no nível do espaço-chave. Copie o modelo e desloque-se como mostrado abaixo ou visite a [Galeria Azure Quickstart](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) e desloque-se a partir do portal Azure. Também pode descarregar o modelo para o seu computador local ou criar um novo modelo e especificar o caminho local com o parâmetro `--template-file`.

> [!NOTE]
> Os nomes das contas devem ser minúsculos e 44 ou menos caracteres.
> Para atualizar o RU/s, reenvie o modelo com valores de propriedade de entrada atualizados.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Implantação com o Azure CLI

Para implementar o modelo de Gestor de Recursos Azure utilizando o AZURE CLI, **copie** o script e selecione **Experimente-o** para abrir a Azure Cloud Shell. Para colar o script, clique na concha e, em seguida, selecione **Pasta:**

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

O comando `az cosmosdb show` mostra a recém-criada conta Azure Cosmos depois de ter sido provisionada. Se optar por utilizar uma versão instalada localmente do Azure CLI em vez de utilizar a Cloud Shell, consulte o artigo [do Azure CLI.](/cli/azure/)


## <a name="next-steps"></a>Passos seguintes

Aqui estão alguns recursos adicionais:

- [Documentação do Gestor de Recursos Azure](/azure/azure-resource-manager/)
- [Esquema de fornecedor de recursos Da Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de Quickstart Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Problemas de resolução de erros comuns de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/common-deployment-errors.md)
