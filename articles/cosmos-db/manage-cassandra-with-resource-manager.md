---
title: Modelos do Azure Resource Manager para a API de Cassandra do Azure Cosmos DB
description: Utilize modelos Azure Resource Manager para criar e configurar a API de Cassandra do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 9878939a461f3ba35d2b6c270de2a965f66204cc
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077549"
---
# <a name="create-azure-cosmos-db-cassandra-api-resources-from-a-resource-manager-template"></a>Criar recursos de Cassandra API do Azure Cosmos DB a partir de um modelo do Resource Manager

Saiba como criar uma conta do Cosmos do Azure para a API de Cassandra através de um modelo Azure Resource Manager. O exemplo seguinte cria uma conta de API de Cassandra do Azure Cosmos DB a partir da [modelo de início rápido do Azure](https://aka.ms/cassandra-arm-qs). Este modelo irá criar uma conta do Cosmos do Azure para a API de Cassandra com duas tabelas que partilham 400 débito de RU/s ao nível do keyspace. 

Esta é uma cópia do modelo:

[!code-json[create-cosmos-Cassandra](~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Implementar com a CLI do Azure

Para implementar o modelo do Resource Manager com CLI do Azure, **cópia** de script e selecione **experimente** para abrir o Azure Cloud shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**:

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

O `az cosmosdb show` comando mostra a conta do Cosmos do Azure recentemente criada após ele ter sido aprovisionado. Se optar por utilizar uma versão instalada localmente da CLI do Azure em vez de usar CloudShell, consulte [Interface de linha de comandos (CLI do Azure)](/cli/azure/) artigo.

No exemplo anterior, tem de referenciar um modelo armazenado no GitHub. Também pode transferir o modelo para o computador local ou criar um novo modelo e especifique o caminho local com o `--template-file` parâmetro.

## <a name="next-steps"></a>Próximos Passos

Aqui estão alguns recursos adicionais:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/)
- [Esquema do fornecedor de recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de início rápido do Cosmos DB do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Resolver erros de implementação comuns do Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)