---
title: Modelos de Gestor de Recursos para API Azure Cosmos DB Gremlin
description: Utilize modelos do Gestor de Recursos Azure para criar e configurar a API Do Cosmos Azure.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: defeec2317f47e9d79245cbde0ef81c8a3543bfb
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791243"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Gerir os recursos da API Azure Cosmos DB Gremlin utilizando modelos de Gestor de Recursos Azure

Neste artigo, aprende-se a usar modelos do Gestor de Recursos Azure para ajudar a implementar e gerir as suas contas, bases de dados e gráficos do Azure Cosmos DB.

Este artigo tem exemplos apenas para contas Da API Gremlin, para encontrar exemplos para outras contas do tipo API ver: use modelos de Gestor de Recursos Azure com API do Azure Cosmos DB para [Cassandra,](manage-cassandra-with-resource-manager.md) [SQL,](manage-sql-with-resource-manager.md) [MongoDB,](manage-mongodb-with-resource-manager.md) [Artigos](manage-table-with-resource-manager.md) de mesa.

> [!IMPORTANT]
>
> * Os nomes das contas estão limitados a 44 caracteres, todos minúsculos.
> * Para alterar os valores de entrada, recoloque o modelo com RU/s atualizado.
> * Quando adiciona ou remove localizações a uma conta Azure Cosmos, não pode simultaneamente modificar outras propriedades. Estas operações devem ser feitas separadamente.

Para criar qualquer um dos recursos do Azure Cosmos DB abaixo, copie o seguinte modelo de exemplo em um novo ficheiro Json. Pode criar opcionalmente um ficheiro json de parâmetros para utilizar ao implementar várias instâncias do mesmo recurso com diferentes nomes e valores. Existem muitas formas de implementar modelos de Gestor de Recursos Azure, incluindo, [portal Azure,](../azure-resource-manager/templates/deploy-portal.md) [Azure CLI,](../azure-resource-manager/templates/deploy-cli.md) [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub.](../azure-resource-manager/templates/deploy-to-azure-button.md)

<a id="create-autoscale"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-autoscale-provisioned-throughput"></a>Azure Cosmos DB conta para Gremlin com produção de escala automática aprovisionada

Este modelo criará uma conta Azure Cosmos para a API Gremlin com uma base de dados e gráfico com entrada em escala automática.

Este modelo também está disponível para uma utilização de um clique na Galeria de Modelos De Quickstart Azure.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-standard-provisioned-throughput"></a>Azure Cosmos DB conta para Gremlin com entrada padrão provisionada

Este modelo criará uma conta Azure Cosmos para a API Gremlin com uma base de dados e gráfico com entrada padrão (manual).

Este modelo também está disponível para uma utilização de um clique na Galeria de Modelos De Quickstart Azure.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json":::

## <a name="next-steps"></a>Passos seguintes

Seguem-se alguns recursos adicionais:

* [Documentação do Gestor de Recursos Azure](/azure/azure-resource-manager/)
* [Esquema de fornecedor de recursos Da Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de Quickstart Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Problemas de resolução de erros comuns de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/common-deployment-errors.md)