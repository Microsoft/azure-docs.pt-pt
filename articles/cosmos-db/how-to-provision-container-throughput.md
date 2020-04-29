---
title: Aprovisionar débito do contentor no Azure Cosmos DB
description: Aprenda a fornecer a produção ao nível do contentor em Azure Cosmos DB utilizando o portal Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: e416501cb3c532b3ba0a262442b35b236875a463
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273297"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Provisão de entrada num recipiente Azure Cosmos

Este artigo explica como fornecer a entrada num recipiente (coleção, gráfico ou tabela) em Azure Cosmos DB. Pode fornecer a entrada num único recipiente, ou [fornecer entrada numa base](how-to-provision-database-throughput.md) de dados e partilhá-la entre os contentores dentro da base de dados. Pode fornecer entrada num recipiente utilizando o portal Azure, O CLI Azure ou o Azure Cosmos DB SDKs.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos,](create-sql-api-dotnet.md#create-account)ou selecione uma conta Azure Cosmos existente.

1. Abra o painel **do Data Explorer** e selecione New **Collection**. Em seguida, forneça os seguintes detalhes:

   * Indique se está a criar uma nova base de dados ou a utilizar uma existente.
   * Introduza um ID de recipiente (ou tabela ou gráfico).
   * Introduza um valor chave `/userid`de partição (por exemplo, ).
   * Introduza uma entrada que pretende fornecer (por exemplo, 1000 RUs).
   * Selecione **OK**.

    ![Screenshot do Data Explorer, com nova coleção em destaque](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI ou PowerShell

Para criar um recipiente com entrada dedicada ver,

* [Criar um contentor com a CLI do Azure](manage-with-cli.md#create-a-container)
* [Criar um recipiente usando powershell](manage-with-powershell.md#create-container)

> [!Note]
> Se estiver a fornecer a entrada num recipiente numa conta Azure Cosmos configurada com o Azure `/myShardKey` Cosmos DB API para MongoDB, utilize para o caminho-chave da partição. Se estiver a fornecer a entrada num recipiente numa conta Azure Cosmos `/myPrimaryKey` configurada com API Cassandra, utilize para o caminho-chave da partilha.

## <a name="net-sdk"></a>SDK .NET

> [!Note]
> Utilize os SDKs Cosmos para API SQL para fornecer a entrada para todos os APIs DB Cosmos, exceto Cassandra API.

### <a name="sql-mongodb-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL, MongoDB, Gremlin e APIs de Tabela
### <a name="net-v2-sdk"></a>.net V2 SDK

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

### <a name="net-v3-sdk"></a>.net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

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

* [Como fornecer a entrada numa base de dados](how-to-provision-database-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
