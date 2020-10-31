---
title: Criar um recipiente em Azure Cosmos DB Table API
description: Saiba como criar um recipiente em Azure Cosmos DB Table API utilizando o portal Azure, .NET, Java, Python, Node.js e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: fde3145e7bd7f4e53ae7a0c44f02e066c28ec785
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101583"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Criar um recipiente em Azure Cosmos DB Table API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Este artigo explica as diferentes formas de criar um recipiente na Azure Cosmos DB Table API. Mostra como criar um recipiente utilizando o portal Azure, Azure CLI, PowerShell ou SDKs suportados. Este artigo demonstra como criar um recipiente, especificar a chave de partição e a produção de provisão.

Este artigo explica as diferentes formas de criar um recipiente na Azure Cosmos DB Table API. Se estiver a utilizar uma API diferente, consulte [a API para MongoDB,](how-to-create-container-mongodb.md) [Cassandra API,](how-to-create-container-cassandra.md) [Gremlin API](how-to-create-container-gremlin.md)e [artigos da SQL API](how-to-create-container.md) para criar o recipiente.

> [!NOTE]
> Ao criar recipientes, certifique-se de que não cria dois recipientes com o mesmo nome, mas invólucros diferentes. Isto porque algumas partes da plataforma Azure não são sensíveis a casos, o que pode resultar em confusão/colisão de telemetria e ações em contentores com tais nomes.

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Criar com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-table-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **do Data Explorer** e selecione Nova **Tabela** . Em seguida, forneça os seguintes detalhes:

   * Introduza uma identificação de mesa.
   * Introduza uma produção a ser a provisionada (por exemplo, 1000 RUs).
   * Selecione **OK** .

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Screenshot da tabela API, adicionar caixa de diálogo de mesa":::

> [!Note]
> Para a API de Tabela, a chave de partição é especificada sempre que adicionar uma nova linha.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Criar usando O Azure CLI

[Criar uma tabela API de tabela com CLI Azure](./scripts/cli/table/create.md). Para uma lista de todas as amostras do Azure CLI em todas as APIs do Azure Cosmos ver, [amostras de Azure CLI para Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Criar com o PowerShell

[Criar uma tabela API de tabela com PowerShell](./scripts/powershell/table/create.md). Para uma lista de todas as amostras powerShell em todas as APIs do Azure Cosmos ver, [Amostras powerShell](powershell-samples.md)

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Trabalhar com uma conta do Azure Cosmos](./account-databases-containers-items.md)