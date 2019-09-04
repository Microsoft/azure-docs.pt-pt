---
title: Localizar a cobrança da unidade de solicitação (RU) no Azure Cosmos DB
description: Saiba como encontrar a cobrança de RU (unidade de solicitação) para qualquer operação executada em um contêiner Cosmos do Azure.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: c5699bb851bd0a818a987228155c62683e93f51a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240776"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Localizar o encargo de unidade de solicitação em Azure Cosmos DB

Este artigo apresenta as diferentes maneiras pelas quais você pode encontrar o consumo da ru ( [unidade de solicitação](request-units.md) ) para qualquer operação executada em um contêiner em Azure Cosmos DB. No momento, você pode medir esse consumo somente usando o portal do Azure ou inspecionando a resposta enviada de volta do Azure Cosmos DB por meio de um dos SDKs.

## <a name="sql-core-api"></a>API de SQL (Core)

Se você estiver usando a API do SQL, terá várias opções para localizar o consumo de RU para uma operação em um contêiner Cosmos do Azure.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

No momento, você pode encontrar o encargo da solicitação no portal do Azure apenas para uma consulta SQL.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) e o feed com dados ou selecione uma conta existente do Azure cosmos que já contenha dados.

1. Vá para o painel de **Data Explorer** e, em seguida, selecione o contêiner no qual você deseja trabalhar.

1. Selecione **nova consulta SQL**.

1. Insira uma consulta válida e, em seguida, selecione **Executar consulta**.

1. Selecione **Estatísticas de consulta** para exibir o encargo de solicitação real para a solicitação que você executou.

