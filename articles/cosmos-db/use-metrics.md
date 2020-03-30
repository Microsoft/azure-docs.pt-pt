---
title: Monitor e depuração com métricas em Azure Cosmos DB
description: Utilize métricas em Azure Cosmos DB para depurar questões comuns e monitorizar a base de dados.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b65bc6097d4841c79a68d4313ac7a3f89f6d1dbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065939"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Monitor e depuração com métricas em Azure Cosmos DB

O Azure Cosmos DB fornece métricas para débito, armazenamento, consistência, disponibilidade e latência. O portal do Azure fornece uma vista agregada destas métricas. Também pode ver as métricas do Azure Cosmos DB na API do Azure Monitor. Para saber como ver as métricas do monitor Azure, consulte as métricas Get do artigo [do Azure Monitor.](cosmos-db-azure-monitor-metrics.md) 

Este artigo passa por casos de uso comum e como as métricas do Azure Cosmos DB podem ser usadas para analisar e depurar estas questões. As métricas são recolhidas a cada cinco minutos e são mantidas por sete dias.

## <a name="view-metrics-from-azure-portal"></a>Ver métricas do portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/)

1. Abra o painel de **Métricas.** Por padrão, o painel de métricas mostra as métricas de armazenamento, índice, unidades de pedidos para todas as bases de dados da sua conta Azure Cosmos. Pode filtrar estas métricas por base de dados, contentor ou região. Também pode filtrar as métricas numa granularidade de tempo específico. Mais detalhes sobre as métricas de entrada, armazenamento, disponibilidade, latência e consistência são fornecidos em separados separados. 

   ![Métricas de desempenho cosmos DB no portal Azure](./media/use-metrics/performance-metrics.png)

As seguintes métricas estão disponíveis no painel de **métricas:** 

* **Métricas** de entrada - Esta métrica mostra o número de pedidos consumidos ou falhados (código de resposta 429) porque a capacidade de entrada ou armazenamento prevista para o recipiente foi ultrapassada.

* **Métricas** de armazenamento - Esta métrica mostra o tamanho dos dados e o uso do índice.

* **Métricas** de disponibilidade - Esta métrica mostra a percentagem de pedidos bem sucedidos ao longo do total de pedidos por hora. A taxa de sucesso é definida pelo Azure Cosmos DB SLAs.

* **Métricas de latência** - Esta métrica mostra a latência de leitura e escrita observada pela Azure Cosmos DB na região onde a sua conta está a funcionar. Pode visualizar a latência em todas as regiões para uma conta geo-replicada. Esta métrica não representa a latência de pedido de ponta a ponta.

* **Métricas** de consistência - Esta métrica mostra como eventual é a consistência para o modelo de consistência que escolhe. Para contas multi-regiões, esta métrica também mostra a latência de replicação entre as regiões que selecionou.

* **Métricas** do sistema - Esta métrica mostra quantos pedidos de metadados são servidos pela partição principal. Também ajuda a identificar os pedidos acelerados.

As seguintes secções explicam cenários comuns onde pode utilizar métricas de DB Do Cosmos Do Azure. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Compreenda quantos pedidos estão a suceder ou a causar erros

Para começar, dirija-se ao [portal Azure](https://portal.azure.com) e navegue até à lâmina **das Métricas.** Na lâmina, encontre o número **Número de pedidos superior à capacidade por gráfico de 1 minuto. Este gráfico mostra um pedido total de minuto a minuto segmentado pelo código de estado. Para obter mais informações sobre os códigos de estado http, consulte os códigos de [estado HTTP para O Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

O código de estado de erro mais comum é 429 (limitação/estrangulamento da taxa). Este erro significa que os pedidos ao Azure Cosmos DB são mais do que a entrada prevista. A solução mais comum para este problema é [aumentar as RUs](./set-throughput.md) para a coleção dada.

![Número de pedidos por minuto](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Determine a distribuição de produtos através das divisórias

Ter uma boa cardinalidade das suas chaves de partição é essencial para qualquer aplicação escalável. Para determinar a distribuição de entrada de qualquer recipiente dividido dividido por divisórias, navegue para a **lâmina Métricas** no [portal Azure](https://portal.azure.com). No separador **De entrada,** a avaria de armazenamento é mostrada no Max consumido RU/segundo por cada gráfico de **partição física.** O gráfico que se segue ilustra um exemplo de uma má distribuição de dados, como mostra a divisão distorcida na extrema esquerda.

![Partição única vendo uso pesado às 15:05](media/use-metrics/metrics-17.png)

Uma distribuição de suposição desigual pode causar divisórias *quentes,* o que pode resultar em pedidos estrangulados e pode exigir a repartição. Para obter mais informações sobre a partilha em Azure Cosmos DB, consulte [partição e escala em Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Determine a distribuição de armazenamento através das divisórias

Ter uma boa cardinalidade da sua partição é essencial para qualquer aplicação escalável. Para determinar a distribuição de armazenamento de qualquer recipiente dividido avariado por divisórias, dirija-se à lâmina Métricas no [portal Azure](https://portal.azure.com). No separador Armazenamento, a avaria de armazenamento é mostrada no armazenamento Data + Index consumido pela tabela de teclas de partição superior. O gráfico seguinte ilustra uma má distribuição do armazenamento de dados, como mostra a divisória distorcida na extrema esquerda.

![Exemplo de má distribuição de dados](media/use-metrics/metrics-07.png)

Pode-se criar a causa que a chave da divisória está a distorcer a distribuição clicando na divisória na tabela.

![A chave da partilha está a distorcer a distribuição](media/use-metrics/metrics-05.png)

Depois de identificar qual a chave de partição que está a causar o enviesamento na distribuição, poderá ter de serpartição do seu recipiente com uma chave de partição mais distribuída. Para obter mais informações sobre a partilha em Azure Cosmos DB, consulte [partição e escala em Azure Cosmos DB](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Comparar o tamanho dos dados com o tamanho do índice

Em Azure Cosmos DB, o armazenamento total consumido é a combinação tanto do tamanho de Dados como do tamanho do Índice. Tipicamente, o tamanho do índice é uma fração do tamanho dos dados. Na lâmina Métricas do [portal Azure,](https://portal.azure.com)o separador de armazenamento apresenta a repartição do consumo de armazenamento com base em dados e índice.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Se quiser conservar o espaço do índice, pode ajustar a política de [indexação.](index-policy.md)

## <a name="debug-why-queries-are-running-slow"></a>Depurar porque as consultas estão a correr lentamente

Nos SDKs SQL API, o Azure Cosmos DB fornece estatísticas de execução de consultas.

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

*A ConsultaMetrics* fornece detalhes sobre quanto tempo cada componente da consulta levou à execução. A causa mais comum para consultas de longa duração são os exames, o que significa que a consulta não foi capaz de alavancar os índices. Este problema pode ser resolvido com uma melhor condição de filtro.

## <a name="next-steps"></a>Passos seguintes

Agora aprendeu a monitorizar e depurar problemas usando as métricas fornecidas no portal Azure. Pode querer saber mais sobre melhorar o desempenho da base de dados lendo os seguintes artigos:

* Para saber como ver as métricas do monitor Azure, consulte as métricas Get do artigo [do Azure Monitor.](cosmos-db-azure-monitor-metrics.md) 
* [Desempenho e testes de escala com Azure Cosmos DB](performance-testing.md)
* [Sugestões de desempenho do Azure Cosmos DB](performance-tips.md)
