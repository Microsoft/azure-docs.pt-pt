---
title: Encontre a taxa da unidade de pedido (RU) para uma consulta SQL em Azure Cosmos DB
description: Saiba como encontrar a taxa da unidade de pedido (RU) para consultas SQL executadas contra um contentor Azure Cosmos. Você pode usar o portal Azure, .NET, Java, Python e Node.js idiomas para encontrar a carga RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: b3e61ca2cee6dd5a2c279b4297e84668729f4ccb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178750"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>Encontre a taxa unitária de pedido para operações executadas na Azure Cosmos DB SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB suporta muitas APIs, tais como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem o seu próprio conjunto de operações de base de dados. Estas operações vão desde leituras simples de pontos e escritas a consultas complexas. Cada operação de base de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações de bases de dados é regulado pelo Azure Cosmos DB e é expresso por Unidades de Pedido (ou RUs, abreviatura em inglês). Pode pensar em RUs como uma moeda de desempenho que abstrai os recursos do sistema, tais como CPU, IOPS e memória que são necessárias para executar as operações de base de dados suportadas pela Azure Cosmos DB. Não interessa que API utiliza para interagir com o contentor do Azure Cosmos: os custos são sempre medidos por RUs. Quer a operação da base de dados seja uma escrita, leitura de ponto ou consulta, os custos são sempre medidos em RUs. Para saber mais, consulte as [unidades de pedido e o artigo de consideração.](request-units.md)

Este artigo apresenta as diferentes formas de encontrar o consumo da [unidade de pedido](request-units.md) (RU) para qualquer operação executada contra um contentor em Azure Cosmos DB SQL API. Se estiver a utilizar uma API diferente, consulte [a API para MongoDB,](find-request-unit-charge-mongodb.md) [Cassandra API,](find-request-unit-charge-cassandra.md) [Gremlin API](find-request-unit-charge-gremlin.md)e artigos [da Table API](find-request-unit-charge-table.md) para encontrar a taxa RU/s.

Atualmente, só pode medir este consumo utilizando o portal Azure ou inspecionando a resposta enviada de Azure Cosmos DB através de um dos SDKs. Se estiver a utilizar a API SQL, tem múltiplas opções para encontrar o consumo de RU para uma operação contra um recipiente Azure Cosmos.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. [Crie uma nova conta Azure Cosmos](create-sql-api-dotnet.md#create-account) e alimente-a com dados, ou selecione uma conta Azure Cosmos existente que já contenha dados.

1. Vá ao painel **do Data Explorer** e, em seguida, selecione o recipiente em que pretende trabalhar.

1. Selecione **Nova Consulta SQL**.

1. Introduza uma consulta válida e, em seguida, selecione **Executar Consulta**.

1. Selecione **Estatísticas de Consulta** para mostrar o custo de pedido real para o pedido que executou.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Screenshot de uma taxa de pedido de consulta SQL no portal Azure":::

## <a name="use-the-net-sdk"></a>Utilizar o .NET SDK

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

## <a name="use-the-java-sdk"></a>Use o Java SDK

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

## <a name="use-the-nodejs-sdk"></a>Use o Node.js SDK

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

## <a name="use-the-python-sdk"></a>Utilizar o SDK Python

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

## <a name="next-steps"></a>Passos seguintes

Para aprender a otimizar o seu consumo de RU, consulte estes artigos:

* [Unidades de pedido e débito no Azure Cosmos DB](request-units.md)
* [Otimizar o débito aprovisionado no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo das consultas no Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Dimensionamento global de débito aprovisionado](./request-units.md)
* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Produção de provisão para um contentor](how-to-provision-container-throughput.md)
* [Monitore e depure com métricas em Azure Cosmos DB](use-metrics.md)
