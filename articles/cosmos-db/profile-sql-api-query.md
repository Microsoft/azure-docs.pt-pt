---
title: Obter métricas de desempenho e a execução de consulta SQL
description: Saiba como obter métricas de execução de consulta SQL e o perfil de desempenho de consultas SQL de pedidos do Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: b4017666956d0e01ea19781fb4f1ce2dde15fff5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481568"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Obter métricas de execução de consulta SQL e analisar o desempenho de consulta com o .NET SDK

Este artigo apresenta como para criar um perfil de desempenho de consultas SQL no Azure Cosmos DB. Essa determinação de perfil pode ser feita utilizando `QueryMetrics` obtidos a partir do SDK de .NET e está detalhada aqui. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) é um objeto com rigidez de tipos com informações sobre a execução de consulta do back-end. Estas métricas são documentadas em mais detalhes a [otimizar o desempenho das consultas](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) artigo.

## <a name="set-the-feedoptions-parameter"></a>Defina o parâmetro FeedOptions

Todas as sobrecargas para [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) tirar do opcional [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) parâmetro. Esta opção é o que permite a execução da consulta ser ajustado e parametrizado. 

Para recolher as métricas de execução de consulta de Sql, tem de definir o parâmetro [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) no [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) para `true`. A definição `PopulateQueryMetrics` para true fará com que, para que o `FeedResponse` irá conter o relevante `QueryMetrics`. 

## <a name="get-query-metrics-with-asdocumentquery"></a>Obter métricas de consulta com AsDocumentQuery()
O exemplo de código seguinte mostra como obter métricas ao usar [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) método:

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Agregar QueryMetrics

Na secção anterior, tenha em atenção que havia várias chamadas para [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) método. Cada chamada devolveu um `FeedResponse` objeto que tem um dicionário de `QueryMetrics`; uma para cada continuação da consulta. O exemplo seguinte mostra como agregar estes `QueryMetrics` usando LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Métricas de consulta de agrupamento por ID de partição

Pode agrupar o `QueryMetrics` pelo ID de partição. Agrupamento por ID de partição permite-lhe ver se uma partição específica está a causar problemas de desempenho em comparação com outras pessoas. O exemplo seguinte mostra como agrupar `QueryMetrics` com LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ no DocumentQuery

Também pode obter o `FeedResponse` de uma consulta de LINQ utilizando o `AsDocumentQuery()` método:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Consultas dispendiosas

Pode capturar as unidades de pedido consumidas por cada consulta para investigar consultas dispendiosas ou consultas que consomem o débito elevado. Pode obter o custo de pedido com o [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) propriedade no `FeedResponse`. Para saber mais sobre como obter o custo de pedido com o portal do Azure e SDKs diferentes, veja [encontrar as cobranças de unidades de pedido](find-request-unit-charge.md) artigo.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Obter o tempo de execução de consulta

Ao calcular o tempo necessário para executar uma consulta do lado do cliente, certifique-se de que inclui apenas o tempo para chamar o `ExecuteNextAsync` método e não para outras partes da sua base de código. Apenas essas chamadas ajudá-lo no cálculo quanto tempo a execução da consulta demorou conforme mostrado no exemplo a seguir:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Analisar de consultas (normalmente lentas e caras)

Uma consulta de análise refere-se a uma consulta que não fosse servida pelo índice, devido a que, muitos documentos são carregados antes de retornar o conjunto de resultados.

Segue-se um exemplo de uma consulta de análise:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Filtro desta consulta utiliza a função de sistema canto superior, que não é servido a partir do índice. Executar esta consulta em relação a uma grande coleção produzido as métricas de consulta seguinte para a primeira continuação:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Tenha em atenção os seguintes valores a partir da saída de métricas de consulta:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Esta consulta carregada 60,951 documentos, o que totalizados 399,998,938 bytes. A carregar vários bytes resulta em Cobranças de unidades de pedido de custo ou alta. Ela também leva muito tempo para executar a consulta, que é clara com a propriedade de tempo total gasto:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

O que significa que a consulta demorou 4.5 segundos para ser executada (e isso foi apenas uma continuação).

Para otimizar esta consulta de exemplo, evite a utilização de superior no filtro. Em vez disso, quando documentos são criados ou atualizados, o `c.description` valores tem de ser inseridos em todos os carateres em maiúsculas. A consulta, em seguida, se torna: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Esta consulta agora é capaz de ser servido a partir do índice.

Para saber mais sobre a otimização do desempenho de consulta, consulte a [otimizar o desempenho das consultas](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) artigo.

## <a id="References"></a>Referências

- [Especificação de Cosmos DB SQL do Azure](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Passos Seguintes

- [Otimizar o desempenho de consulta](sql-api-query-metrics.md)
- [Descrição geral de indexação](index-overview.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
