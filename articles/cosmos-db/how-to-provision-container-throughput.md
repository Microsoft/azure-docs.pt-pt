---
title: Produção de contentores de provisão em Azure Cosmos DB SQL API
description: Saiba como obter a produção ao nível do contentor no Azure Cosmos DB SQL API utilizando o portal Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4caf43cb972b44dd1482b9e6e467e41cae294708
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93100104"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Produção padrão de provisão (manual) num recipiente Azure Cosmos - SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo explica como providenciar a produção padrão (manual) num recipiente em Azure Cosmos DB SQL API. Pode provistirá a produção num único contentor, ou [provisão de produção numa base de dados](how-to-provision-database-throughput.md) e partilhá-la entre os contentores da base de dados. Você pode providenciar produção em um recipiente usando o portal Azure CLI, ou Azure Cosmos DB SDKs.

Se estiver a utilizar uma API diferente, consulte [a API para a MongoDB,](how-to-provision-throughput-mongodb.md) [Cassandra API,](how-to-provision-throughput-cassandra.md) [artigos da API gremlin](how-to-provision-throughput-gremlin.md) para prever o rendimento.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-sql-api-dotnet.md#create-account)ou selecione uma conta Azure Cosmos existente.

1. Abra o painel **do Explorador de Dados** e selecione **Novo Recipiente**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente.
   * Introduza uma identificação do contentor.
   * Introduza um valor chave de partição (por exemplo, `/ItemID` ).
   * Introduza uma produção que pretende prever (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Screenshot do Data Explorer, com nova coleção em destaque":::

## <a name="azure-cli-or-powershell"></a>Azure CLI ou PowerShell

Para criar um recipiente com produção dedicada ver,

* [Criar um contentor com a CLI do Azure](manage-with-cli.md#create-a-container)
* [Criar um recipiente utilizando o PowerShell](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>SDK .NET

> [!Note]
> Utilize os SDKs Cosmos para a SQL API para provisão para todas as APIs cosmos, exceto Cassandra e MongoDB API.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>SDK JavaScript

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para saber sobre o fornecimento de produção em Azure Cosmos DB:

* [Como prever a produção padrão (manual) numa base de dados](how-to-provision-database-throughput.md)
* [Como providenciar a produção de escala automática numa base de dados](how-to-provision-autoscale-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
