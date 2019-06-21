---
title: Monitor e de depuração com métricas na Azure Cosmos DB
description: Utilize métricas no Azure Cosmos DB para depurar problemas comuns e monitorizar a base de dados.
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 06/18/2019
ms.reviewer: sngun
ms.openlocfilehash: ef457fe8c21bc7e62f910a78913069df32bea1a3
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275675"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitor e de depuração com métricas na Azure Cosmos DB

O Azure Cosmos DB fornece as métricas de débito, armazenamento, consistência, disponibilidade e latência. O portal do Azure fornece uma exibição agregada destas métricas. Também pode ver as métricas do Azure Cosmos DB a partir da API do Azure Monitor. Para saber mais sobre como ver as métricas do monitor do Azure, consulte a [obter métricas do Azure Monitor](cosmos-db-azure-monitor-metrics.md) artigo. 

Este artigo explica-casos de utilização comuns e como as métricas do Azure Cosmos DB podem ser usadas para analisar e depurar esses problemas. As métricas são recolhidas a cada cinco minutos e são mantidas durante sete dias.

## <a name="view-metrics-from-azure-portal"></a>Ver métricas no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/)

1. Abra o **métricas** painel. Por predefinição, o painel de métricas mostra o armazenamento de índice, as métricas de unidades de pedido para todas as bases de dados na sua conta do Cosmos do Azure. Pode filtrar estas métricas por base de dados, contentor ou uma região. Também pode filtrar as métricas com uma granularidade de tempo específico. Obter mais detalhes sobre o débito, armazenamento, disponibilidade, latência e métricas de consistência são fornecidas nos separadores separados. 

   ![Métricas de desempenho do cosmos DB no portal do Azure](./media/use-metrics/performance-metrics.png)

As métricas seguintes estão disponíveis a partir da **métricas** painel: 

* **Métricas de débito** -esta métrica mostra o número de pedidos consumidos ou falhou (código de resposta 429), porque excedeu a capacidade de armazenamento ou débito para o contentor.

* **Métricas de armazenamento** -esta métrica mostra o tamanho da utilização de dados e índice.

* **As métricas de disponibilidade** -esta métrica mostra a percentagem de pedidos com êxito ao longo do total de pedidos por hora. A taxa de êxito é definida pelos SLAs do Azure Cosmos DB.

* **As métricas de latência** -esta métrica mostra a latência de leitura e escrita foi observada pelo Azure Cosmos DB na região em que sua conta está a funcionar. É possível visualizar a latência em várias regiões para uma conta georreplicado. Esta métrica não representa a latência de pedido de ponto-a-ponto.

* **Métricas de consistência** -esta métrica mostra como eventual é a consistência para o modelo de consistência que escolher. Para contas de várias regiões, esta métrica mostra também a latência de replicação entre regiões do que selecionou.

* **As métricas do sistema** -esta métrica mostra o número de pedidos de metadados é servido pela partição principal. Ele também ajuda a identificar os pedidos limitados.

As secções seguintes explicam os cenários comuns em que pode utilizar as métricas do Azure Cosmos DB. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Compreender o número de pedidos está a ter êxito ou a que está causando erros

Para começar, vá para o [portal do Azure](https://portal.azure.com) e navegue para o **métricas** painel. No painel, localize a * * número de pedidos excedeu a capacidade por 1 minuto gráfico. Este gráfico mostra um minuto a minuto total de pedidos segmentados pelo código de estado. Para obter mais informações sobre códigos de estado HTTP, consulte [códigos de estado HTTP para o Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

O código de estado de erro mais comuns é 429 (limitação/limitação da taxa de). Este erro significa que os pedidos ao Azure Cosmos DB são mais do que o débito aprovisionado. A solução mais comuns para esse problema é [aumentar verticalmente o RUs](./set-throughput.md) para determinada coleção.

![Número de pedidos por minuto](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Determinar a distribuição de débito entre partições

Ter uma boa cardinalidade das suas chaves de partição é essencial para qualquer aplicação dimensionável. Para determinar a distribuição de taxa de transferência de qualquer contentor particionado dividida por partições, navegue para o **painel de métricas** no [portal do Azure](https://portal.azure.com). Na **débito** separador, a divisão de armazenamento é mostrada na **RU/segundo máximos consumidos por cada partição física** gráfico. O gráfico seguinte ilustra um exemplo de uma distribuição fraco de dados, conforme exibido a partição distorcida mais à esquerda.

![Ver o uso intenso às 15:00: 05 de partição única](media/use-metrics/metrics-17.png)

Pode fazer com que uma distribuição desigual de débito *frequente* partições, que podem resultar em pedidos limitados e podem exigir a repartição. Para obter mais informações sobre a criação de partições no Azure Cosmos DB, consulte [particionar e dimensionar no Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Determinar a distribuição de armazenamento em várias partições

Ter uma boa cardinalidade da sua partição é essencial para qualquer aplicação dimensionável. Para determinar a distribuição de armazenamento de qualquer contentor particionado dividida por partições, vá para o painel de métricas no [portal do Azure](https://portal.azure.com). No separador de armazenamento, a divisão de armazenamento é mostrada nos dados + armazenamento consumido pelo gráfico de chaves de partição principais de índice. O gráfico seguinte ilustra uma distribuição fraco de armazenamento de dados, conforme exibido a partição distorcida mais à esquerda.

![Exemplo de distribuição de dados fraca](media/use-metrics/metrics-07.png)

Pode causa ao qual chave de partição é a inclinação a distribuição clicando numa partição no gráfico.

![Chave de partição é a inclinação de distribuição](media/use-metrics/metrics-05.png)

Depois de identificar qual chave de partição está a causar a distorção na distribuição, poderá ter de criar novas partições seu contentor com uma chave de partição mais distribuída. Para obter mais informações sobre a criação de partições no Azure Cosmos DB, consulte [particionar e dimensionar no Azure Cosmos DB](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Comparar o tamanho dos dados contra o tamanho do índice

No Azure Cosmos DB, o armazenamento total consumido é a combinação do tamanho dos dados e o tamanho do índice. Normalmente, o tamanho do índice é uma fração do tamanho dos dados. No painel de métricas a [portal do Azure](https://portal.azure.com), separador armazenamento demonstra a divisão do consumo de armazenamento com base nos dados e índice.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Se desejar conservar espaço de índice, pode ajustar os [política de indexação](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Depuração, por que consultas estão lentas

Os SDKs de API de SQL do Azure Cosmos DB fornece estatísticas de execução de consulta.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* fornece detalhes sobre cada componente da consulta de quanto tempo demorou a execução. A causa mais comum para consultas de execução longa é análises, que significa que a consulta não foi possível ao tirar partido de índices. Esse problema pode ser resolvido com uma condição de filtro melhor.

## <a name="next-steps"></a>Passos Seguintes

Agora já aprendeu como monitorizar e problemas de depuração com as métricas fornecidas no portal do Azure. Pode querer saber mais sobre como melhorar o desempenho da base de dados ao ler os artigos seguintes:

* Para saber mais sobre como ver as métricas do monitor do Azure, consulte a [obter métricas do Azure Monitor](cosmos-db-azure-monitor-metrics.md) artigo. 
* [Desempenho e dimensionamento testes com o Azure Cosmos DB](performance-testing.md)
* [Sugestões de desempenho para o Azure Cosmos DB](performance-tips.md)
