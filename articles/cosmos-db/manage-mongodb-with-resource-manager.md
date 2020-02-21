---
title: Modelos de Gestor de Recursos para Azure Cosmos DB API para MongoDB
description: Utilize modelos do Gestor de Recursos Azure para criar e configurar a API DB Azure Cosmos para MongoDB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 65838c36cadba580c578677185683e27b57b1e27
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525503"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gerir os recursos da API Do Azure Cosmos DB MongoDB utilizando modelos de Gestor de Recursos Azure

Este artigo descreve como realizar diferentes operações para automatizar a gestão das suas contas, bases de dados e contentores Do MB D SAL utilizando modelos de Gestor de Recursos Azure. Este artigo tem exemplos para a API da Azure Cosmos DB apenas para MongoDB, para encontrar exemplos para outras contas do tipo API ver: use modelos de Gestor de Recursos Azure com API do Azure Cosmos DB para [Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [SQL,](manage-sql-with-resource-manager.md)Artigos de [mesa.](manage-table-with-resource-manager.md)

## Criar a API DB Azure Cosmos para conta MongoDB, base de dados e recolha<a id="create-resource"></a>

Crie recursos DB Azure Cosmos utilizando um modelo de Gestor de Recursos Azure. Este modelo criará uma conta Azure Cosmos para a MongoDB API com duas coleções que partilham 400 ru/s de entrada ao nível da base de dados. Copie o modelo e desloque-se como mostrado abaixo ou visite a [Galeria Azure Quickstart](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) e desloque-se a partir do portal Azure. Também pode descarregar o modelo para o seu computador local ou criar um novo modelo e especificar o caminho local com o parâmetro `--template-file`.

> [!NOTE]
> Os nomes das contas devem ser minúsculos e 44 ou menos caracteres.
> Para atualizar o RU/s, reenvie o modelo com valores de propriedade de entrada atualizados.
>
> Atualmente só pode criar a versão 3.2 (isto é, contas usando o ponto final no formato `*.documents.azure.com`) da API do Azure Cosmos DB para contas MongoDB utilizando modelos PowerShell, CLI e Resource Manager. Para criar 3.6 versão de contas, utilize o portal Azure.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-the-azure-cli"></a>Implantação através do Azure CLI

Para implementar o modelo de Gestor de Recursos Azure utilizando o AZURE CLI, **copie** o script e selecione **Experimente-o** para abrir a Azure Cloud Shell. Para colar o script, clique na concha e, em seguida, selecione **Pasta:**

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

O comando `az cosmosdb show` mostra a recém-criada conta Azure Cosmos depois de ter sido provisionada. Se optar por utilizar uma versão instalada localmente do Azure CLI em vez de utilizar a Cloud Shell, consulte o artigo [do Azure CLI.](/cli/azure/)

## <a name="next-steps"></a>Passos seguintes

Aqui estão alguns recursos adicionais:

- [Documentação do Gestor de Recursos Azure](/azure/azure-resource-manager/)
- [Esquema de fornecedor de recursos Da Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Modelos de Quickstart Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Problemas de resolução de erros comuns de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/common-deployment-errors.md)