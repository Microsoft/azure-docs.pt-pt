---
title: Criar e gerir o Azure Cosmos DB com modelos de Gestor de Recursos
description: Utilize modelos de Gestor de Recursos Azure para criar e configurar O Azure Cosmos DB para Core (SQL) API
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: b24998cbfdc037a6ded58fd17801c340c5891073
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684790"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Gerir os recursos da API Azure Cosmos DB Core (SQL) com modelos de Gestor de Recursos Azure

Neste artigo, aprende-se a usar modelos do Gestor de Recursos Azure para ajudar a implementar e gerir as suas contas, bases de dados e contentores Do Azure Cosmos DB.

Este artigo apenas mostra exemplos de modelo seletiva seletiva sql (SQL) Do Gestor de Recursos. Você também pode encontrar exemplos de modelo para [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin,](manage-gremlin-with-resource-manager.md) [MongoDB](manage-mongodb-with-resource-manager.md)e APIs [de mesa.](manage-table-with-resource-manager.md)

> [!IMPORTANT]
>
> * Os nomes das contas estão limitados a 44 caracteres, todos minúsculos.
> * Para alterar os valores de entrada, recoloque o modelo com RU/s atualizado.
> * Quando adiciona ou remove localizações a uma conta Azure Cosmos, não pode simultaneamente modificar outras propriedades. Estas operações devem ser feitas separadamente.

Para criar qualquer um dos recursos do Azure Cosmos DB abaixo, copie o seguinte modelo de exemplo em um novo ficheiro Json. Pode criar opcionalmente um ficheiro json de parâmetros para utilizar ao implementar várias instâncias do mesmo recurso com diferentes nomes e valores. Existem muitas formas de implementar modelos de Gestor de Recursos Azure, incluindo, [portal Azure,](../azure-resource-manager/templates/deploy-portal.md) [Azure CLI,](../azure-resource-manager/templates/deploy-cli.md) [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [GitHub.](../azure-resource-manager/templates/deploy-to-azure-button.md)

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Conta Azure Cosmos com entrada em escala automática

Este modelo cria uma conta Azure Cosmos em duas regiões com opções de consistência e falha, com base de dados e contentor configurado para a produção de escala automática que tem a maioria das opções políticas ativadas. Este modelo também está disponível para uma utilização de um clique na Galeria de Modelos De Quickstart Azure.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Conta Azure Cosmos com loja analítica

Este modelo cria uma conta Azure Cosmos numa região com um recipiente com TTL analítico habilitado e opções para a produção manual ou automática. Este modelo também está disponível para uma utilização de um clique na Galeria de Modelos De Quickstart Azure.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-analytical-store%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-analytical-store/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Conta Azure Cosmos com entrada normalizada

Este modelo cria uma conta Azure Cosmos em duas regiões com opções de consistência e falha, com base de dados e contentorconfigurado para a entrada padrão que tem a maioria das opções políticas ativadas. Este modelo também está disponível para uma utilização de um clique na Galeria de Modelos De Quickstart Azure.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Recipiente Db Azure Cosmos com funcionalidade do lado do servidor

Este modelo cria uma conta Azure Cosmos, base de dados e recipiente com um procedimento armazenado, gatilho e função definida pelo utilizador. Este modelo também está disponível para uma utilização de um clique na Galeria de Modelos De Quickstart Azure.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Conta Free Tier Azure Cosmos DB

Este modelo cria uma conta Azure Cosmos de nível livre e uma base de dados com entrada partilhada que pode ser partilhada com até 25 contentores. Este modelo também está disponível para uma utilização de um clique na Galeria de Modelos De Quickstart Azure.

[![Implementar no Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Passos seguintes

Seguem-se alguns recursos adicionais:

* [Documentação do Gestor de Recursos Azure](/azure/azure-resource-manager/)
* [Esquema de fornecedor de recursos Da Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Modelos de Quickstart Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Problemas de resolução de erros comuns de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/common-deployment-errors.md)
