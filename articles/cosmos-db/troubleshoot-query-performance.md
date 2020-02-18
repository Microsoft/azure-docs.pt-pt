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
ms.openlocfilehash: aae11facd2fea5413b2996b3088cb2edc23f0dc1
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77424937"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Problemas de consulta ao usar O Azure Cosmos DB

Este artigo percorre uma abordagem geral recomendada para consultas de resolução de problemas em Azure Cosmos DB. Embora os passos delineados neste documento não devam ser considerados um "catch all" para potenciais questões de consulta, incluímos aqui as dicas de desempenho mais comuns. Você deve usar este documento como um ponto de partida para resolução de problemas consultas lentas ou caras no núcleo de Azure Cosmos DB (SQL) API. Também pode utilizar [registos](cosmosdb-monitor-resource-logs.md) de diagnóstico para identificar consultas que sejam lentas ou que consumam quantidades significativas de entrada.

Você pode amplamente categorizar otimizações de consulta em Azure Cosmos DB: Otimizações que reduzem a carga da Unidade de Pedido (RU) das consultas e otimizações que apenas reduzem a latência. Ao reduzir a carga de RU de uma consulta, certamente também diminuirá a latência.

Este documento usará exemplos que podem ser recriados usando o conjunto de dados [nutricionais.](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

### <a name="obtaining-query-metrics"></a>Obtenção de métricas de consulta:

Ao otimizar uma consulta em Azure Cosmos DB, o primeiro passo é sempre [obter as métricas](profile-sql-api-query.md) de consulta para a sua consulta. Estes também estão disponíveis através do portal Azure, como mostrado abaixo:

[![Obtenção de métricas de consulta](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Depois de obter métricas de consulta, compare a Contagem de Documentos Recuperado com a Contagem de Documentos de Saída para a sua consulta. Utilize esta comparação para identificar as secções relevantes para referência abaixo.

O Conde de Documentos Recuperados é o número de documentos que a consulta precisava para carregar. O Conde de Documentos de Saída é o número de documentos necessários para os resultados da consulta. Se o Conde de Documentos Recuperados for significativamente superior ao Conde de Documentos de Saída, então havia pelo menos uma parte da sua consulta que não foi capaz de utilizar o índice e precisava de fazer uma verificação.

Pode fazer referência à secção abaixo para compreender as otimizações de consulta relevantes para o seu cenário:

### <a name="querys-ru-charge-is-too-high"></a>A acusação de RU da Consulta é muito alta.

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>Contagem de documentos recuperado é significativamente maior do que a contagem de documentos de saída

- [Incluir os caminhos necessários na política de indexação](#include-necessary-paths-in-the-indexing-policy)

- [Entenda quais as funções do sistema que utilizam o índice](#understand-which-system-functions-utilize-the-index)

- [Consultas com um filtro e uma cláusula ORDER BY](#queries-with-both-a-filter-and-an-order-by-clause)

- [Otimizar as expressões JOIN utilizando uma subqueria](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Contagem de documentos recuperado é aproximadamente igual à contagem de documentos de saída

- [Evite consultas de partição cruzada](#avoid-cross-partition-queries)

- [Filtros em várias propriedades](#filters-on-multiple-properties)

- [Consultas com um filtro e uma cláusula ORDER BY](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>A acusação de RU da Consulta é aceitável, mas a latência ainda é muito alta.

- [Melhorar a proximidade](#improve-proximity)

- [Aumentar o acréscimo previsto](#increase-provisioned-throughput)

- [Aumentar a moeda MaxCon](#increase-maxconcurrency)

- [Aumentar MaxBufferedItemCount](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Consultas em que a contagem de documentos recuperada excede a contagem de documentos de saída

 O Conde de Documentos Recuperados é o número de documentos que a consulta precisava para carregar. O Conde de Documentos de Saída é o número de documentos necessários para os resultados da consulta. Se o Conde de Documentos Recuperados for significativamente superior ao Conde de Documentos de Saída, então havia pelo menos uma parte da sua consulta que não foi capaz de utilizar o índice e precisava de fazer uma verificação.

 Abaixo está um exemplo de consulta de digitalização que não foi inteiramente servido pelo índice.

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

A contagem de documentos recuperada (60.951) é significativamente maior do que a Contagem de Documentos de Saída (7) pelo que esta consulta necessária para fazer uma verificação. Neste caso, a função do sistema [UPPER()](sql-query-upper.md) não utiliza o índice.

## <a name="include-necessary-paths-in-the-indexing-policy"></a>Incluir os caminhos necessários na política de indexação

A sua política de indexação deve abranger quaisquer propriedades incluídas em cláusulas `WHERE`, cláusulas `ORDER BY`, `JOIN`e a maioria das Funções do Sistema. O caminho especificado na política de índice deve coincidir (sensível a casos) à propriedade nos documentos JSON.

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

Pode adicionar propriedades adicionais à política de indexação a qualquer momento, sem impacto para escrever disponibilidade ou desempenho. Se adicionar uma nova propriedade ao índice, as consultas que utilizam esta propriedade utilizarão imediatamente o novo índice disponível. A consulta utilizará o novo índice enquanto está a ser construído. Como resultado, os resultados da consulta podem ser inconsistentes, uma vez que a reconstrução do índice está em curso. Se uma nova propriedade for indexada, as consultas que apenas utilizam os índices existentes não serão afetadas durante a reconstrução do índice. Pode acompanhar o progresso da [transformação do índice.](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)

## <a name="understand-which-system-functions-utilize-the-index"></a>Entenda quais as funções do sistema que utilizam o índice

Se a expressão puder ser traduzida para um intervalo de valores de cadeia, pode utilizar o índice; caso contrário, não pode.

Aqui está a lista de funções de cadeia que podem utilizar o índice:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0

Algumas funções comuns do sistema que não utilizam o índice e devem carregar cada documento são abaixo:

| **Função do Sistema**                     | **Ideias para otimização**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Utilizar a Pesquisa Azure para pesquisa completa de texto                        |
| SUPERIOR/INFERIOR                             | Em vez de utilizar a função do sistema para normalizar os dados de cada vez para comparações, em vez disso, normalizar o invólucro após a inserção. Então uma consulta como ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` simplesmente torna-se ```SELECT * FROM c WHERE c.name = 'BOB'``` |
| Funções matemáticas (não agregados) | Se precisar calcular frequentemente um valor na sua consulta, considere armazenar este valor como propriedade no seu documento JSON. |

------

Outras partes da consulta podem ainda utilizar o índice, apesar das funções do sistema não utilizarem o índice.

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>Consultas com um filtro e uma cláusula ORDER BY

Embora as consultas com um filtro e uma cláusula `ORDER BY` normalmente utilizem um índice de gama, serão mais eficientes se puderem ser servidas a partir de um índice composto. Além de modificar a política de indexação, deve adicionar todas as propriedades do índice composto à cláusula `ORDER BY`. Esta modificação de consulta assegurará que utiliza o índice composto.  Pode observar o impacto executando uma consulta no conjunto de [dados](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) nutricional.

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

## <a name="optimize-join-expressions-by-using-a-subquery"></a>Otimizar as expressões JOIN utilizando uma subqueria
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

Para esta consulta, o índice corresponderá a qualquer documento que tenha uma etiqueta com o nome "fórmula infantil", nutriçãoValor superior a 0, e servindo uma quantidade superior a 1. A expressão `JOIN` aqui irá realizar o cross-product de todos os itens de tags, nutrientes e porções arrays para cada documento correspondente antes de qualquer filtro ser aplicado. A cláusula `WHERE` aplicará então o predicado do filtro em cada `<c, t, n, s>` tuple.

Por exemplo, se um documento correspondente tivesse 10 itens em cada uma das três matrizes, expandir-se-á para 1 x 10 x 10 x 10 x 10 (isto é, 1.000) tuples. A utilização de subqueimentos aqui pode ajudar a filtrar itens de matriz unificados antes de se juntar à próxima expressão.

Esta consulta é equivalente à anterior, mas utiliza subqueries:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU Charge:** 22.17 RU's

Assuma que apenas um item na matriz de etiquetas corresponde ao filtro, e existem cinco itens para nutrientes e porções arrays. As expressões `JOIN` expandir-se-ão então para 1 x 1 x 5 x 5 = 25 itens, em oposição a 1.000 itens na primeira consulta.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Consultas onde a contagem de documentos recuperadoé igual à contagem de documentos de saída

Se a contagem de documentos recuperado for aproximadamente igual à Contagem de Documentos de Saída, significa que a consulta não teve de digitalizar muitos documentos desnecessários. Para muitas consultas, como as que usam a palavra-chave TOP, a Contagem de Documentos Recuperados pode exceder a Contagem de Documentos de Saída por 1. Isto não deve ser motivo de preocupação.

## <a name="avoid-cross-partition-queries"></a>Evite consultas de partição cruzada

A Azure Cosmos DB utiliza [a partilha](partitioning-overview.md) para escalar os contentores individuais à medida que a Unidade de Pedido e as necessidades de armazenamento de dados aumentam. Cada partição física tem um índice separado e independente. Se a sua consulta tiver um filtro de igualdade que corresponda à chave de partição do seu recipiente, só terá de verificar o índice de partição relevante. Esta otimização reduz o número total de RU's que a consulta requer.

Se tiver um grande número de RU 's aprovisionados (mais de 30.000) ou uma grande quantidade de dados armazenados (mais de 100 GB), você provavelmente tem um recipiente grande o suficiente para ver uma redução significativa nas taxas de RU consulta.

Por exemplo, se criarmos um recipiente com a chave de partilha de alimentosGroup, as seguintes consultas só teriam de verificar uma única partição física:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Estas consultas também seriam otimizadas através da inclusão da chave de partição na consulta:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

As consultas que têm filtros de alcance na chave da divisória ou não têm filtros na chave da divisória, terão de "sair" e verificar os resultados de todos os divisórias físicos.

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>Filtros em várias propriedades

Embora consultas com filtros em várias propriedades normalmente utilizem um índice de gama, serão mais eficientes se puderem ser servidos a partir de um índice composto. Para pequenas quantidades de dados, esta otimização não terá um impacto significativo. Pode revelar-se útil, no entanto, para grandes quantidades de dados. Só é possível otimizar, no máximo, um filtro de não igualdade por índice composto. Se a sua consulta tiver vários filtros de não igualdade, deve escolher um deles que utilize o índice composto. O restante continuará a utilizar índices de gama. O filtro de não igualdade deve ser definido em último no índice composto. [Saiba mais sobre índices compostos](index-policy.md#composite-indexes)

Aqui estão alguns exemplos de consultas que poderiam ser otimizadas com um índice composto:

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

## <a name="optimizations-that-reduce-query-latency"></a>Otimizações que reduzem a latência da consulta:

Em muitos casos, a acusação de RU pode ser aceitável, mas a latência da consulta ainda é muito elevada. As secções abaixo dão uma visão geral das dicas para reduzir a latência da consulta. Se fizer a mesma consulta várias vezes no mesmo conjunto de dados, terá a mesma carga de RU de cada vez. No entanto, a latência de consulta pode variar entre execuções de consulta.

## <a name="improve-proximity"></a>Melhorar a proximidade

As consultas que são geridas de uma região diferente da conta Azure Cosmos DB terão uma latência maior do que se fossem executadas dentro da mesma região. Por exemplo, se estiver a executar código no seu computador de secretária, deve esperar que a latência seja de dezenas ou centenas (ou mais) milissegundos maiordo do que se a consulta viesse de uma Máquina Virtual dentro da mesma região azure que o Azure Cosmos DB. É simples [distribuir globalmente dados no Azure Cosmos DB](distribute-data-globally.md) para garantir que pode aproximar os seus dados da sua aplicação.

## <a name="increase-provisioned-throughput"></a>Aumentar o acréscimo previsto

Em Azure Cosmos DB, a sua entrada é medida em Unidades de Pedido (RU's). Imaginemos que tem uma consulta que consome 5 RU de entrada. Por exemplo, se fornecer 1.000 RU's, poderá fazer essa consulta 200 vezes por segundo. Se tentasse executar a consulta quando não houvesse entrada disponível, o Azure Cosmos DB devolveria um erro HTTP 429. Qualquer um dos atuais SDK's Core (SQL) irá automaticamente rejulgar esta consulta após esperar um breve período. Os pedidos acelerados demoram mais tempo, pelo que o aumento da oferta pode melhorar a latência da consulta. Pode observar o [número total de pedidos estrangulados](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) na lâmina métrica do portal Azure.

## <a name="increase-maxconcurrency"></a>Aumentar a moeda MaxCon

Consultas paralelas funcionam consultando múltiplas divisórias em paralelo. No entanto, os dados de uma coleção individual de divisórias são recolhidos em série no que diz respeito à consulta. Assim, ajustar a moeda MaxCon ao número de divisórias tem a máxima hipótese de alcançar a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não souber o número de divisórias, pode definir a moeda MaxCon (ou MaxDegreesOfParallelismo em versões sdk mais antigas) para um número elevado, e o sistema escolhe o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau máximo de paralelismo.

## <a name="increase-maxbuffereditemcount"></a>Aumentar MaxBufferedItemCount

As consultas são concebidas para pré-obter resultados enquanto o atual lote de resultados está a ser processado pelo cliente. A pré-busca ajuda na melhoria geral da latência de uma consulta. A definição do MaxBufferedItemCount limita o número de resultados pré-rebuscados. Ao definir este valor para o número esperado de resultados devolvidos (ou um número superior), a consulta pode receber o máximo benefício da pré-busca. A definição deste valor para -1 permite ao sistema decidir automaticamente o número de itens para tampão.

## <a name="next-steps"></a>Passos seguintes
Consulte os documentos abaixo sobre como medir as RUs por consulta, obtenha estatísticas de execução para afinar as suas consultas, e muito mais:

* [Get SQL query execution metrics using .NET SDK](profile-sql-api-query.md) (Obter métricas de execução de consultas SQL através do SDK do .NET)
* [Otimização do desempenho de consulta com o Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Sugestões de desempenho para o SDK .NET](performance-tips.md)
