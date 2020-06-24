---
title: Modelos de gestor de recursos para Azure Cosmos DB Table API
description: Utilize modelos de Gestor de Recursos Azure para criar e configurar a API da tabela API da Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: a12b3ad3aba42870f72cda5d4b574b5b88b8d5e4
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262298"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Gerir recursos API de tabela de DB da Azure Cosmos utilizando modelos de Gestor de Recursos Azure

Neste artigo, aprende a usar modelos do Azure Resource Manager para ajudar a implementar e gerir as suas contas, bases de dados e contentores Azure Cosmos.

Este artigo tem exemplos apenas para contas de API de tabela, para encontrar exemplos para outras contas tipo API ver: use modelos de Gestor de Recursos Azure com AZure Cosmos DB's API para [Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB,](manage-mongodb-with-resource-manager.md)artigos [SQL.](manage-sql-with-resource-manager.md)

> [!IMPORTANT]
>
> * Os nomes das contas estão limitados a 44 caracteres, todos minúsculos.
> * Para alterar os valores de produção, reimplante o modelo com RU/s atualizados.
> * Quando adiciona ou remove localizações a uma conta Azure Cosmos, não pode modificar simultaneamente outras propriedades. Estas operações devem ser efetuadas separadamente.

Para criar qualquer um dos recursos DB do Azure Cosmos abaixo, copie o modelo de exemplo a seguir num novo ficheiro json. Pode criar opcionalmente um ficheiro json de parâmetros para utilizar ao implementar várias instâncias do mesmo recurso com diferentes nomes e valores. Existem muitas formas de implementar modelos de Gestor de Recursos Azure, incluindo, [portal Azure](../azure-resource-manager/templates/deploy-portal.md) [CLI,](../azure-resource-manager/templates/deploy-cli.md) [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub.](../azure-resource-manager/templates/deploy-to-azure-button.md)

> [!TIP]
> Para ativar o rendimento partilhado ao utilizar a Tabela API, ative a produção de nível de conta no portal Azure.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Azure Cosmos conta para tabela com produção de escala automática

Este modelo criará uma conta Azure Cosmos para API de tabela com uma tabela com produção de escala automática. Este modelo também está disponível para uma implementação de um clique a partir da Galeria de Modelos Azure Quickstart.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Conta Azure Cosmos para tabela com produção padrão a provisionada

Este modelo criará uma conta Azure Cosmos para API de tabela com uma tabela com produção padrão. Este modelo também está disponível para uma implementação de um clique a partir da Galeria de Modelos Azure Quickstart.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>Passos seguintes

Seguem-se alguns recursos adicionais:

* [Documentação do Gestor de Recursos Azure](/azure/azure-resource-manager/)
* [Esquema de fornecedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de arranque rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Resolução de problemas erros comuns de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/common-deployment-errors.md)
