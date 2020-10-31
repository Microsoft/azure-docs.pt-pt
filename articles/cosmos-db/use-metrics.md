---
title: Monitore e depure com métricas em Azure Cosmos DB
description: Utilize métricas em Azure Cosmos DB para depurar questões comuns e monitorizar a base de dados.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8694a884b26194c61cc77d00848692a24e3009be
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073711"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitore e depure com métricas em Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB fornece métricas para débito, armazenamento, consistência, disponibilidade e latência. O portal do Azure fornece uma vista agregada destas métricas. Também pode ver as métricas do Azure Cosmos DB na API do Azure Monitor. Os valores de dimensão para as métricas como o nome do recipiente são insensíveis ao caso. Por isso, tens de usar a comparação caso-insensível ao fazer comparações de cordas sobre estes valores de dimensão. Para saber como ver as métricas do monitor Azure, consulte as [métricas get do artigo do Azure Monitor.](./monitor-cosmos-db.md)

Este artigo aborda casos de utilização comum e a forma como as métricas do Azure Cosmos DB podem ser utilizadas para analisar e depurar estas questões. As métricas são recolhidas a cada cinco minutos e são mantidas durante sete dias.

## <a name="view-metrics-from-azure-portal"></a>Ver métricas do portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/)

1. Abra o **painel métrica.** Por padrão, o painel de métricas mostra as métricas de armazenamento, índice, unidades de pedido para todas as bases de dados na sua conta Azure Cosmos. Pode filtrar estas métricas por base de dados, contentor ou região. Também pode filtrar as métricas num momento específico de granularidade. Mais detalhes sobre a produção, armazenamento, disponibilidade, latência e métricas de consistência são fornecidos em separado separados. 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Métricas de desempenho do Cosmos DB no portal Azure":::

As seguintes métricas estão disponíveis no **painel métrica:** 

* **Métricas de produção** - Esta métrica mostra o número de pedidos consumidos ou falhados (código de resposta 429) porque a capacidade de produção ou armazenamento prevista para o contentor excedeu.

* **Métricas de armazenamento** - Esta métrica mostra o tamanho dos dados e a utilização do índice.

* **Métricas de disponibilidade** - Esta métrica mostra a percentagem de pedidos bem sucedidos ao longo do total de pedidos por hora. A taxa de sucesso é definida pelos Azure Cosmos DLAs.

* **Métricas de latência** - Esta métrica mostra a latência lida e escrita observada pela Azure Cosmos DB na região onde a sua conta está a funcionar. Pode visualizar a latência em todas as regiões para uma conta geo-replicada. Esta métrica não representa a latência do pedido de ponta a ponta.

* **Métricas de consistência** - Esta métrica mostra como a consistência é eventual para o modelo de consistência que você escolhe. Para contas multi-regiões, esta métrica também mostra a latência de replicação entre as regiões que selecionou.

* **Métricas do sistema** - Esta métrica mostra quantos pedidos de metadados são servidos pela partição primária. Também ajuda a identificar os pedidos acelerados.

As secções seguintes explicam cenários comuns onde pode utilizar métricas DB do Azure Cosmos. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Entenda quantos pedidos estão a ter sucesso ou a causar erros

Para começar, dirija-se ao [portal Azure](https://portal.azure.com) e navegue até à lâmina **métrica.** Na lâmina, encontre o número **de pedidos excedeu a capacidade por gráfico de 1 minuto. Este gráfico mostra um minuto a minuto de pedidos totais segmentados pelo código de estado. Para obter mais informações sobre os códigos de estado HTTP, consulte [os códigos de estado HTTP para Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

O código de estado de erro mais comum é 429 (limitação/estrangulamento de taxa). Este erro significa que os pedidos à Azure Cosmos DB são mais do que o rendimento previsto. A solução mais comum para este problema é [aumentar as RUs](./set-throughput.md) para a recolha dada.

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="Métricas de desempenho do Cosmos DB no portal Azure":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>Determinar a distribuição de produção através de divisórias

Ter uma boa cardinalidade das suas chaves de partição é essencial para qualquer aplicação escalável. Para determinar a distribuição de produção de qualquer recipiente dividido por divisórias, navegue até à **lâmina métrica** do [portal Azure](https://portal.azure.com). No **separador Deprodução,** a avaria de armazenamento é mostrada no **Max consumido RU/segundo por cada gráfico de partição física.** O gráfico que se segue ilustra um exemplo de uma má distribuição de dados, como mostra a divisória distorcida na extrema esquerda.

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="Métricas de desempenho do Cosmos DB no portal Azure":::

Uma distribuição de produção desigual pode causar divisórias *quentes,* o que pode resultar em pedidos acelerados e pode exigir repartição. Para obter mais informações sobre a partição em Azure Cosmos DB, consulte [a Partição e a escala em Azure Cosmos DB](./partitioning-overview.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Determinar a distribuição de armazenamento através de divisórias

Ter uma boa cardinalidade da sua partição é essencial para qualquer aplicação escalável. Para determinar a distribuição de armazenamento de qualquer recipiente dividido por divisórias, dirija-se à lâmina métrica do [portal Azure](https://portal.azure.com). No separador Armazenamento, a desagregação de armazenamento é mostrada no armazenamento data + índice consumido pelo gráfico de chaves de partição superior. O gráfico que se segue ilustra uma má distribuição do armazenamento de dados, como mostra a divisória distorcida na extrema esquerda.

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="Métricas de desempenho do Cosmos DB no portal Azure":::

Pode raírar a causa da chave de partição que está a distorcer a distribuição clicando na partição na tabela.

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="Métricas de desempenho do Cosmos DB no portal Azure":::

Depois de identificar qual a chave de partição que está a causar a distorção na distribuição, poderá ter de repartir o seu recipiente com uma chave de partição mais distribuída. Para obter mais informações sobre a partição em Azure Cosmos DB, consulte [a Partição e a escala em Azure Cosmos DB](./partitioning-overview.md).

## <a name="compare-data-size-against-index-size"></a>Compare o tamanho dos dados com o tamanho do índice

Em Azure Cosmos DB, o armazenamento total consumido é a combinação tanto do tamanho de Dados como do tamanho do Índice. Tipicamente, o tamanho do índice é uma fração do tamanho dos dados. Na lâmina Métricas do [portal Azure,](https://portal.azure.com)o separador de Armazenamento mostra a desagregação do consumo de armazenamento com base em dados e índice.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Se quiser conservar o espaço de índice, pode ajustar [a política de indexação](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Depurar por que as consultas estão a correr devagar

Nos SDKs da API SQL, a Azure Cosmos DB fornece estatísticas de execução de consultas.

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

*A ConsultaMetrics* fornece detalhes sobre quanto tempo cada componente da consulta demorou a ser executado. A causa mais comum para consultas de longa duração são os exames, o que significa que a consulta não foi capaz de alavancar os índices. Este problema pode ser resolvido com uma melhor condição de filtro.

## <a name="next-steps"></a>Passos seguintes

Agora aprendeu a monitorizar e depurar problemas usando as métricas fornecidas no portal Azure. Pode querer saber mais sobre a melhoria do desempenho da base de dados lendo os seguintes artigos:

* Para saber como ver as métricas do monitor Azure, consulte as [métricas get do artigo do Azure Monitor.](./monitor-cosmos-db.md) 
* [Testes de desempenho e escala com Azure Cosmos DB](performance-testing.md)
* [Sugestões de desempenho do Azure Cosmos DB](performance-tips.md)