---
title: Modelos de Gestor de Recursos para API Db Cassandra API do Azure Cosmos
description: Utilize modelos do Gestor de Recursos Azure para criar e configurar a API DB Cassandra do Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 630c965eadf17e21c75e1bd180fec4496df830e2
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684835"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Gerir os recursos da API da Azure Cosmos DB Cassandra utilizando modelos de Gestor de Recursos Azure

Neste artigo, você aprende a usar modelos de Gestor de Recursos Azure para ajudar a implementar e gerir as suas contas, espaços-chave e tabelas Do Azure Cosmos.

Este artigo tem exemplos apenas para as contas da API Cassandra, para encontrar exemplos para outras contas do tipo API ver: use modelos de Gestor de Recursos Azure com API do Azure Cosmos DB para [SQL,](manage-sql-with-resource-manager.md) [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB,](manage-mongodb-with-resource-manager.md) [Artigos](manage-table-with-resource-manager.md) de mesa.

> [!IMPORTANT]
>
> * Os nomes das contas estão limitados a 44 caracteres, todos minúsculos.
> * Para alterar os valores de entrada, recoloque o modelo com RU/s atualizado.
> * Quando adiciona ou remove localizações a uma conta Azure Cosmos, não pode simultaneamente modificar outras propriedades. Estas operações devem ser feitas separadamente.

Para criar qualquer um dos recursos do Azure Cosmos DB abaixo, copie o seguinte modelo de exemplo em um novo ficheiro Json. Pode criar opcionalmente um ficheiro json de parâmetros para utilizar ao implementar várias instâncias do mesmo recurso com diferentes nomes e valores. Existem muitas formas de implementar modelos de Gestor de Recursos Azure, incluindo, [portal Azure,](../azure-resource-manager/templates/deploy-portal.md) [Azure CLI,](../azure-resource-manager/templates/deploy-cli.md) [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub.](../azure-resource-manager/templates/deploy-to-azure-button.md)

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>Azure Cosmos conta cassandra com produção de escala automática aprovisionada

Este modelo cria uma conta Azure Cosmos em duas regiões com opções de consistência e failover, com um espaço de chave e tabela configurada para a produção de escala automática. Este modelo também está disponível para uma utilização de um clique na Galeria de Modelos De Quickstart Azure.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-provisioned-throughput"></a>Azure Cosmos conta com Cassandra com entrada padrão provisionada

Este modelo cria uma conta Azure Cosmos em duas regiões com opções de consistência e failover, com um espaço de chave e tabela configurada para a entrada padrão. Este modelo também está disponível para uma utilização de um clique na Galeria de Modelos De Quickstart Azure.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>Passos seguintes

Seguem-se alguns recursos adicionais:

* [Documentação do Gestor de Recursos Azure](/azure/azure-resource-manager/)
* [Esquema de fornecedor de recursos Da Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de Quickstart Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Problemas de resolução de erros comuns de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/common-deployment-errors.md)
