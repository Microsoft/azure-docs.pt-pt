---
title: Modelos de gestor de recursos para Azure Cosmos DB Cassandra API
description: Utilize modelos de Gestor de Recursos Azure para criar e configurar a Azure Cosmos DB Cassandra API.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: eb746e8105717883b029586db2bfce8e31290e93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340673"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Gerir recursos da Azure Cosmos DB Cassandra API utilizando modelos de Gestor de Recursos Azure
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Neste artigo, você aprende a usar modelos do Azure Resource Manager para ajudar a implementar e gerir as suas contas DB Azure Cosmos, espaços-chave e tabelas.

Este artigo tem exemplos apenas para contas da API de Cassandra, para encontrar exemplos para outras contas tipo API ver: use modelos de Gestor de Recursos Azure com AZure Cosmos DB's API para [SQL,](templates-samples-sql.md) [Gremlin,](templates-samples-gremlin.md) [MongoDB,](templates-samples-mongodb.md) [Artigos de tabela.](templates-samples-table.md)

> [!IMPORTANT]
>
> * Os nomes das contas estão limitados a 44 caracteres, todos minúsculos.
> * Para alterar os valores de produção, reimplante o modelo com RU/s atualizados.
> * Quando adiciona ou remove localizações a uma conta Azure Cosmos, não pode modificar simultaneamente outras propriedades. Estas operações devem ser efetuadas separadamente.

Para criar qualquer um dos recursos DB do Azure Cosmos abaixo, copie o modelo de exemplo a seguir num novo ficheiro json. Pode criar opcionalmente um ficheiro json de parâmetros para utilizar ao implementar várias instâncias do mesmo recurso com diferentes nomes e valores. Existem muitas formas de implementar modelos de Gestor de Recursos Azure, incluindo, [portal Azure](../azure-resource-manager/templates/deploy-portal.md) [CLI,](../azure-resource-manager/templates/deploy-cli.md) [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub.](../azure-resource-manager/templates/deploy-to-azure-button.md)

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>Conta de Azure Cosmos para Cassandra com produção de autoescala

Este modelo cria uma conta Azure Cosmos em duas regiões com opções de consistência e failover, com um espaço-chave e tabela configurada para a produção de escala automática. Este modelo também está disponível para uma implementação de um clique a partir da Galeria de Modelos Azure Quickstart.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-provisioned-throughput"></a>Conta de Azure Cosmos para Cassandra com produção padrão a provisionada

Este modelo cria uma conta Azure Cosmos em duas regiões com opções de consistência e failover, com um espaço-chave e tabela configurada para produção padrão. Este modelo também está disponível para uma implementação de um clique a partir da Galeria de Modelos Azure Quickstart.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>Passos seguintes

Seguem-se alguns recursos adicionais:

* [Documentação do Azure Resource Manager](../azure-resource-manager/index.yml)
* [Esquema de fornecedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de arranque rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Resolução de problemas erros comuns de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/common-deployment-errors.md)