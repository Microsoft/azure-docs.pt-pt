---
title: Encontrar a unidade de pedido custos (RU) no Azure Cosmos DB
description: Saiba como encontrar a unidade de pedido custos (RU) para qualquer operação executada em relação a um contentor do Cosmos do Azure.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/14/2019
ms.author: thweiss
ms.openlocfilehash: 07ca6278da6dd1f50f017f389c2c1389a42196f4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163826"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Encontre as cobranças de unidades de pedido no Azure Cosmos DB

Este artigo apresenta as diferentes formas em que pode encontrar os [unidades de pedido](request-units.md) consumo (RU) para qualquer operação executada em relação a um contentor no Azure Cosmos DB. Atualmente, pode medir o consumo deste apenas com o portal do Azure ou ao inspecionar a resposta enviada de volta do Azure Cosmos DB através de um dos SDKs.

## <a name="sql-core-api"></a>API de SQL (Core)

Se estiver a utilizar a API de SQL, tem várias opções para encontrar o consumo de RU para uma operação num contentor do Cosmos do Azure.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Atualmente, pode encontrar o custo de pedido no portal do Azure apenas para uma consulta SQL.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) e inseri-la com os dados ou selecione uma conta existente do Cosmos do Azure que já contenha dados.

1. Vá para o **Data Explorer** painel e, em seguida, selecione o contentor que pretende trabalhar.

1. Selecione **nova consulta SQL**.

1. Introduza uma consulta válida e, em seguida, selecione **executar consulta**.

1. Selecione **estatísticas de consulta** para apresentar a cobrança do pedido real para o pedido é executado.

