---
title: Problemas de consulta de resolução de problemas ao utilizar a API DB API do Azure Cosmos para a MongoDB
description: Aprenda a identificar, diagnosticar e resolver problemas a API da Azure Cosmos para questões de consulta mongoDB.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.subservice: cosmosdb-mongo
ms.date: 03/02/2021
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5302cb7bb3f4683d200f6f9ea106991bb934fc17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101659907"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>Problemas de consulta de resolução de problemas ao utilizar a API DB API do Azure Cosmos para a MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este artigo percorre uma abordagem geral recomendada para consultas de resolução de problemas em Azure Cosmos DB. Embora não deva considerar os passos descritos neste artigo como uma defesa completa contra potenciais problemas de consulta, incluímos as dicas de desempenho mais comuns aqui. Você deve usar este artigo como um local de partida para resolver consultas lentas ou caras na API da Azure Cosmos DB para MongoDB. Se estiver a utilizar a API do núcleo DB do Azure Cosmos (SQL), consulte o artigo guia [de resolução de problemas da API SQL.](troubleshoot-query-performance.md)

As otimizações de consultas em Azure Cosmos DB são amplamente categorizadas da seguinte forma:

- Otimizações que reduzem a carga da Unidade de Pedido (RU) da consulta
- Otimizações que apenas reduzem a latência

Se reduzir a carga RU de uma consulta, você normalmente diminuirá a latência também.

