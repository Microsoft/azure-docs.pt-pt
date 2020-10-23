---
title: Criar um recipiente em Azure Cosmos DB Gremlin API
description: Saiba como criar um recipiente em Azure Cosmos DB Gremlin API utilizando o portal Azure, .NET e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 75591fbf9423ec715247af613cd047bf9440525b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284645"
---
# <a name="create-a-container-in-azure-cosmos-db-gremlin-api"></a>Criar um recipiente em Azure Cosmos DB Gremlin API

Este artigo explica as diferentes formas de criar um recipiente na Azure Cosmos DB Gremlin API. Mostra como criar um recipiente utilizando o portal Azure, Azure CLI, PowerShell ou SDKs suportados. Este artigo demonstra como criar um recipiente, especificar a chave de partição e a produção de provisão.

Este artigo explica as diferentes formas de criar um recipiente na Azure Cosmos DB Gremlin API. Se estiver a utilizar uma API diferente, consulte [a API para MongoDB,](how-to-create-container-mongodb.md) [Cassandra API,](how-to-create-container-cassandra.md) [Table API](how-to-create-container-table.md)e [artigos da API SQL](how-to-create-container.md) para criar o recipiente.

> [!NOTE]
> Ao criar recipientes, certifique-se de que não cria dois recipientes com o mesmo nome, mas invólucros diferentes. Isto porque algumas partes da plataforma Azure não são sensíveis a casos, o que pode resultar em confusão/colisão de telemetria e ações em contentores com tais nomes.

## <a name="create-using-azure-portal"></a><a id="portal-gremlin"></a>Criar com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-graph-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **do Data Explorer** e selecione Novo **Gráfico**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente.
   * Introduza um ID de gráfico.
   * Selecione a capacidade de armazenamento **Ilimitada**.
   * Introduza uma chave de partição para vértices.
   * Introduza uma produção a ser a provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Screenshot de Gremlin API, Adicionar caixa de diálogo de gráfico":::

## <a name="create-using-net-sdk"></a><a id="dotnet-sql-graph"></a>Criar usando .NET SDK

Se encontrar exceção no tempo limite ao criar uma coleção, faça uma operação de leitura para validar se a coleção foi criada com sucesso. A operação de leitura abre uma exceção até que a operação de criação de recolha seja bem sucedida. Para a lista de códigos de estado suportados pela operação de criação consulte os Códigos de Estado HTTP para o artigo [DB do Azure Cosmos.](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Criar usando O Azure CLI

[Crie um gráfico Gremlin com Azure CLI](./scripts/cli/gremlin/create.md). Para uma lista de todas as amostras do Azure CLI em todas as APIs do Azure Cosmos ver, [amostras de Azure CLI para Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Criar com o PowerShell

[Crie um gráfico Gremlin com PowerShell](./scripts/powershell/gremlin/create.md). Para uma lista de todas as amostras powerShell em todas as APIs do Azure Cosmos ver, [Amostras powerShell](powershell-samples.md)

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Trabalhar com uma conta do Azure Cosmos](account-overview.md)