![Captura de ecrã de um encargo de pedido de consulta SQL no portal do Azure](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Utilizar o V2 do SDK de .NET

Objetos que são devolvidos a partir do [v2 do SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) expor um `RequestCharge` propriedade:

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

Para obter mais informações, consulte [início rápido: Criar uma aplicação web .NET com uma conta da API de SQL no Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Utilizar o Java SDK

Objetos que são devolvidos a partir do [SDK de Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) expor um `getRequestCharge()` método:

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

Para obter mais informações, consulte [início rápido: Criar uma aplicação Java com uma conta do Azure Cosmos DB SQL API](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Utilizar o SDK de node. js

Objetos que são devolvidos a partir do [SDK node. js](https://www.npmjs.com/package/@azure/cosmos) expor um `headers` subobject que mapeia todos os cabeçalhos devolvidos pela API de HTTP subjacente. A cobrança do pedido está disponível na `x-ms-request-charge` chave:

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

Para obter mais informações, consulte [início rápido: Criar uma aplicação de node. js utilizando uma conta do Azure Cosmos DB SQL API](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Utilizar o SDK Python

O `CosmosClient` de objeto do [Python SDK](https://pypi.org/project/azure-cosmos/) expõe um `last_response_headers` dicionário que mapeia todos os cabeçalhos devolvidos pela API HTTP subjacente para a última operação executada. A cobrança do pedido está disponível na `x-ms-request-charge` chave:

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

Para obter mais informações, consulte [início rápido: Criar uma aplicação Python utilizando uma conta do Azure Cosmos DB SQL API](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

O custo de RU é exposto por um personalizado [comandos de base de dados](https://docs.mongodb.com/manual/reference/command/) com o nome `getLastRequestStatistics`. O comando devolve um documento que contém o nome da última operação executada, o custo de pedido e sua duração. Se utilizar a API do Azure Cosmos DB para o MongoDB, tem várias opções para recuperar o custo de RU.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

Atualmente, pode encontrar o custo de pedido no portal do Azure apenas para uma consulta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) e inseri-la com os dados ou selecione uma conta existente que já contenha dados.

1. Vá para o **Data Explorer** painel e, em seguida, selecione a coleção que pretende trabalhar.

1. Selecione **Nova Consulta**.

1. Introduza uma consulta válida e, em seguida, selecione **executar consulta**.

1. Selecione **estatísticas de consulta** para apresentar a cobrança do pedido real para o pedido é executado.

![Captura de ecrã de um encargo de pedido de consulta do MongoDB no portal do Azure](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Utilizar o controlador .NET do MongoDB

Quando utiliza a [oficial controlador do .NET do MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/), pode executar comandos chamando o `RunCommand` método num `IMongoDatabase` objeto. Este método requer uma implementação do `Command<>` classe abstrata:

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

Para obter mais informações, consulte [início rápido: Criar uma aplicação web .NET com uma API do Azure Cosmos DB para o MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Utilizar o controlador Java do MongoDB


Quando utiliza a [controlador oficial de Java do MongoDB](https://mongodb.github.io/mongo-java-driver/), pode executar comandos chamando o `runCommand` método num `MongoDatabase` objeto:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Para obter mais informações, consulte [início rápido: Criar uma aplicação web com a API do Azure Cosmos DB para MongoDB e o SDK de Java](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Utilizar o controlador de node. js do MongoDB

Quando utiliza a [oficial controlador de node. js do MongoDB](https://mongodb.github.io/node-mongodb-native/), pode executar comandos chamando o `command` método num `db` objeto:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Para obter mais informações, consulte [início rápido: Migrar uma aplicação web do node. js do MongoDB existente para o Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>API de Cassandra

Quando efetua operações de acordo com a API de Cassandra do Azure Cosmos DB, a taxa de RU é devolvida no payload de entrada como um campo chamado `RequestCharge`. Tem várias opções para recuperar o custo de RU.

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

Quando utiliza a [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/), pode obter o payload de entrada no `Info` propriedade de um `RowSet` objeto:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Para obter mais informações, consulte [início rápido: Criar uma aplicação do Cassandra com o SDK do .NET e o Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Utilizar o Java SDK

Quando utiliza a [SDK de Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), pode obter o payload de entrada ao chamar o `getExecutionInfo()` método num `ResultSet` objeto:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Para obter mais informações, consulte [início rápido: Criar uma aplicação do Cassandra com o SDK de Java e o Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>API do Gremlin

Quando utiliza a API do Gremlin, tem várias opções para encontrar o consumo de RU para uma operação num contentor do Cosmos do Azure. 

### <a name="use-drivers-and-sdk"></a>Drivers de utilização e SDK

Cabeçalhos devolvidos pela API do Gremlin são mapeados para atributos de estado personalizado, que atualmente, são apresentados do Gremlin .NET e Java. A cobrança do pedido está disponível na `x-ms-request-charge` chave.

### <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

Quando utiliza a [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/), os atributos de estado estão disponíveis no `StatusAttributes` propriedade do `ResultSet<>` objeto:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Para obter mais informações, consulte [início rápido: Criar um aplicativo .NET Framework ou Core com uma conta de API do Azure Cosmos DB Gremlin](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Utilizar o Java SDK

Quando utiliza a [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), pode obter os atributos de estado ao chamar os `statusAttributes()` método no `ResultSet` objeto:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Para obter mais informações, consulte [início rápido: Criar uma base de dados no Azure Cosmos DB com o SDK de Java](create-graph-java.md).

## <a name="table-api"></a>API de Tabela

Atualmente, o SDK de apenas que retorna a cobrança de RU para operações de tabela é o [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). O `TableResult` objeto expõe um `RequestCharge` propriedade que é preenchida com o SDK quando usá-lo contra a API de tabela do Azure Cosmos DB:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Para obter mais informações, consulte [início rápido: Criar uma aplicação API de tabela com o SDK do .NET e o Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como otimizar seu consumo de RU, veja estes artigos:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
* [Otimizar o débito aprovisionado no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](optimize-cost-queries.md)
* [Dimensione globalmente o débito aprovisionado](scaling-throughput.md)
* [Débito de aprovisionar em contentores e bases de dados](set-throughput.md)
* [Débito de aprovisionamento para um contentor](how-to-provision-container-throughput.md)
* [Monitor e de depuração com métricas na Azure Cosmos DB](use-metrics.md)
