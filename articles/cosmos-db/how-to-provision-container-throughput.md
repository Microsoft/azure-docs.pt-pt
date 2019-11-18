---
title: Aprovisionar débito do contentor no Azure Cosmos DB
description: Aprenda a aprovisionar débito ao nível do contentor no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 0b48652f7b181f1254a4b20af75b83593c2aba05
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147608"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Provisionar a taxa de transferência em um contêiner Cosmos do Azure

Este artigo explica como provisionar a taxa de transferência em um contêiner (coleção, gráfico ou tabela) no Azure Cosmos DB. Você pode provisionar a produtividade em um único contêiner ou [provisionar a taxa de transferência em um banco de dados](how-to-provision-database-throughput.md) e compartilhá-lo entre os contêineres no banco de dados. Você pode provisionar a taxa de transferência em um contêiner usando os SDKs portal do Azure, CLI do Azure ou Azure Cosmos DB.

## <a name="azure-portal"></a>Portal do Azure

1. Iniciar sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account)ou selecione uma conta existente do Azure Cosmos.

1. Abra o painel **Data Explorer** e selecione **nova coleção**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um novo banco de dados ou usando um existente.
   * Insira uma ID de contêiner (ou tabela ou gráfico).
   * Insira um valor de chave de partição (por exemplo, `/userid`).
   * Insira uma taxa de transferência que você deseja provisionar (por exemplo, 1000 RUs).
   * Selecione **OK**.

    ![Captura de tela de Data Explorer, com a nova coleção realçada](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>CLI do Azure ou PowerShell

Para criar um contêiner com taxa de transferência dedicada, consulte

* [Criar um contêiner usando CLI do Azure](manage-with-cli.md#create-a-container)
* [Criar um contêiner usando o PowerShell](manage-with-powershell.md#create-container)

> [!Note]
> Se você estiver Provisionando a taxa de transferência em um contêiner em uma conta do Azure Cosmos configurada com a API do Azure Cosmos DB para MongoDB, use `/myShardKey` para o caminho da chave de partição. Se você estiver Provisionando a taxa de transferência em um contêiner em uma conta do Azure Cosmos configurada com API do Cassandra, use `/myPrimaryKey` para o caminho da chave de partição.

## <a name="net-sdk"></a>SDK .NET

> [!Note]
> Use os SDKs do cosmos para a API do SQL para provisionar a taxa de transferência para todas as APIs de Cosmos DB, exceto API do Cassandra.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin e APIs de Tabela
### <a name="net-v2-sdk"></a>SDK do .net v2

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

### <a name="net-v3-sdk"></a>SDK do .net v3
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

## <a name="javascript-sdk"></a>SDK JavaScript

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "contaierId ",
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

### <a id="dotnet-cassandra"></a>API para Cassandra

Comandos semelhantes podem ser emitidos por meio de qualquer driver compatível com CQL.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Alterar ou alterar a taxa de transferência para a tabela Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Passos seguintes

Consulte os artigos a seguir para saber mais sobre o provisionamento de taxa de transferência no Azure Cosmos DB:

* [Como provisionar a taxa de transferência em um banco de dados](how-to-provision-database-throughput.md)
* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
