---
title: Obtenha desempenho de consulta SQL & métricas de execução
description: Saiba como recuperar as métricas de execução de consultas SQL e perfil SQL desempenho de consulta de pedidos de Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 71ebc90834083def5b82e16dc387a6e61943206d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021753"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Obtenha métricas de execução de consulta SQL e analise o desempenho da consulta usando .NET SDK

Este artigo apresenta como perfilar o desempenho da consulta SQL no Azure Cosmos DB. Este perfil pode ser feito utilizando `QueryMetrics` o .NET SDK e é detalhado aqui. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) é um objeto fortemente dactilografado com informações sobre a execução de consulta de backend. Estas métricas são documentadas mais detalhadamente no artigo [de Desempenho da Query Tune.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="set-the-feedoptions-parameter"></a>Definir o parâmetro FeedOptions

Todas as sobrecargas para [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) tomam um parâmetro opcional [de Opções de Feed.](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) Esta opção é o que permite que a execução de consultas seja afinada e parametrizada. 

Para recolher as métricas de execução de consultas Sql, deve definir o parâmetro [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) nas [Opições de Feed](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) para `true` . A definição `PopulateQueryMetrics` de verdade fará com que a vontade `FeedResponse` contenha o relevante `QueryMetrics` . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Obtenha métricas de consulta com AsDocumentQuery()
A amostra de código que se segue mostra como fazer métricas de recuperação quando se utiliza o método [AsDocumentQuery():](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx)

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
## <a name="aggregating-querymetrics"></a>Consulta de Agregação

Na secção anterior, note que houve várias chamadas para executar o método [ExecutarNextAsync.](https://msdn.microsoft.com/library/azure/dn850294.aspx) Cada chamada devolveu um `FeedResponse` objeto que tem um dicionário `QueryMetrics` de; um para cada continuação da consulta. O exemplo a seguir mostra como agregar estes `QueryMetrics` usando LINQ:

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

Pode agrupar `QueryMetrics` o ID pela Partição. O agrupamento por ID de partição permite-lhe ver se uma partição específica está a causar problemas de desempenho quando comparado com outros. O exemplo a seguir mostra como `QueryMetrics` agrupar-se com o LINQ:

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

Também pode obter o `FeedResponse` de uma consulta LINQ utilizando o `AsDocumentQuery()` método:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Consultas caras

Pode capturar as unidades de pedido consumidas por cada consulta para investigar consultas caras ou consultas que consomem alta produção. Pode obter o custo de pedido utilizando o imóvel [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) em `FeedResponse` . Para saber mais sobre como obter a taxa de pedido usando o portal Azure e diferentes SDKs, consulte o artigo [de cobrança da unidade de pedido.](find-request-unit-charge.md)

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

## <a name="get-the-query-execution-time"></a>Obtenha o tempo de execução da consulta

Ao calcular o tempo necessário para executar uma consulta do lado do cliente, certifique-se de que apenas inclui o tempo para ligar para o `ExecuteNextAsync` método e não para outras partes da sua base de código. Apenas estas chamadas ajudam-no a calcular quanto tempo a execução de consulta demorou como mostrado no seguinte exemplo:

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>Consultas de digitalização (geralmente lentas e caras)

Uma consulta de digitalização refere-se a uma consulta que não foi servida pelo índice, devido à qual, muitos documentos são carregados antes de devolver o conjunto de resultados.

Abaixo está um exemplo de uma consulta de digitalização:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Este filtro de consulta utiliza a função do sistema UPPER, que não é servida a partir do índice. A execução desta consulta contra uma grande coleção produziu as seguintes métricas de consulta para a primeira continuação:

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

Note os seguintes valores da saída das métricas de consulta:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Esta consulta carregou 60.951 documentos, que totalizaram 399.998.938 bytes. Carregar estes muitos bytes resulta em custos elevados ou solicitar a taxa unitária. Também leva muito tempo para executar a consulta, o que é claro com o tempo total gasto propriedade:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

O que significa que a consulta demorou 4,5 segundos a ser executada (e esta foi apenas uma continuação).

Para otimizar esta consulta de exemplo, evite a utilização de UPPER no filtro. Em vez disso, quando os documentos são criados ou `c.description` atualizados, os valores devem ser inseridos em todos os caracteres maiúsculas. A consulta torna-se então: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Esta consulta pode agora ser servida a partir do índice.

Para saber mais sobre o desempenho da consulta de sintonização, consulte o artigo ['Tune Query Performance'.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="references"></a><a id="References"></a>Referências

- [Especificação de Azure Cosmos DB SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Passos seguintes

- [Otimizar o desempenho de consultas](sql-api-query-metrics.md)
- [Descrição geral da indexação](index-overview.md)
- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
