---
title: Criar um recipiente em Azure Cosmos DB SQL API
description: Aprenda a criar um recipiente em Azure Cosmos DB SQL API utilizando o portal Azure, .NET, Java, Python, Node.js e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: eb9e8caff81e86b7db732c970eee1e1a08be5241
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491109"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Criar um recipiente em Azure Cosmos DB SQL API

Este artigo explica as diferentes formas de criar um recipiente na Azure Cosmos DB SQL API. Mostra como criar um recipiente utilizando o portal Azure CLI, PowerShell ou SDKs suportados. Este artigo demonstra como criar um recipiente, especificar a chave de partição e a produção de provisão.

Este artigo explica as diferentes formas de criar um recipiente na Azure Cosmos DB SQL API. Se estiver a utilizar uma API diferente, consulte [a API para MongoDB,](how-to-create-container-mongodb.md) [Cassandra API,](how-to-create-container-cassandra.md) [Gremlin API](how-to-create-container-gremlin.md)e [artigos da Table API](how-to-create-container-table.md) para criar o recipiente.

> [!NOTE]
> Ao criar recipientes, certifique-se de que não cria dois recipientes com o mesmo nome, mas invólucros diferentes. Isto porque algumas partes da plataforma Azure não são sensíveis a casos, o que pode resultar em confusão/colisão de telemetria e ações em contentores com tais nomes.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Criar um contentor com o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-sql-api-dotnet.md#create-account)ou selecione uma conta existente.

1. Abra o painel **do Explorador de Dados** e selecione **Novo Recipiente**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente.
   * Introduza a identificação do contentor.
   * Introduza uma chave de partição.
   * Introduza uma produção a ser a provisionada (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Screenshot do painel do Data Explorer, com novo recipiente em destaque":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Criar um contentor com a CLI do Azure

[Criar um recipiente com Azure CLI](manage-with-cli.md#create-a-container). Para uma lista de todas as amostras do Azure CLI em todas as APIs do Azure Cosmos ver, [amostras de Azure CLI para Azure Cosmos DB](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>Criar um recipiente utilizando o PowerShell

[Criar um recipiente com PowerShell](manage-with-powershell.md#create-container). Para uma lista de todas as amostras powerShell em todas as APIs do Azure Cosmos ver, [Amostras powerShell](powershell-samples.md)

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Criar um contentor com o SDK .NET

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

## <a name="next-steps"></a>Passos seguintes

* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Trabalhar com uma conta do Azure Cosmos](./account-databases-containers-items.md)