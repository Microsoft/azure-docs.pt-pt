---
title: Aprovisionar débito do contentor no Azure Cosmos DB
description: Aprenda a fornecer a produção ao nível do contentor em Azure Cosmos DB utilizando o portal Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: 0e7a2e9e5feb848971c4858415510f98a7bdaf78
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655343"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container"></a>Entrada padrão de fornecimento (manual) num contentor Azure Cosmos

Este artigo explica como fornecer uma entrada padrão (manual) num recipiente (recolha, gráfico ou tabela) em Azure Cosmos DB. Pode fornecer a entrada num único recipiente, ou [fornecer entrada numa base](how-to-provision-database-throughput.md) de dados e partilhá-la entre os contentores dentro da base de dados. Pode fornecer entrada num recipiente utilizando o portal Azure, O CLI Azure ou o Azure Cosmos DB SDKs.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-sql-api-dotnet.md#create-account)ou selecione uma conta Azure Cosmos existente.

1. Abra o painel **do Data Explorer** e selecione New **Collection**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente.
   * Introduza um ID de recipiente (ou tabela ou gráfico).
   * Introduza um valor chave de partição (por exemplo, `/userid` ).
   * Introduza uma entrada que pretende fornecer (por exemplo, 1000 RUs).
   * Selecione **OK**.

    ![Screenshot do Data Explorer, com nova coleção em destaque](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI ou PowerShell

Para criar um recipiente com entrada dedicada ver,

* [Criar um contentor com a CLI do Azure](manage-with-cli.md#create-a-container)
* [Criar um recipiente usando powerShell](manage-with-powershell.md#create-container)

> [!Note]
> Se estiver a fornecer a entrada num recipiente numa conta Azure Cosmos configurada com o Azure Cosmos DB API para MongoDB, utilize `/myShardKey` para o caminho-chave da partição. Se estiver a fornecer a entrada num recipiente numa conta Azure Cosmos configurada com API Cassandra, utilize `/myPrimaryKey` para o caminho-chave da partilha.

## <a name="net-sdk"></a>SDK .NET

> [!Note]
> Utilize os SDKs Cosmos para API SQL para fornecer a entrada para todos os APIs DB Cosmos, exceto Cassandra API.

### <a name="sql-mongodb-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL, MongoDB, Gremlin e APIs de Tabela

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

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>API de Cassandra

Comandos semelhantes podem ser emitidos através de qualquer controlador compatível com CQL.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Alterar ou alterar a entrada para a tabela Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para conhecer o fornecimento de entrada em Azure Cosmos DB:

* [Como fornecer a entrada padrão (manual) numa base de dados](how-to-provision-database-throughput.md)
* [Como fornecer a produção de escala automática numa base de dados](how-to-provision-autoscale-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
