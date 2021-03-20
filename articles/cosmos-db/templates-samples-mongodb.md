---
title: Modelos de gestor de recursos para Azure Cosmos DB API para MongoDB
description: Utilize modelos de Gestor de Recursos Azure para criar e configurar a AZure Cosmos DB API para o MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 7664d48bad153b34e7557e9faaf4c8aa0d4215ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340633"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Gerir recursos da Azure Cosmos DB MongoDB API utilizando modelos do Gestor de Recursos Azure
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Neste artigo, você aprende a usar modelos do Azure Resource Manager para ajudar a implementar e gerir as suas contas DB Azure Cosmos para API, bases de dados e coleções da MongoDB.

Este artigo tem exemplos para a API da Azure Cosmos DB apenas para a MongoDB, para encontrar exemplos para outras contas do tipo API ver: use modelos de Gestor de Recursos Azure com API da Azure Cosmos DB para [Cassandra,](templates-samples-cassandra.md) [Gremlin,](templates-samples-gremlin.md) [SQL,](templates-samples-sql.md) [Artigos de tabela.](templates-samples-table.md)

> [!IMPORTANT]
>
> * Os nomes das contas estão limitados a 44 caracteres, todos minúsculos.
> * Para alterar os valores de produção, reimplante o modelo com RU/s atualizados.
> * Quando adiciona ou remove localizações a uma conta Azure Cosmos, não pode modificar simultaneamente outras propriedades. Estas operações devem ser efetuadas separadamente.

Para criar qualquer um dos recursos DB do Azure Cosmos abaixo, copie o modelo de exemplo a seguir num novo ficheiro json. Pode criar opcionalmente um ficheiro json de parâmetros para utilizar ao implementar várias instâncias do mesmo recurso com diferentes nomes e valores. Existem muitas formas de implementar modelos de Gestor de Recursos Azure, incluindo, [portal Azure](../azure-resource-manager/templates/deploy-portal.md) [CLI,](../azure-resource-manager/templates/deploy-cli.md) [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub.](../azure-resource-manager/templates/deploy-to-azure-button.md)

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>Azure Cosmos conta para MongoDB com produção de autoescala

Este modelo criará uma conta Azure Cosmos para a MongoDB API (3.2 ou 3.6) com duas coleções que partilham a produção de escala automática ao nível da base de dados. Este modelo também está disponível para uma implementação de um clique a partir da Galeria de Modelos Azure Quickstart.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>Azure Cosmos conta para a MongoDB com produção padrão abastada

Este modelo criará uma conta Azure Cosmos para a MongoDB API (3.2 ou 3.6) com duas coleções que partilham 400 RU/s padrão (manual) de produção ao nível da base de dados. Este modelo também está disponível para uma implementação de um clique a partir da Galeria de Modelos Azure Quickstart.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>Passos seguintes

Seguem-se alguns recursos adicionais:

* [Documentação do Azure Resource Manager](../azure-resource-manager/index.yml)
* [Esquema de fornecedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de arranque rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Resolução de problemas erros comuns de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/common-deployment-errors.md)