Este artigo fornece exemplos que pode recriar utilizando o conjunto de [dados nutricionais](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

> [!NOTE] 
> Este artigo pressupõe que está a usar a API da Azure Cosmos para contas MongoDB com a versão 3.6 ou superior. Algumas consultas que têm um desempenho fraco na versão 3.2 têm melhorias significativas nas versões 3.6+. Upgrade para a versão 3.6, apresentando um pedido de [apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="use-explain-command-to-get-metrics"></a>Use $explain comando para obter métricas

Quando otimiza uma consulta em Azure Cosmos DB, o primeiro passo é sempre [obter a taxa RU](find-request-unit-charge-mongodb.md) para a sua consulta. Como uma orientação áspera, você deve explorar formas de baixar a taxa RU para consultas com encargos superiores a 50 RUs. 

Além de obter a carga RU, deve utilizar o `$explain` comando para obter as métricas de utilização de consultas e índices. Aqui está um exemplo que executa uma consulta e usa o `$explain` comando para mostrar métricas de consulta e uso de índice:

**$explain comando:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**Saída:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

A `$explain` saída do comando é longa e tem informações detalhadas sobre a execução de consultas. No entanto, em geral, existem algumas secções em que deve focar-se na otimização do desempenho da consulta:

| Metric | Descrição | 
| ------ | ----------- |
| `timeInclusiveMS` | Latência de consulta de backend |
| `pathsIndexed` | Mostra índices que a consulta usada | 
| `pathsNotIndexed` | Mostra índices que a consulta poderia ter usado, se disponível | 
| `shardInformation` | Resumo do desempenho da consulta para uma [partição física](./partitioning-overview.md#physical-partitions) particular | 
| `retrievedDocumentCount` | Número de documentos carregados pelo motor de consulta | 
| `outputDocumentCount` | Número de documentos devolvidos nos resultados da consulta | 
| `estimatedDelayFromRateLimitingInMilliseconds` | Latência adicional estimada devido à limitação da taxa | 

Depois de obter as métricas de consulta, compare a `retrievedDocumentCount` com a para a sua `outputDocumentCount` consulta. Utilize esta comparação para identificar as secções relevantes para rever neste artigo. É `retrievedDocumentCount`  o número de documentos que o motor de consulta precisa de carregar. `outputDocumentCount`Trata-se do número de documentos necessários para os resultados da consulta. Se o `retrievedDocumentCount`  for significativamente maior do que o `outputDocumentCount` , havia pelo menos uma parte da sua consulta que não era capaz de usar um índice e precisava de fazer uma varredura.

Consulte as seguintes secções para entender as otimizações de consultas relevantes para o seu cenário.

### <a name="querys-ru-charge-is-too-high"></a>A carga ru da consulta é muito alta

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>A contagem de documentos recuperados é significativamente maior do que a contagem de documentos de saída

- [Incluir índices necessários.](#include-necessary-indexes)

- [Entenda quais as operações de agregação que usam o índice.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>A contagem de documentos recuperados é aproximadamente igual à contagem de documentos de saída

- [Minimize as consultas de partição cruzada.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>A taxa de RU da Consulta é aceitável, mas a latência ainda é muito alta

- [Melhorar a proximidade.](#improve-proximity)

- [Aumentar a produção a provisionada.](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Consultas onde a contagem de documentos recuperados excede a contagem de documentos de saída

 É `retrievedDocumentCount` o número de documentos que o motor de consulta precisava de carregar. É `outputDocumentCount` o número de documentos devolvidos pela consulta. Se o `retrievedDocumentCount` for significativamente maior do que o `outputDocumentCount` , havia pelo menos uma parte da sua consulta que não era capaz de usar um índice e precisava de fazer uma varredura.

Aqui está um exemplo de consulta de digitalização que não foi inteiramente servido pelo índice:

**$explain comando:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**Saída:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

O `retrievedDocumentCount` (8618) é significativamente maior do que o `outputDocumentCount` (1), o que implica que esta consulta exigia uma verificação de documento. 

### <a name="include-necessary-indexes"></a>Incluir índices necessários

Deve verificar a `pathsNotIndexed` matriz e adicionar estes índices. Neste exemplo, os caminhos `foodGroup` e `description` devem ser indexados.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

A indexação das melhores práticas na API da Azure Cosmos para a MongoDB é diferente da MongoDB. Na API da Azure Cosmos DB para o MongoDB, os índices compostos são usados apenas em consultas que precisam de classificar eficientemente por múltiplas propriedades. Se tiver consultas com filtros em várias propriedades, deverá criar índices de campo únicos para cada uma destas propriedades. Os predicados de consulta podem usar vários índices de campo único.

[Os índices wildcard](mongodb-indexing.md#wildcard-indexes) podem simplificar a indexação. Ao contrário do MongoDB, os índices wildcard podem suportar vários campos em predicados de consulta. Não haverá uma diferença no desempenho da consulta se utilizar um único índice wildcard em vez de criar um índice separado para cada propriedade. Adicionar um índice wildcard para todas as propriedades é a maneira mais fácil de otimizar todas as suas consultas.

Pode adicionar novos índices a qualquer momento, sem qualquer efeito na disponibilidade de escrita ou leitura. Pode [acompanhar o progresso da transformação do índice.](./how-to-manage-indexing-policy.md#dotnet-sdk)

### <a name="understand-which-aggregation-operations-use-the-index"></a>Entenda quais as operações de agregação que usam o índice

Na maioria dos casos, as operações de agregação na API da Azure Cosmos DB para a MongoDB utilizarão parcialmente índices. Normalmente, o motor de consulta aplicará primeiro os filtros de igualdade e gama e utilizará índices. Após a aplicação destes filtros, o motor de consulta pode avaliar filtros adicionais e recorrer ao carregamento de documentos restantes para calcular o agregado, se necessário. 

Eis um exemplo:

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

Neste caso, os índices podem otimizar o `$match` palco. A adição de um índice `foodGroup` irá melhorar significativamente o desempenho da consulta. Como em MongoDB, você deve colocar `$match` o mais cedo possível no pipeline de agregação para maximizar o uso de índices.

Na API da Azure Cosmos DB para o MongoDB, os índices não são utilizados para a agregação real, que neste caso é `$max` . Adicionar um índice `version` não melhorará o desempenho da consulta.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Consultas onde a contagem de documentos recuperada é igual à contagem de documentos de saída

Se o `retrievedDocumentCount` é aproximadamente igual ao `outputDocumentCount` , o motor de consulta não teve que digitalizar muitos documentos desnecessários.

### <a name="minimize-cross-partition-queries"></a>Minimizar consultas de partição cruzada

A Azure Cosmos DB utiliza [divisórias](partitioning-overview.md) para escalar contentores individuais à medida que a Unidade de Pedido e as necessidades de armazenamento de dados aumentam. Cada divisória física tem um índice separado e independente. Se a sua consulta tiver um filtro de igualdade que corresponda à chave de partição do seu recipiente, terá de verificar apenas o índice de partição relevante. Esta otimização reduz o número total de RUs que a consulta requer. [Saiba mais sobre as diferenças entre consultas de partição e consultas de divisórias.](how-to-query-container.md)

Se tiver um grande número de RUs provisões (mais de 30.000) ou uma grande quantidade de dados armazenados (mais de 100 GB), provavelmente tem um recipiente suficientemente grande para ver uma redução significativa nos encargos ru de consulta. 

Pode verificar a `shardInformation` matriz para entender as métricas de consulta de cada partição física individual. O número de valores únicos `shardKeyRangeId` é o número de divisórias físicas onde a consulta precisava de ser executada. Neste exemplo, a consulta foi executada em quatro divisórias físicas. É importante entender que a execução é completamente independente da utilização do índice. Por outras palavras, as consultas de partição cruzada ainda podem usar índices.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>Otimizações que reduzem a latência da consulta

Em muitos casos, a taxa RU pode ser aceitável quando a latência da consulta ainda é demasiado elevada. As seguintes secções dão uma visão geral das dicas para reduzir a latência da consulta. Se executar a mesma consulta várias vezes no mesmo conjunto de dados, normalmente terá a mesma carga RU de cada vez. Mas a latência da consulta pode variar entre execuções de consultas.

### <a name="improve-proximity"></a>Melhorar a proximidade

As consultas que são geridas a partir de uma região diferente da conta DB do Azure Cosmos terão maior latência do que se fossem geridas dentro da mesma região. Por exemplo, se estiver a executar código no seu computador de secretária, deve esperar que a latência seja dezenas ou centenas de milissegundos mais elevados (ou mais) do que se a consulta viesse de uma máquina virtual dentro da mesma região de Azure Cosmos DB. É simples [distribuir globalmente dados em Azure Cosmos DB](tutorial-global-distribution-mongodb.md) para garantir que pode aproximar os seus dados da sua app.

### <a name="increase-provisioned-throughput"></a>Aumento da produção a provisionada

Em Azure Cosmos DB, a sua produção provisida é medida em Unidades de Pedido (RUs). Imagine que tem uma consulta que consome 5 RUs de produção. Por exemplo, se você fornece 1.000 RUs, você seria capaz de executar essa consulta 200 vezes por segundo. Se tentou executar a consulta quando não havia produção suficiente disponível, a Azure Cosmos DB irá limitar os pedidos. A API da Azure Cosmos DB para a MongoDB irá automaticamente repetir esta consulta depois de esperar por um curto período de tempo. Os pedidos de aceleração demoram mais tempo, pelo que o aumento da produção a provisionada pode melhorar a latência da consulta.

O valor `estimatedDelayFromRateLimitingInMilliseconds` dá uma ideia dos potenciais benefícios de latência se aumentar a produção.

## <a name="next-steps"></a>Passos seguintes

* [Desempenho da consulta de resolução de problemas (SQL API)](troubleshoot-query-performance.md)
* [Gerir os índices na API para MongoDB do Azure Cosmos DB](mongodb-indexing.md)
