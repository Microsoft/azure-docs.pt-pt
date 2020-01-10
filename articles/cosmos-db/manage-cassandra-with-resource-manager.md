---
title: Modelos do Resource Manager para Azure Cosmos DB API do Cassandra
description: Use modelos de Azure Resource Manager para criar e configurar Azure Cosmos DB API do Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 54d1c27148f20c89e17b343f0ac87295f978c140
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445284"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Gerenciar Azure Cosmos DB API do Cassandra recursos usando modelos de Azure Resource Manager

Este artigo descreve como executar operações diferentes para automatizar o gerenciamento de suas contas de Azure Cosmos DB, bancos de dados e contêineres usando modelos de Azure Resource Manager. Este artigo tem exemplos apenas para contas de API do SQL, para encontrar exemplos para outras contas de tipo de API, consulte: usar modelos de Azure Resource Manager com a API de Azure Cosmos DB para [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), artigos de [tabela](manage-table-with-resource-manager.md) .

## Criar conta do Azure Cosmos, espaço de keyspace e tabela<a id="create-resource"></a>

Crie Azure Cosmos DB recursos usando um modelo de Azure Resource Manager. Este modelo criará uma conta do Azure Cosmos para API do Cassandra com duas tabelas que compartilham a taxa de transferência de 400 RU/s no nível de keyspace. Copie o modelo e implante-o conforme mostrado abaixo ou visite a [Galeria de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) e implante do portal do Azure. Você também pode baixar o modelo em seu computador local ou criar um novo modelo e especificar o caminho local com o parâmetro `--template-file`.

> [!NOTE]
> Os nomes de conta devem ser minúsculos e 44 ou menos caracteres.
> Para atualizar RU/s, envie novamente o modelo com valores de propriedade de produtividade atualizados.

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-the-azure-cli"></a>Implantar com o CLI do Azure

Para implantar o modelo de Azure Resource Manager usando o CLI do Azure, **Copie** o script e selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**:

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

O comando `az cosmosdb show` mostra a conta recém-criada do Azure Cosmos depois que ela é provisionada. Se você optar por usar uma versão instalada localmente do CLI do Azure em vez de usar Cloud Shell, consulte o artigo [CLI do Azure](/cli/azure/) .


## <a name="next-steps"></a>Passos seguintes

Seguem-se alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do provedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de início rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Solucionar erros comuns de implantação de Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
