---
title: Criar e gerir a Azure Cosmos DB com modelos de Gestor de Recursos
description: Utilize modelos de Gestor de Recursos Azure para criar e configurar Azure Cosmos DB para Core (SQL) API
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 3684feff10ee3a09cc07276895496524fd64deee
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262315"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Gerir recursos API DB Core (SQL) da Azure Cosmos com modelos de Gestor de Recursos Azure

Neste artigo, aprende a usar modelos do Azure Resource Manager para ajudar a implementar e gerir as suas contas, bases de dados e contentores Azure Cosmos.

Este artigo apenas mostra exemplos de modelo do Azure Resource Manager para contas API Core (SQL). Também pode encontrar exemplos de modelos para [Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md)e [APIs de tabela.](manage-table-with-resource-manager.md)

> [!IMPORTANT]
>
> * Os nomes das contas estão limitados a 44 caracteres, todos minúsculos.
> * Para alterar os valores de produção, reimplante o modelo com RU/s atualizados.
> * Quando adiciona ou remove localizações a uma conta Azure Cosmos, não pode modificar simultaneamente outras propriedades. Estas operações devem ser efetuadas separadamente.

Para criar qualquer um dos recursos DB do Azure Cosmos abaixo, copie o modelo de exemplo a seguir num novo ficheiro json. Pode criar opcionalmente um ficheiro json de parâmetros para utilizar ao implementar várias instâncias do mesmo recurso com diferentes nomes e valores. Existem muitas formas de implementar modelos de Gestor de Recursos Azure, incluindo, [portal Azure](../azure-resource-manager/templates/deploy-portal.md) [CLI,](../azure-resource-manager/templates/deploy-cli.md) [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub.](../azure-resource-manager/templates/deploy-to-azure-button.md)

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Conta Azure Cosmos com produção de escala automática

Este modelo cria uma conta Azure Cosmos em duas regiões com opções de consistência e failover, com base de dados e contentor configurado para produção de escala automática que tem a maioria das opções de política ativadas. Este modelo também está disponível para uma implementação de um clique a partir da Galeria de Modelos Azure Quickstart.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Conta Azure Cosmos com loja analítica

Este modelo cria uma conta Azure Cosmos numa região com um recipiente com TTL Analytical ativado e opções para produção manual ou de escala automática. Este modelo também está disponível para uma implementação de um clique a partir da Galeria de Modelos Azure Quickstart.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-analytical-store%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-analytical-store/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Conta Azure Cosmos com produção padrão a provisionada

Este modelo cria uma conta Azure Cosmos em duas regiões com opções de consistência e failover, com base de dados e contentor configurado para produção padrão que tem a maioria das opções de política ativadas. Este modelo também está disponível para uma implementação de um clique a partir da Galeria de Modelos Azure Quickstart.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Recipiente DB Azure Cosmos com funcionalidade do lado do servidor

Este modelo cria uma conta, base de dados e recipiente Azure Cosmos com um procedimento armazenado, gatilho e função definida pelo utilizador. Este modelo também está disponível para uma implementação de um clique a partir da Galeria de Modelos Azure Quickstart.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Conta DB de nível livre Azure Cosmos

Este modelo cria uma conta Azure Cosmos de nível livre e uma base de dados com produção partilhada que pode ser partilhada com até 25 contentores. Este modelo também está disponível para uma implementação de um clique a partir da Galeria de Modelos Azure Quickstart.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Passos seguintes

Seguem-se alguns recursos adicionais:

* [Documentação do Gestor de Recursos Azure](/azure/azure-resource-manager/)
* [Esquema de fornecedor de recursos Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de arranque rápido Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Resolução de problemas erros comuns de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/common-deployment-errors.md)
