---
title: Consultar contentores no Azure Cosmos DB
description: Como consultar contentores no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 799fa43ad6ff12e5fa84326cbb41842e76daff12
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092973"
---
# <a name="query-an-azure-cosmos-container"></a>Consultar um contêiner Cosmos do Azure

Este artigo explica como consultar um contêiner (coleção, gráfico ou tabela) em Azure Cosmos DB.

## <a name="in-partition-query"></a>Consulta na partição

Quando você consulta dados de contêineres, se a consulta tiver um filtro de chave de partição especificado, Azure Cosmos DB tratará a consulta automaticamente. Ele roteia a consulta para as partições correspondentes aos valores de chave de partição especificados no filtro. Por exemplo, a consulta a seguir é roteada para a partição de `DeviceId`, que contém todos os documentos correspondentes ao valor de chave de partição `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Consulta entre partições

A consulta a seguir não tem um filtro na chave de partição (`DeviceId`) e é distribuídada para todas as partições em que ele é executado no índice da partição. Para executar uma consulta entre partições, defina `EnableCrossPartitionQuery` como true (ou `x-ms-documentdb-query-enablecrosspartition` na API REST).

A propriedade EnableCrossPartitionQuery aceita um valor booliano. Quando definido como true e se a sua consulta não tiver uma chave de partição, Azure Cosmos DB fãs a consulta entre partições. O Fan out é feito emitindo consultas individuais para todas as partições. Para ler os resultados da consulta, os aplicativos cliente devem consumir os resultados do FeedResponse e verificar a Propriedade ContinuationToken. Para ler todos os resultados, continue Iterando nos dados até que o ContinuationToken seja nulo. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

O Azure Cosmos DB dá suporte a funções de agregação COUNT, MIN, MAX e AVG sobre contêineres usando SQL. As funções de agregação em contêineres começando do SDK versão 1.12.0 e posterior. As consultas devem incluir um único operador de agregação e devem incluir um único valor na projeção.

## <a name="parallel-cross-partition-query"></a>Consulta entre partições paralelas

O Azure Cosmos DB SDKs 1.9.0 e posteriores dão suporte a opções de execução de consulta paralela. As consultas entre partições paralelas permitem-lhe realizar consultas entre partições de baixa latência. Por exemplo, a seguinte consulta está configurada para ser executada em paralelo em várias partições.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Pode gerir a execução paralela da consulta ao otimizar os parâmetros abaixo:

- **MaxDegreeOfParallelism**: define o número máximo de ligações de rede simultâneas às partições do contentor. Se você definir essa propriedade como-1, o SDK gerenciará o grau de paralelismo. Se o  de `MaxDegreeOfParallelism`não for especificado ou definido como 0, que é o valor padrão, haverá uma única conexão de rede com as partições do contêiner.

- **MaxBufferedItemCount**: negoceia a latência da consulta em relação à utilização da memória do lado do cliente. Se essa opção for omitida ou para definir como-1, o SDK gerenciará o número de itens armazenados em buffer durante a execução de consulta paralela.

Com o mesmo estado da coleção, uma consulta paralela retorna resultados na mesma ordem que uma execução serial. Ao executar uma consulta entre partições que inclui operadores de classificação (ORDENAr por, TOP), o SDK do Azure Cosmos DB emite a consulta paralelamente entre partições. Ele mescla resultados parcialmente classificados no lado do cliente para produzir resultados ordenados globalmente.

## <a name="next-steps"></a>Passos seguintes

Consulte os artigos a seguir para saber mais sobre o particionamento no Azure Cosmos DB:

- [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
- [Chaves de partição sintéticas no Azure Cosmos DB](synthetic-partition-keys.md)
