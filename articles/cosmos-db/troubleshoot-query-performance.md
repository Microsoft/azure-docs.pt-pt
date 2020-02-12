---
title: Problemas de consulta ao usar O Azure Cosmos DB
description: Aprenda a identificar, diagnosticar e resolver problemas com problemas de consulta do Azure Cosmos DB SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 34f5de01df72b48d275448e028ab0f8cb71e51f8
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132071"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Problemas de consulta ao usar O Azure Cosmos DB

Este artigo percorre uma abordagem geral recomendada para a solução de problemas de consultas no Azure Cosmos DB. Embora as etapas descritas neste documento não devam ser consideradas "capturar tudo" para possíveis problemas de consulta, incluímos as dicas de desempenho mais comuns aqui. Você deve usar este documento como um local de início para solucionar problemas de consultas lentas ou caras na API principal (SQL) do Azure Cosmos DB. Também pode utilizar [registos](cosmosdb-monitor-resource-logs.md) de diagnóstico para identificar consultas que sejam lentas ou que consumam quantidades significativas de entrada.

Você pode categorizar amplamente as otimizações de consulta no Azure Cosmos DB: otimizações que reduzem o encargo da RU (unidade de solicitação) da consulta e otimizações que apenas reduzem a latência. Ao reduzir a carga de RU de uma consulta, você quase certamente diminuirá a latência também.

