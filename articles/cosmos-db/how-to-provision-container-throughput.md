---
title: Aprovisionar débito do contentor no Azure Cosmos DB
description: Saiba como obter a produção ao nível do contentor no Azure Cosmos DB utilizando o portal Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 8c4259383196734c6e15c4ea261092938b1dd404
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282821"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container"></a>Produção padrão de provisão (manual) num recipiente Azure Cosmos

Este artigo explica como prever a produção padrão (manual) num recipiente (coleção, gráfico ou tabela) em Azure Cosmos DB. Pode provistirá a produção num único contentor, ou [provisão de produção numa base de dados](how-to-provision-database-throughput.md) e partilhá-la entre os contentores da base de dados. Você pode providenciar produção em um recipiente usando o portal Azure CLI, ou Azure Cosmos DB SDKs.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-sql-api-dotnet.md#create-account)ou selecione uma conta Azure Cosmos existente.

1. Abra o painel **do Data Explorer** e selecione New **Collection**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente.
   * Introduza um ID de recipiente (ou tabela ou gráfico).
   * Introduza um valor chave de partição (por exemplo, `/userid` ).
   * Introduza uma produção que pretende prever (por exemplo, 1000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png" alt-text="Screenshot do Data Explorer, com nova coleção em destaque":::

## <a name="azure-cli-or-powershell"></a>Azure CLI ou PowerShell

Para criar um recipiente com produção dedicada ver,

* [Criar um contentor com a CLI do Azure](manage-with-cli.md#create-a-container)
* [Criar um recipiente utilizando o PowerShell](manage-with-powershell.md#create-container)

> [!Note]
> Se estiver a abastecer a produção num recipiente numa conta Azure Cosmos configurada com o API API AZure Cosmos para o MongoDB, utilize `/myShardKey` para o caminho chave da partição. Se estiver a aagarr a produção num recipiente numa conta Azure Cosmos configurada com a API cassandra, use `/myPrimaryKey` para o caminho chave da partição.

## <a name="net-sdk"></a>SDK .NET

> [!Note]
> Utilize os SDKs Cosmos para a SQL API para provisão para todas as APIs cosmos, exceto Cassandra e MongoDB API.

### <a name="sql-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL, Gremlin e APIs de tabela

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

### <a name="mongodb-api"></a><a id="dotnet-mongodb"></a>API do MongoDB

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API de Cassandra

Comandos semelhantes podem ser emitidos através de qualquer controlador compatível com CQL.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Alterar ou alterar a produção para a tabela Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para saber sobre o fornecimento de produção em Azure Cosmos DB:

* [Como prever a produção padrão (manual) numa base de dados](how-to-provision-database-throughput.md)
* [Como providenciar a produção de escala automática numa base de dados](how-to-provision-autoscale-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
