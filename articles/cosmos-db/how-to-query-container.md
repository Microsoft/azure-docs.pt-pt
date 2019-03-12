---
title: Consultar contentores no Azure Cosmos DB
description: Como consultar contentores no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 2ea228a1db204170f947b5fe71f1865a4620b0f4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549039"
---
# <a name="query-an-azure-cosmos-container"></a>Consultar um contentor do Cosmos do Azure

Este artigo explica como consultar um contentor (gráfico ou uma coleção de tabela) no Azure Cosmos DB.

## <a name="in-partition-query"></a>Consulta na partição

Quando consulta dados a partir de contentores, se a consulta tem um filtro da chave de partição especificado, o Azure Cosmos DB processa automaticamente a consulta. Encaminha a consulta para as partições que correspondem aos valores de chave de partição especificados no filtro. Por exemplo, a seguinte consulta é encaminhada para o `DeviceId` partição, que contém todos os documentos correspondente ao valor de chave de partição `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Consulta entre partições

A seguinte consulta não tem um filtro a chave de partição (`DeviceId`) e é distribuída para todas as partições onde ele é executado no índice da partição. Para executar uma consulta entre partições, definida `EnableCrossPartitionQuery` como true (ou `x-ms-documentdb-query-enablecrosspartition`  na REST API).

A propriedade de EnableCrossPartitionQuery aceita um valor booleano. Quando definido como true e se a sua consulta não tiver uma chave de partição, o Azure Cosmos DB ventiladores horizontalmente a consulta entre partições. O fan-out é feito através da emissão de consultas individuais para todas as partições. Para ler os resultados da consulta, as aplicações de cliente devem consumir os resultados a partir do FeedResponse e verificar a propriedade ContinuationToken. Para ler todos os resultados, mantenha a iteração nos dados até que o ContinuationToken é nulo. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB suporta as funções de agregação COUNT, MIN, Máx e média ao longo de contentores com o SQL. As funções de agregação através de contentores a partir da versão SDK 1.12.0 e posteriores. Consultas tem de incluir um operador de agregação único e tem de incluir um valor único na projecção.

## <a name="parallel-cross-partition-query"></a>Consulta entre partições paralelas

Os SDKs do Azure Cosmos DB 1.9.0 e posterior suportam opções de execução de consulta em paralelo. As consultas entre partições paralelas permitem-lhe realizar consultas entre partições de baixa latência. Por exemplo, a seguinte consulta está configurada para ser executada em paralelo em várias partições.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Pode gerir a execução paralela da consulta ao otimizar os parâmetros abaixo:

- **MaxDegreeOfParallelism**: Define o número máximo de ligações de rede simultâneas às partições do contentor. Se definir esta propriedade como -1, o SDK gerencia o grau de paralelismo. Se o `MaxDegreeOfParallelism` não está especificado ou for definido como 0, o que é o valor predefinido, existe uma ligação de rede única de partições do contentor.

- **MaxBufferedItemCount**: Obra consulta latência em comparação com a utilização da memória do lado do cliente. Se esta opção for omitida ou para definir como -1, o SDK gere o número de itens colocados em memória intermédia durante a execução paralela da consulta.

Com o mesmo Estado da coleção, uma consulta paralela devolve resultados pela mesma ordem como uma execução serial. Ao executar uma consulta entre partições que inclua ordenação (ORDER BY, TOP) de operadores, o SDK do Azure Cosmos DB emite a consulta em paralelo em partições. Ela mescla os resultados ordenados parcialmente no lado do cliente para produzir resultados ordenados globalmente.

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos para saber mais sobre a criação de partições no Azure Cosmos DB:

- [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
- [Chaves de partição sintéticas no Azure Cosmos DB](synthetic-partition-keys.md)
