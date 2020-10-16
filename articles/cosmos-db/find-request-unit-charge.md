---
title: Encontre a taxa da unidade de pedido (RU) na Azure Cosmos DB
description: Saiba como encontrar a taxa da unidade de pedido (RU) para qualquer operação executada contra um contentor Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2019
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 81660bee32cace458424c9975c74ca7980148e29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316193"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Encontre a taxa da unidade de pedido na Azure Cosmos DB

Este artigo apresenta as diferentes formas de encontrar o consumo da [unidade de pedido](request-units.md) (RU) para qualquer operação executada contra um contentor em Azure Cosmos DB. Atualmente, só pode medir este consumo utilizando o portal Azure ou inspecionando a resposta enviada de Azure Cosmos DB através de um dos SDKs.

## <a name="sql-core-api"></a>API de SQL (Core)

Se estiver a utilizar a API SQL, tem múltiplas opções para encontrar o consumo de RU para uma operação contra um recipiente Azure Cosmos.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Atualmente, pode encontrar a taxa de pedido no portal Azure apenas para uma consulta SQL.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos](create-sql-api-dotnet.md#create-account) e alimente-a com dados, ou selecione uma conta Azure Cosmos existente que já contenha dados.

1. Vá ao painel **do Data Explorer** e, em seguida, selecione o recipiente em que pretende trabalhar.

1. Selecione **Nova Consulta SQL**.

1. Introduza uma consulta válida e, em seguida, selecione **Executar Consulta**.

1. Selecione **Estatísticas de Consulta** para mostrar o custo de pedido real para o pedido que executou.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Screenshot de uma taxa de pedido de consulta SQL no portal Azure":::

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Os objetos que são devolvidos do [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) expõem uma `RequestCharge` propriedade:

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Os objetos que são devolvidos do [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) expõem uma `RequestCharge` propriedade:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Para obter mais informações, consulte [Quickstart: Construa uma aplicação web .NET utilizando uma conta API SQL em Azure Cosmos DB](create-sql-api-dotnet.md).

---

### <a name="use-the-java-sdk"></a>Use o Java SDK

Os objetos que são devolvidos do [SDK Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) expõem um `getRequestCharge()` método:

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

Para obter mais informações, consulte [Quickstart: Construa uma aplicação Java utilizando uma conta Azure Cosmos DB SQL API](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Use o Node.js SDK

Os objetos que são devolvidos do [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) expõem um `headers` subobjecto que mapeia todos os cabeçalhos devolvidos pela API HTTP subjacente. A taxa de pedido está disponível na `x-ms-request-charge` chave:

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

Para obter mais informações, consulte [Quickstart: Construa uma aplicação Node.js utilizando uma conta Azure Cosmos DB SQL API](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Utilizar o SDK Python

O `CosmosClient` objeto do Python [SDK](https://pypi.org/project/azure-cosmos/) expõe um `last_response_headers` dicionário que mapeia todos os cabeçalhos devolvidos pela API HTTP subjacente para a última operação executada. A taxa de pedido está disponível na `x-ms-request-charge` chave:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação Python utilizando uma conta AZure Cosmos DB SQL API](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

A carga RU é exposta por um [comando de base de dados](https://docs.mongodb.com/manual/reference/command/) personalizado chamado `getLastRequestStatistics` . O comando devolve um documento que contém o nome da última operação executada, a sua carga de pedido e a sua duração. Se utilizar o AZure Cosmos DB API para o MongoDB, tem várias opções para recuperar a carga RU.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Atualmente, pode encontrar a taxa de pedido no portal Azure apenas para uma consulta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) e alimente-a com dados ou selecione uma conta existente que já contenha dados.

1. Vá ao painel **do Data Explorer** e, em seguida, selecione o recipiente em que pretende trabalhar.

1. Selecione **Nova Consulta**.

1. Introduza uma consulta válida e, em seguida, selecione **Executar Consulta**.

1. Selecione **Estatísticas de Consulta** para mostrar o custo de pedido real para o pedido que executou.

:::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Screenshot de uma taxa de pedido de consulta SQL no portal Azure":::

### <a name="use-the-mongodb-net-driver"></a>Utilize o controlador MongoDB .NET

Quando utilizar o [controlador oficial MongoDB .NET,](https://docs.mongodb.com/ecosystem/drivers/csharp/)pode executar comandos ligando para o `RunCommand` método num `IMongoDatabase` objeto. Este método requer uma implementação da `Command<>` classe abstrata:

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

Para obter mais informações, consulte [Quickstart: Construa uma aplicação web .NET utilizando um API AZure Cosmos DB para a MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Use o motorista de Java MongoDB


Quando utilizar o [controlador oficial MongoDB Java,](https://mongodb.github.io/mongo-java-driver/)pode executar comandos chamando o `runCommand` método num `MongoDatabase` objeto:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação web utilizando o Azure Cosmos DB API para a MongoDB e o Java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Use o motorista de Node.js MongoDB

Quando utilizar o [controlador oficial de Node.js MongoDB,](https://mongodb.github.io/node-mongodb-native/)pode executar comandos chamando o método num `command` `db` objeto:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Para mais informações, consulte [Quickstart: Migrar uma aplicação web Node.js MongoDB existente para Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>API de Cassandra

Quando executa operações contra a Azure Cosmos DB Cassandra API, a carga RU é devolvida na carga útil recebida como um campo chamado `RequestCharge` . Tem várias opções para obter os custos das RUs.

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

Quando utilizar o [.NET SDK,](https://www.nuget.org/packages/CassandraCSharpDriver/)pode recuperar a carga útil recebida sob a `Info` propriedade de um `RowSet` objeto:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação Cassandra utilizando o .NET SDK e a Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Use o Java SDK

Quando utilizar o [SDK Java,](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)pode recuperar a carga útil de entrada, chamando o `getExecutionInfo()` método num `ResultSet` objeto:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação Cassandra utilizando o Java SDK e o Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>API do Gremlin

Quando utiliza a API Gremlin, tem várias opções para encontrar o consumo de RU para uma operação contra um contentor Azure Cosmos. 

### <a name="use-drivers-and-sdk"></a>Use motoristas e SDK

Os cabeçalhos devolvidos pela API gremlin são mapeados para atributos de estado personalizados, que atualmente são surgidos pela Gremlin .NET e Java SDK. A taxa de pedido está disponível na `x-ms-request-charge` chave.

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

Quando utilizar o [Gremlin.NET SDK,](https://www.nuget.org/packages/Gremlin.Net/)os atributos de estado estão disponíveis sob a `StatusAttributes` propriedade do `ResultSet<>` objeto:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação .NET Framework ou Core utilizando uma conta API API AZure Cosmos DB Gremlin](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Use o Java SDK

Quando utilizar o [Gremlin Java SDK,](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)pode obter atributos de estado, chamando o `statusAttributes()` método do `ResultSet` objeto:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Para obter mais informações, consulte [Quickstart: Crie uma base de dados de gráficos em Azure Cosmos DB utilizando o Java SDK](create-graph-java.md).

## <a name="table-api"></a>API de Tabela

Atualmente, o único SDK que devolve a taxa RU para operações de mesa é o [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). O `TableResult` objeto expõe uma propriedade que é `RequestCharge` povoada pelo SDK quando a utiliza contra a API da Tabela DB AZure Cosmos:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Para obter mais informações, consulte [Quickstart: Construa uma aplicação API de tabela utilizando o DB .NET SDK e Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Passos seguintes

Para aprender a otimizar o seu consumo de RU, consulte estes artigos:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
* [Otimizar o débito aprovisionado no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo das consultas no Azure Cosmos DB](optimize-cost-queries.md)
* [Dimensionamento global de débito aprovisionado](scaling-throughput.md)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Produção de provisão para um contentor](how-to-provision-container-throughput.md)
* [Monitore e depure com métricas em Azure Cosmos DB](use-metrics.md)
