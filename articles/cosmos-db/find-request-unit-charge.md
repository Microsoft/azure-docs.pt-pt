---
title: Encontrar a unidade de pedido custos (RU) no Azure Cosmos DB
description: Saiba como localizar o custo da unidade de pedido para qualquer operação executada em relação a um contentor do Cosmos do Azure
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: thweiss
ms.openlocfilehash: 833f815f0c84584f084e4d4637c0318f7c2daec0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683839"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>Encontrar a unidade de pedido custos (RU) no Azure Cosmos DB

Este artigo apresenta as diferentes formas de localizar os [unidades de pedido](request-units.md) consumo para qualquer operação executada em relação a um contentor do Cosmos do Azure. É atualmente possível medir este consumo com o portal do Azure ou ao inspecionar a resposta enviada de volta do Azure Cosmos DB através de um dos SDKs.

## <a name="sql-core-api"></a>SQL API (principal)

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Atualmente o portal do Azure permite-lhe encontrar os encargos de pedidos para apenas uma consulta SQL.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) e inseri-la com os dados ou selecione uma conta existente do Cosmos do Azure que já contenha dados.

1. Abra o **Data Explorer** painel e selecione o contentor que deseja trabalhar.

1. Clique em **nova consulta SQL**.

1. Introduza uma consulta válida, em seguida, clique em **executar consulta**.

1. Clique em **estatísticas de consulta** para apresentar a cobrança do pedido real para o pedido que acabou de executar.

![Encargos de pedidos de consulta de captura de ecrã do SQL no portal do Azure](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Utilizar o V2 do SDK de .NET

Objetos devolvidos do [v2 do SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (consulte [neste início rápido](create-sql-api-dotnet.md) em relação à sua utilização) expõem um `RequestCharge` propriedade.

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="use-the-java-sdk"></a>Utilizar o Java SDK

Objetos devolvidos do [SDK de Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (consulte [neste início rápido](create-sql-api-java.md) em relação à sua utilização) expõem um `getRequestCharge()` método.

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

### <a name="use-the-nodejs-sdk"></a>Utilizar o SDK de node. js

Objetos devolvidos do [SDK node. js](https://www.npmjs.com/package/@azure/cosmos) (consulte [neste início rápido](create-sql-api-nodejs.md) em relação à sua utilização) expõem um `headers` subobjeto que mapeia todos os cabeçalhos devolvidos pela API de HTTP subjacente. A cobrança do pedido está disponível na `x-ms-request-charge` chave.

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

### <a name="use-the-python-sdk"></a>Utilizar o SDK Python

O `CosmosClient` de objeto do [Python SDK](https://pypi.org/project/azure-cosmos/) (consulte [neste início rápido](create-sql-api-python.md) em relação à sua utilização) expõe um `last_response_headers` dicionário que mapeia todos os cabeçalhos devolvidos pela API HTTP subjacente para o última operação executada. A cobrança do pedido está disponível na `x-ms-request-charge` chave.

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

Cobranças de unidades de pedido é exposta por um personalizado [comandos de base de dados](https://docs.mongodb.com/manual/reference/command/) com o nome `getLastRequestStatistics.` este comando devolve um documento que contém o nome da última operação executada, a seu encargo de pedido e a sua duração.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Atualmente o portal do Azure permite-lhe encontrar os encargos de pedidos para apenas uma consulta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) e inseri-la com os dados ou selecione uma conta existente que já contenha dados.

1. Abra o **Data Explorer** painel e selecione a coleção que deseja trabalhar.

1. Clique em **nova consulta**.

1. Introduza uma consulta válida, em seguida, clique em **executar consulta**.

1. Clique em **estatísticas de consulta** para apresentar a cobrança do pedido real para o pedido que acabou de executar.

![Captura de ecrã do MongoDB encargos de pedidos de consulta no portal do Azure](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Utilizar o controlador .NET do MongoDB

Ao utilizar o [oficial controlador do .NET do MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/) (consulte [neste início rápido](create-mongodb-dotnet.md) em relação à sua utilização), comandos podem ser executados ao chamar o `RunCommand` método num `IMongoDatabase` objeto. Este método requer uma implementação do `Command<>` classe abstrata.

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

### <a name="use-the-mongodb-java-driver"></a>Utilizar o controlador Java do MongoDB

Ao utilizar o [controlador oficial de Java do MongoDB](http://mongodb.github.io/mongo-java-driver/) (consulte [neste início rápido](create-mongodb-java.md) em relação à sua utilização), comandos podem ser executados ao chamar o `runCommand` método num `MongoDatabase` objeto.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>Utilizar o controlador de node. js do MongoDB

Ao utilizar o [oficial controlador de node. js do MongoDB](https://mongodb.github.io/node-mongodb-native/) (consulte [neste início rápido](create-mongodb-nodejs.md) em relação à sua utilização), comandos podem ser executados ao chamar o `command` método num `db` objeto.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>API de Cassandra

Quando realizar operações de API para Cassandra do Azure Cosmos DB, as cobranças de unidades de pedido é retornada no payload de entrada como um campo chamado `RequestCharge`.

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

Ao utilizar o [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) (consulte [neste início rápido](create-cassandra-dotnet.md) em relação à sua utilização), o payload de entrada pode ser obtido no `Info` propriedade de um `RowSet` objeto.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>Utilizar o Java SDK

Ao utilizar o [SDK de Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) (consulte [neste início rápido](create-cassandra-java.md) em relação à sua utilização), o payload de entrada pode ser obtido chamando o `getExecutionInfo()` método num `ResultSet` objeto.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>API do Gremlin

### <a name="use-drivers-and-sdk"></a>Drivers de utilização e SDK

Cabeçalhos devolvidos pela API do Gremlin são mapeados para atributos de estado personalizado, que atualmente são apresentados do Gremlin .NET e Java. A cobrança do pedido está disponível na `x-ms-request-charge` chave.

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

Ao utilizar o [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/) (consulte [neste início rápido](create-graph-dotnet.md) em relação à sua utilização), atributos de estado estão disponíveis no `StatusAttributes` propriedade do `ResultSet<>` objeto.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>Utilizar o Java SDK

Ao utilizar o [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) (consulte [neste início rápido](create-graph-java.md) em relação à sua utilização), atributos de estado podem ser obtidos chamando o `statusAttributes()` método no `ResultSet` objeto.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>API de Tabela

O SDK de apenas retornar atualmente cobranças de unidades de pedido para operações de tabela é o [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) (consulte [neste início rápido](create-table-dotnet.md) em relação à sua utilização). O `TableResult` objeto expõe um `RequestCharge` propriedade que fica preenchida pelo SDK quando utilizado em relação a API de tabela do Azure Cosmos DB.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos para saber mais sobre como otimizar seu consumo de unidades de pedido:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
* [Otimizar o débito aprovisionado de custo no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](optimize-cost-queries.md)
* [Dimensione globalmente o débito aprovisionado](scaling-throughput.md)
* [Débito de aprovisionar em contentores e bases de dados](set-throughput.md)
* [Como aprovisionar o débito para um contentor](how-to-provision-container-throughput.md)