![Captura de tela de uma cobrança de solicitação de consulta SQL no portal do Azure](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK
### <a name="net-v2-sdk"></a>SDK do .net v2

Os objetos que são retornados do [SDK do .net v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) expõem uma `RequestCharge` Propriedade:

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

### <a name="net-v3-sdk"></a>SDK do .net v3

Os objetos que são retornados do [SDK do .net v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) expõem uma `RequestCharge` Propriedade:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Para obter mais informações, [consulte início rápido: Crie um aplicativo Web .NET usando uma conta da API do SQL no](create-sql-api-dotnet.md)Azure Cosmos DB.

### <a name="use-the-java-sdk"></a>Usar o SDK do Java

Os objetos que são retornados do [SDK do Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) expõem um `getRequestCharge()` método:

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

Para obter mais informações, [consulte início rápido: Crie um aplicativo Java usando uma conta](create-sql-api-java.md)de API do SQL Azure Cosmos DB.

### <a name="use-the-nodejs-sdk"></a>Usar o SDK do node. js

Os objetos que são retornados do [SDK do node. js](https://www.npmjs.com/package/@azure/cosmos) expõem um `headers` subobjeto que mapeia todos os cabeçalhos retornados pela API http subjacente. O encargo da solicitação está disponível na `x-ms-request-charge` chave:

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

Para obter mais informações, [consulte início rápido: Crie um aplicativo node. js usando uma Azure Cosmos DB conta](create-sql-api-nodejs.md)da API do SQL. 

### <a name="use-the-python-sdk"></a>Usar o SDK do Python

O `CosmosClient` objeto do [SDK do python](https://pypi.org/project/azure-cosmos/) expõe um `last_response_headers` dicionário que mapeia todos os cabeçalhos retornados pela API http subjacente para a última operação executada. O encargo da solicitação está disponível na `x-ms-request-charge` chave:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Para obter mais informações, [consulte início rápido: Crie um aplicativo Python usando uma Azure Cosmos DB conta](create-sql-api-python.md)da API do SQL. 

## <a name="azure-cosmos-db-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

O encargo de RU é exposto por um [comando](https://docs.mongodb.com/manual/reference/command/) de banco `getLastRequestStatistics`de dados personalizado chamado. O comando retorna um documento que contém o nome da última operação executada, seu encargo de solicitação e sua duração. Se você usar a API de Azure Cosmos DB para MongoDB, terá várias opções para recuperar o encargo de RU.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

No momento, você pode encontrar o encargo da solicitação no portal do Azure apenas para uma consulta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) e o feed com dados ou selecione uma conta existente que já contenha dados.

1. Vá para o painel de **Data Explorer** e, em seguida, selecione o contêiner no qual você deseja trabalhar.

1. Selecione **Nova Consulta**.

1. Insira uma consulta válida e, em seguida, selecione **Executar consulta**.

1. Selecione **Estatísticas de consulta** para exibir o encargo de solicitação real para a solicitação que você executou.

![Captura de tela de um encargo de solicitação de consulta do MongoDB no portal do Azure](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Usar o driver .NET do MongoDB

Ao usar o [Driver .net do MongoDB oficial](https://docs.mongodb.com/ecosystem/drivers/csharp/), você pode executar comandos chamando o `RunCommand` método em um `IMongoDatabase` objeto. Este método requer uma implementação da `Command<>` classe abstrata:

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

Para obter mais informações, [consulte início rápido: Compilar um aplicativo Web .NET usando uma API Azure Cosmos DB para MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Usar o driver Java do MongoDB


Ao usar o [Driver Java do MongoDB oficial](https://mongodb.github.io/mongo-java-driver/), você pode executar comandos chamando o `runCommand` método em um `MongoDatabase` objeto:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Para obter mais informações, [consulte início rápido: Crie um aplicativo Web usando a API de Azure Cosmos DB para MongoDB e o SDK](create-mongodb-java.md)do Java.

### <a name="use-the-mongodb-nodejs-driver"></a>Usar o driver node. js do MongoDB

Ao usar o [Driver do node. js do MongoDB oficial](https://mongodb.github.io/node-mongodb-native/), você pode executar comandos chamando o `command` método em um `db` objeto:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Para obter mais informações, [consulte início rápido: Migre um aplicativo Web node. js do MongoDB existente para](create-mongodb-nodejs.md)Azure Cosmos DB.

## <a name="cassandra-api"></a>API de Cassandra

Quando você executa operações no API do Cassandra de Azure Cosmos DB, o encargo de RU é retornado na carga de entrada como um campo `RequestCharge`chamado. Você tem várias opções para recuperar o encargo de RU.

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

Ao usar o [SDK do .net](https://www.nuget.org/packages/CassandraCSharpDriver/), você pode recuperar a carga de entrada sob `Info` a propriedade de `RowSet` um objeto:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Para obter mais informações, [consulte início rápido: Crie um aplicativo Cassandra usando o SDK do .NET e o](create-cassandra-dotnet.md)Azure Cosmos DB.

### <a name="use-the-java-sdk"></a>Usar o SDK do Java

Ao usar o [SDK do Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), você pode recuperar a carga de entrada chamando o `getExecutionInfo()` método em um `ResultSet` objeto:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Para obter mais informações, [consulte início rápido: Crie um aplicativo Cassandra usando o SDK do Java e Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>API do Gremlin

Ao usar a API Gremlin, você tem várias opções para localizar o consumo de RU para uma operação em um contêiner Cosmos do Azure. 

### <a name="use-drivers-and-sdk"></a>Usar drivers e SDK

Os cabeçalhos retornados pela API Gremlin são mapeados para atributos de status personalizados, que atualmente são exibidos pelo SDK do Gremlin .NET e do Java. O encargo da solicitação está disponível sob `x-ms-request-charge` a chave.

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

Quando você usa o [SDK do Gremlin.net](https://www.nuget.org/packages/Gremlin.Net/), os atributos de status estão `StatusAttributes` disponíveis na propriedade `ResultSet<>` do objeto:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Para obter mais informações, [consulte início rápido: Crie um aplicativo .NET Framework ou Core usando uma conta](create-graph-dotnet.md)de API do Gremlin Azure Cosmos DB.

### <a name="use-the-java-sdk"></a>Usar o SDK do Java

Ao usar o [SDK do Java do Gremlin](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), você pode recuperar atributos de status chamando `statusAttributes()` o método no `ResultSet` objeto:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Para obter mais informações, [consulte início rápido: Crie um banco de dados de grafo no Azure Cosmos DB usando o](create-graph-java.md)SDK do Java.

## <a name="table-api"></a>API de Tabela

Atualmente, o único SDK que retorna a carga de RU para operações de tabela é o [.net Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). O `TableResult` objeto expõe uma `RequestCharge` propriedade que é populada pelo SDK quando você a utiliza em relação ao Azure Cosmos DB API de tabela:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Para obter mais informações, [consulte início rápido: Crie um aplicativo API de Tabela usando o SDK do .NET e o](create-table-dotnet.md)Azure Cosmos DB.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como otimizar seu consumo de RU, consulte estes artigos:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
* [Otimizar o débito aprovisionado no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo da consulta no Azure Cosmos DB](optimize-cost-queries.md)
* [Dimensionar globalmente a taxa de transferência provisionada](scaling-throughput.md)
* [Provisionar taxa de transferência em contêineres e bancos de dados](set-throughput.md)
* [Provisionar a taxa de transferência para um contêiner](how-to-provision-container-throughput.md)
* [Monitorar e depurar com métricas no Azure Cosmos DB](use-metrics.md)