Este documento usará exemplos que podem ser recriados usando o conjunto de dados [nutricionais.](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

### <a name="obtaining-query-metrics"></a>Obtendo métricas de consulta:

Ao otimizar uma consulta em Azure Cosmos DB, o primeiro passo é sempre [obter as métricas](profile-sql-api-query.md) de consulta para a sua consulta. Eles também estão disponíveis por meio do portal do Azure, conforme mostrado abaixo:

[![Obtenção de métricas de consulta](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Depois de obter as métricas de consulta, compare a contagem de documentos recuperados com a contagem de documentos de saída para sua consulta. Use essa comparação para identificar as seções relevantes a serem referenciadas abaixo.

A contagem de documentos recuperados é o número de documentos que a consulta precisava carregar. A contagem de documentos de saída é o número de documentos que foram necessários para os resultados da consulta. Se a contagem de documentos recuperados for significativamente maior que a contagem de documentos de saída, então, havia pelo menos uma parte da consulta que não pôde utilizar o índice e necessário fazer uma verificação.

Você pode fazer referência à seção abaixo para entender as otimizações de consulta relevantes para seu cenário:

### <a name="querys-ru-charge-is-too-high"></a>A cobrança de RU da consulta é muito alta

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>A contagem de documentos recuperados é significativamente maior que a contagem de documentos de saída

- [Incluir os caminhos necessários na política de indexação](#include-necessary-paths-in-the-indexing-policy)

- [Entenda quais as funções do sistema que utilizam o índice](#understand-which-system-functions-utilize-the-index)

- [Consultas com um filtro e uma cláusula ORDER BY](#queries-with-both-a-filter-and-an-order-by-clause)

- [Otimizar as expressões JOIN utilizando uma subqueria](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>A contagem de documentos recuperados é aproximadamente igual à contagem de documentos de saída

- [Evite consultas de partição cruzada](#avoid-cross-partition-queries)

- [Filtros em várias propriedades](#filters-on-multiple-properties)

- [Consultas com um filtro e uma cláusula ORDER BY](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>A cobrança de RU da consulta é aceitável, mas a latência ainda é muito alta

- [Melhorar a proximidade](#improve-proximity)

- [Aumentar o acréscimo previsto](#increase-provisioned-throughput)

- [Aumentar a moeda MaxCon](#increase-maxconcurrency)

- [Aumentar MaxBufferedItemCount](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Consultas em que a contagem de documentos recuperados excede a contagem de documentos de saída

 A contagem de documentos recuperados é o número de documentos que a consulta precisava carregar. A contagem de documentos de saída é o número de documentos que foram necessários para os resultados da consulta. Se a contagem de documentos recuperados for significativamente maior que a contagem de documentos de saída, então, havia pelo menos uma parte da consulta que não pôde utilizar o índice e necessário fazer uma verificação.

 Veja abaixo um exemplo de consulta de verificação que não foi totalmente servida pelo índice.

Consulta:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Métricas de consulta:

```
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

A contagem de documentos recuperados (60.951) é significativamente maior que a contagem de documentos de saída (7) para que essa consulta seja necessária para fazer uma verificação. Neste caso, a função do sistema [UPPER()](sql-query-upper.md) não utiliza o índice.

## <a name="include-necessary-paths-in-the-indexing-policy"></a>Incluir caminhos necessários na política de indexação

A sua política de indexação deve abranger quaisquer propriedades incluídas em cláusulas `WHERE`, cláusulas `ORDER BY`, `JOIN`e a maioria das Funções do Sistema. O caminho especificado na política de índice deve corresponder (diferencia maiúsculas de minúsculas) à propriedade nos documentos JSON.

Se fizermos uma simples consulta no conjunto de dados [nutricionais,](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) observamos uma carga de RU muito mais baixa quando a propriedade na cláusula `WHERE` é indexada.

### <a name="original"></a>Original

Consulta:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Política de indexação:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**RU Charge:** 409.51 RU's

### <a name="optimized"></a>Otimizado

Política de indexação atualizada:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**RU Charge:** 2.98 RU's

Você pode adicionar propriedades adicionais à política de indexação a qualquer momento, sem afetar a disponibilidade ou o desempenho da gravação. Se você adicionar uma nova propriedade ao índice, as consultas que usam essa propriedade usarão imediatamente o novo índice disponível. A consulta usará o novo índice enquanto ele estiver sendo compilado. Como resultado, os resultados da consulta podem ser inconsistentes à medida que a recompilação do índice está em andamento. Se uma nova propriedade for indexada, as consultas que utilizam apenas índices existentes não serão afetadas durante a recompilação do índice. Pode acompanhar o progresso da [transformação do índice.](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)

## <a name="understand-which-system-functions-utilize-the-index"></a>Entender quais funções do sistema utilizam o índice

Se a expressão puder ser traduzida para um intervalo de valores de cadeia, pode utilizar o índice; caso contrário, não pode.

Aqui está a lista de funções de cadeia que podem utilizar o índice:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0

Algumas funções comuns do sistema que não usam o índice e que devem carregar cada documento estão abaixo:

| **Função do Sistema**                     | **Ideias para otimização**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Usar Azure Search para pesquisa de texto completo                        |
| SUPERIOR/INFERIOR                             | Em vez de usar a função do sistema para normalizar os dados a cada vez para comparações, em vez disso, Normalize a capitalização após a inserção. Então uma consulta como ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` simplesmente torna-se ```SELECT * FROM c WHERE c.name = 'BOB'``` |
| Funções matemáticas (não agregadas) | Se você precisar calcular com frequência um valor em sua consulta, considere armazenar esse valor como uma propriedade em seu documento JSON. |

------

Outras partes da consulta ainda podem utilizar o índice, apesar das funções do sistema não usarem o índice.

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>Consultas com um filtro e uma cláusula ORDER BY

Embora as consultas com um filtro e uma cláusula `ORDER BY` normalmente utilizem um índice de gama, serão mais eficientes se puderem ser servidas a partir de um índice composto. Além de modificar a política de indexação, deve adicionar todas as propriedades do índice composto à cláusula `ORDER BY`. Essa modificação de consulta garantirá que ela utilize o índice composto.  Pode observar o impacto executando uma consulta no conjunto de [dados](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) nutricional.

### <a name="original"></a>Original

Consulta:

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

Política de indexação:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**RU Charge:** 44.28 RU's

### <a name="optimized"></a>Otimizado

Consulta atualizada (inclui ambas as propriedades na cláusula `ORDER BY`):

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies”
ORDER BY c.foodGroup, c._ts ASC
```

Política de indexação atualizada:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**RU Charge:** 8.86 RU's

## <a name="optimize-join-expressions-by-using-a-subquery"></a>Otimizar expressões de junção usando uma subconsulta
Subqueries de vários valores podem otimizar expressões `JOIN` empurrando predicados após cada expressão selecionada em vez de afinal se juntarem à cláusula `WHERE`.

Considere a seguinte consulta:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**RU Charge:** 167.62 RU's

Para essa consulta, o índice corresponderá a qualquer documento que tenha uma marca com o nome "Infant Formula", nutritionValue maior que 0 e atendendo a quantidade maior que 1. A expressão `JOIN` aqui irá realizar o cross-product de todos os itens de tags, nutrientes e porções arrays para cada documento correspondente antes de qualquer filtro ser aplicado. A cláusula `WHERE` aplicará então o predicado do filtro em cada `<c, t, n, s>` tuple.

Por exemplo, se um documento correspondente tiver 10 itens em cada uma das três matrizes, ele se expandirá para uma tupla de 1 x 10 x 10 x 10 (ou seja, 1.000). O uso de subconsultas aqui pode ajudar na filtragem de itens de matriz Unidos antes da junção com a próxima expressão.

Essa consulta é equivalente à anterior, mas usa subconsultas:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU Charge:** 22.17 RU's

Suponha que apenas um item na matriz de marcas corresponda ao filtro e que haja cinco itens para nutrients e atende a matrizes. As expressões `JOIN` expandir-se-ão então para 1 x 1 x 5 x 5 = 25 itens, em oposição a 1.000 itens na primeira consulta.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Consultas em que a contagem de documentos recuperados é igual à contagem de documentos de saída

Se a contagem de documentos recuperados for aproximadamente igual à contagem de documentos de saída, isso significará que a consulta não precisou verificar muitos documentos desnecessários. Para muitas consultas, como as que usam a palavra-chave TOP, a contagem de documentos recuperados pode exceder a contagem de documentos de saída em 1. Isso não deve causar preocupação.

## <a name="avoid-cross-partition-queries"></a>Evitar consultas entre partições

A Azure Cosmos DB utiliza [a partilha](partitioning-overview.md) para escalar os contentores individuais à medida que a Unidade de Pedido e as necessidades de armazenamento de dados aumentam. Cada partição física tem um índice separado e independente. Se sua consulta tiver um filtro de igualdade que corresponda à chave de partição do contêiner, você só precisará verificar o índice da partição relevante. Essa otimização reduz o número total de RU que a consulta requer.

Se você tiver um grande número de RU provisionadas (mais de 30.000) ou uma grande quantidade de dados armazenados (mais de ~ 100 GB), provavelmente terá um contêiner grande o suficiente para ver uma redução significativa nos encargos de consulta RU.

Por exemplo, se criarmos um contêiner com a chave de partição de alimentos, as consultas a seguir precisarão apenas verificar uma única partição física:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Essas consultas também seriam otimizadas com a inclusão da chave de partição na consulta:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

As consultas que têm filtros de intervalo na chave de partição ou não têm filtros na chave de partição precisarão de "Fan-out" e verificar o índice de cada partição física para obter resultados.

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>Filtros em várias propriedades

Embora as consultas com filtros em várias propriedades normalmente utilizem um índice de intervalo, elas serão mais eficientes se puderem ser servidas de um índice composto. Para pequenas quantidades de dados, essa otimização não terá um impacto significativo. No entanto, pode-se provar útil para grandes quantidades de dados. Você só pode otimizar, no máximo, um filtro de não igualdade por índice composto. Se sua consulta tiver vários filtros de não igualdade, você deverá escolher um deles que utilizará o índice composto. O restante continuará a utilizar índices de intervalo. O filtro de não igualdade deve ser definido por último no índice composto. [Saiba mais sobre índices compostos](index-policy.md#composite-indexes)

Aqui estão alguns exemplos de consultas que podem ser otimizadas com um índice composto:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Aqui está o índice composto relevante:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Otimizações que reduzem a latência de consulta:

Em muitos casos, a carga de RU pode ser aceitável, mas a latência de consulta ainda é muito alta. As seções abaixo fornecem uma visão geral das dicas para reduzir a latência da consulta. Se você executar a mesma consulta várias vezes no mesmo conjunto de mesmos, ela terá a mesma cobrança de RU a cada vez. No entanto, a latência de consulta pode variar entre as execuções de consulta.

## <a name="improve-proximity"></a>Melhorar a proximidade

As consultas executadas a partir de uma região diferente da conta de Azure Cosmos DB terão uma latência maior do que se fossem executadas dentro da mesma região. Por exemplo, se você estiver executando código em seu computador desktop, deverá esperar que a latência seja de dezenas ou centenas (ou mais) milissegundos maiores do que se a consulta vier de uma máquina virtual na mesma região do Azure que Azure Cosmos DB. É simples [distribuir globalmente dados no Azure Cosmos DB](distribute-data-globally.md) para garantir que pode aproximar os seus dados da sua aplicação.

## <a name="increase-provisioned-throughput"></a>Aumentar a taxa de transferência provisionada

Em Azure Cosmos DB, sua taxa de transferência provisionada é medida em unidades de solicitação (RU). Vamos imaginar que você tenha uma consulta que consuma 5 RU de taxa de transferência. Por exemplo, se você provisionar 1.000 RU, poderá executar essa consulta 200 vezes por segundo. Se você tentou executar a consulta quando não havia taxa de transferência suficiente disponível, Azure Cosmos DB retornaria um erro HTTP 429. Qualquer um dos principais SDK da API do núcleo (SQL) tentará automaticamente essa consulta depois de aguardar um breve período. As solicitações limitadas demoram mais tempo, portanto, aumentar a taxa de transferência provisionada pode melhorar a latência da consulta. Pode observar o [número total de pedidos solicitados](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) na lâmina métrica do portal Azure.

## <a name="increase-maxconcurrency"></a>Aumentar MaxConcurrency

As consultas paralelas funcionam consultando várias partições em paralelo. No entanto, os dados de uma coleção particionada individual são buscados em série em relação à consulta. Portanto, ajustar o MaxConcurrency para o número de partições tem a chance máxima de alcançar a consulta de melhor desempenho, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não souber o número de partições, poderá definir MaxConcurrency (ou MaxDegreesOfParallelism em versões mais antigas do SDK) para um número alto, e o sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o grau máximo de paralelismo.

## <a name="increase-maxbuffereditemcount"></a>Aumentar MaxBufferedItemCount

As consultas são projetadas para buscar antecipadamente resultados enquanto o lote atual de resultados está sendo processado pelo cliente. A busca prévia ajuda na melhoria da latência geral de uma consulta. Definir o MaxBufferedItemCount limita o número de resultados previamente buscados. Ao definir esse valor como o número esperado de resultados retornados (ou um número mais alto), a consulta pode receber o máximo benefício da busca prévia. Definir esse valor como-1 permite que o sistema decida automaticamente o número de itens para armazenar em buffer.

## <a name="next-steps"></a>Passos seguintes
Consulte os documentos abaixo sobre como medir as RUs por consulta, obtenha estatísticas de execução para afinar as suas consultas, e muito mais:

* [Get SQL query execution metrics using .NET SDK](profile-sql-api-query.md) (Obter métricas de execução de consultas SQL através do SDK do .NET)
* [Otimização do desempenho de consulta com o Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Sugestões de desempenho para o SDK .NET](performance-tips.md)
