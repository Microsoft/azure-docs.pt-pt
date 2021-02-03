---
title: Resolver problemas de consultas ao utilizar o Azure Cosmos DB
description: Saiba como identificar, diagnosticar e resolver problemas Azure Cosmos DB SQL questões de consulta.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/02/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 6875fc53a651b89fcfe88d3217ff86bd21204f6c
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524314"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Resolver problemas de consultas ao utilizar o Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo percorre uma abordagem geral recomendada para consultas de resolução de problemas em Azure Cosmos DB. Embora não deva considerar os passos descritos neste artigo como uma defesa completa contra potenciais problemas de consulta, incluímos as dicas de desempenho mais comuns aqui. Deverá utilizar este artigo como ponto de partida para resolver problemas de consultas lentas ou dispendiosas na API do core (SQL) do Azure Cosmos DB. Também pode utilizar os [registos de diagnóstico](cosmosdb-monitor-resource-logs.md) para identificar consultas que são lentas ou que consomem um débito significativo. Se estiver a usar a API da Azure Cosmos para a MongoDB, deve utilizar [a API da Azure Cosmos para o guia de resolução de problemas da MongoDB](mongodb-troubleshoot-query.md)

As otimizações de consultas em Azure Cosmos DB são amplamente categorizadas da seguinte forma:

- Otimizações que reduzem a carga da Unidade de Pedido (RU) da consulta
- Otimizações que apenas reduzem a latência

Se reduzir a carga RU de uma consulta, você normalmente diminuirá a latência também.

Este artigo fornece exemplos que pode recriar utilizando o conjunto de [dados nutricionais](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

## <a name="common-sdk-issues"></a>Questões SDK comuns

Antes de ler este guia, é útil considerar questões SDK comuns que não estejam relacionadas com o motor de consulta.

- Siga estas [dicas de desempenho da SDK](performance-tips.md).
    - [.NET SDK guia de resolução de problemas](troubleshoot-dot-net-sdk.md)
    - [Guia de resolução de problemas java SDK](troubleshoot-java-sdk-v4-sql.md)
- O SDK permite definir um `MaxItemCount` para as suas consultas, mas não pode especificar uma contagem mínima de item.
    - O código deve lidar com qualquer tamanho de página, de zero a. `MaxItemCount` .
- Por vezes, as consultas podem ter páginas vazias mesmo quando há resultados numa página futura. As razões para tal podem ser:
    - O SDK pode estar a fazer várias chamadas de rede.
    - A consulta pode demorar muito tempo a recuperar os documentos.
- Todas as consultas têm um token de continuação que permitirá que a consulta continue. Certifique-se de drenar completamente a consulta. Saiba mais sobre [como lidar com várias páginas de resultados](sql-query-pagination.md#handling-multiple-pages-of-results)

## <a name="get-query-metrics"></a>Obtenha métricas de consulta

Quando otimiza uma consulta em Azure Cosmos DB, o primeiro passo é sempre [obter as métricas de consulta](profile-sql-api-query.md) para a sua consulta. Estas métricas também estão disponíveis através do portal Azure. Uma vez executada a sua consulta no Data Explorer, as métricas de consulta são visíveis ao lado do **separador Resultados:**

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="Obtenção de métricas de consulta" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

Depois de obter as métricas de consulta, compare a **contagem de documentos recuperado** com a contagem de **documentos de saída** para a sua consulta. Utilize esta comparação para identificar as secções relevantes para rever neste artigo.

A **Contagem de Documentos Recuperados** é o número de documentos que o motor de consulta precisava de carregar. A **Contagem de Documentos** de Saída é o número de documentos necessários para os resultados da consulta. Se o **Conde de Documentos Recuperados** for significativamente maior do que o Contagem de Documentos de **Saída,** houve pelo menos uma parte da sua consulta que não foi capaz de usar um índice e precisava de fazer uma verificação.

Consulte as seguintes secções para entender as otimizações de consultas relevantes para o seu cenário.

### <a name="querys-ru-charge-is-too-high"></a>A carga ru da consulta é muito alta

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>A contagem de documentos recuperados é significativamente maior do que a contagem de documentos de saída

- [Incluir os caminhos necessários na política de indexação.](#include-necessary-paths-in-the-indexing-policy)

- [Compreenda quais as funções do sistema que usam o índice.](#understand-which-system-functions-use-the-index)

- [Melhorar a execução da função do sistema de cordas.](#improve-string-system-function-execution)

- [Entenda quais consultas agregadas usam o índice.](#understand-which-aggregate-queries-use-the-index)

- [Otimize as consultas que tenham um filtro e uma cláusula ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Otimize as expressões JOIN utilizando um sublote.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>A contagem de documentos recuperados é aproximadamente igual à contagem de documentos de saída

- [Minimize as consultas de partição cruzada.](#minimize-cross-partition-queries)

- [Otimize consultas que tenham filtros em várias propriedades.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Otimize as consultas que tenham um filtro e uma cláusula ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>A taxa de RU da Consulta é aceitável, mas a latência ainda é muito alta

- [Melhorar a proximidade.](#improve-proximity)

- [Aumentar a produção a provisionada.](#increase-provisioned-throughput)

- [Aumentar a MaxConcurrency.](#increase-maxconcurrency)

- [Aumentar MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Consultas onde contagem de documentos recuperados excede a contagem de documentos de saída

 A **Contagem de Documentos Recuperados** é o número de documentos que o motor de consulta precisava de carregar. A **Contagem de Documentos** de Saída é o número de documentos devolvidos pela consulta. Se o **Conde de Documentos Recuperados** for significativamente maior do que o Contagem de Documentos de **Saída,** houve pelo menos uma parte da sua consulta que não foi capaz de usar um índice e precisava de fazer uma verificação.

Aqui está um exemplo de consulta de digitalização que não foi inteiramente servido pelo índice:

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

O **Conde de Documentos Recuperados** (60.951) é significativamente superior ao **Contagem de Documentos** de Saída (7), o que implica que esta consulta resultou numa verificação de documentos. Neste caso, a função do sistema [UPPER()](sql-query-upper.md) não utiliza um índice.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Incluir os caminhos necessários na política de indexação

A sua política de indexação deve abranger quaisquer propriedades incluídas em `WHERE` `ORDER BY` cláusulas, cláusulas `JOIN` e a maioria das funções do sistema. Os caminhos desejados especificados na política de índice devem corresponder às propriedades nos documentos JSON.

> [!NOTE]
> Propriedades na política de indexação DB da Azure Cosmos são sensíveis a casos

Se executar a seguinte consulta simples sobre o conjunto de dados [nutricionais,](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) observará uma carga RU muito mais baixa quando a propriedade na `WHERE` cláusula estiver indexada:

#### <a name="original"></a>Original

Consulta:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
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

**Carga RU:** 409.51 RUs

#### <a name="optimized"></a>Otimizado

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

**Carga RU:** 2.98 RUs

Pode adicionar propriedades à política de indexação a qualquer momento, sem qualquer efeito na disponibilidade de escrita ou leitura. Pode [acompanhar o progresso da transformação do índice.](./how-to-manage-indexing-policy.md#dotnet-sdk)

### <a name="understand-which-system-functions-use-the-index"></a>Entenda quais as funções do sistema que usam o índice

A maioria das funções do sistema usam índices. Aqui está uma lista de algumas funções de cordas comuns que usam índices:

- StartsWith
- Contains
- RegexMatch
- Esquerda
- Substring - mas só se o primeiro num_expr for 0

Seguem-se algumas funções comuns do sistema que não utilizam o índice e devem carregar cada documento quando utilizado numa `WHERE` cláusula:

| **Função do sistema**                     | **Ideias para otimização**             |
| --------------------------------------- |------------------------------------------------------------ |
| Superior/Inferior                         | Em vez de utilizar a função do sistema para normalizar os dados para comparações, normalize o invólucro após a inserção. Uma consulta como ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` se ```SELECT * FROM c WHERE c.name = 'BOB'``` torna. |
| GetCurrentDateTime/GetCurrentTimestamp/GetCurrentTicks | Calcular o tempo atual antes da execução de consulta e usar o valor da cadeia na `WHERE` cláusula. |
| Funções matemáticas (não agregados) | Se precisar de calcular um valor frequentemente na sua consulta, considere armazenar o valor como um imóvel no seu documento JSON. |

Quando usado na cláusula, as `SELECT` funções ineficientes do sistema não afetarão a forma como as consultas podem usar índices.

### <a name="improve-string-system-function-execution"></a>Melhorar a execução da função do sistema de cordas

Para algumas funções do sistema que utilizam índices, pode melhorar a execução de consultas adicionando uma `ORDER BY` cláusula à consulta. 

Mais especificamente, qualquer função do sistema cuja carga RU aumenta à medida que a cardinalidade do imóvel aumenta pode beneficiar de ter `ORDER BY` na consulta. Estas consultas fazem uma verificação de índice, de modo que ter os resultados de consulta ordenados pode tornar a consulta mais eficiente.

Esta otimização pode melhorar a execução para as seguintes funções do sistema:

- Começacom (onde caso-insensível = verdadeiro)
- StringEquals (onde caso-insensível = verdadeiro)
- Contains
- RegexMatch
- EndsWith

Por exemplo, considere a consulta abaixo com `CONTAINS` . `CONTAINS` usará índices, mas às vezes, mesmo depois de adicionar o índice relevante, ainda pode observar uma carga RU muito alta ao executar a consulta abaixo.

Consulta original:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

Pode melhorar a execução de consultas `ORDER BY` adicionando:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

A mesma otimização pode ajudar em consultas com filtros adicionais. Neste caso, o melhor é também adicionar propriedades com filtros de igualdade à `ORDER BY` cláusula.

Consulta original:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

Pode melhorar a execução de consultas adicionando `ORDER BY` e um índice [composto](index-policy.md#composite-indexes) para (c.name, c.town):

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>Entenda quais consultas agregadas usam o índice

Na maioria dos casos, as funções agregadas do sistema em Azure Cosmos DB usarão o índice. No entanto, dependendo dos filtros ou cláusulas adicionais numa consulta agregada, o motor de consulta pode ser necessário para carregar um elevado número de documentos. Normalmente, o motor de consulta aplicará primeiro a igualdade e os filtros de alcance. Após a aplicação destes filtros, o motor de consulta pode avaliar filtros adicionais e recorrer ao carregamento de documentos restantes para calcular o agregado, se necessário.

Por exemplo, dadas estas duas consultas de amostra, a consulta com um filtro de igualdade e `CONTAINS` função do sistema será geralmente mais eficiente do que uma consulta com apenas um `CONTAINS` filtro de função do sistema. Isto porque o filtro para a igualdade é aplicado primeiro e utiliza o índice antes de os documentos terem de ser carregados para o filtro mais `CONTAINS` caro.

Consulta com apenas `CONTAINS` filtro - carga RU mais alta:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Consulta com filtro de igualdade e `CONTAINS` filtro - menor carga RU:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Aqui estão exemplos adicionais de consultas agregadas que não utilizarão totalmente o índice:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Consultas com funções do sistema que não usam o índice

Deve consultar a [página da função do sistema](sql-query-system-functions.md) relevante para ver se utiliza o índice.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Consultas agregadas com funções definidas pelo utilizador (UDF's)

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Consultas com GROUP BY

A carga ru de consultas `GROUP BY` com vai aumentar à medida que a cardinalidade dos imóveis na cláusula `GROUP BY` aumenta. Na consulta abaixo, por exemplo, a carga RU da consulta aumentará à medida que o número de descrições únicas aumenta.

A carga RU de uma função agregada com uma `GROUP BY` cláusula será superior à taxa RU de uma função agregada por si só. Neste exemplo, o motor de consulta deve carregar todos os documentos que correspondam ao `c.foodGroup = "Sausages and Luncheon Meats"` filtro de modo a que a carga RU seja elevada.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Se planeia executar frequentemente as mesmas consultas agregadas, pode ser mais eficiente construir uma visão materializada em tempo real com o [feed de mudança de Azure Cosmos do](change-feed.md) que executar consultas individuais.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Otimize consultas que tenham um filtro e uma cláusula ORDER BY

Embora as consultas que tenham um filtro e uma `ORDER BY` cláusula utilizem normalmente um índice de gama, serão mais eficientes se puderem ser servidas a partir de um índice composto. Além de modificar a política de indexação, deve adicionar todas as propriedades do índice composto à `ORDER BY` cláusula. Esta alteração à consulta assegurará a utilização do índice composto.  Pode observar o impacto executando uma consulta no conjunto de dados [nutricionais:](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

#### <a name="original"></a>Original

Consulta:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
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

**Carga RU:** 44.28 RUs

#### <a name="optimized"></a>Otimizado

Consulta atualizada (inclui ambas as propriedades na `ORDER BY` cláusula):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
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

**Carga RU:** 8.86 RUs

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Otimize as expressões join usando um subquery

Subqueries multi-valor podem otimizar `JOIN` expressões empurrando predicados após cada expressão selecionada e não depois de todas as junções cruzadas na `WHERE` cláusula.

Considere esta consulta:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Carga RU:** 167.62 RUs

Para esta consulta, o índice corresponderá a qualquer documento que tenha uma etiqueta com o nome , superior a `infant formula` `nutritionValue` 0, e `amount` superior a 1. A `JOIN` expressão aqui irá executar o produto transversal de todos os itens de tags, nutrientes e porções de conjuntos para cada documento correspondente antes de qualquer filtro ser aplicado. A `WHERE` cláusula aplicará então o predicado do filtro em cada `<c, t, n, s>` tuple.

Por exemplo, se um documento correspondente tiver 10 itens em cada uma das três matrizes, expandir-se-á para 1 x 10 x 10 x 10 (ou seja, 1.000) tuples. O uso de subqueries aqui pode ajudar a filtrar itens de matriz juntas antes de se juntar à próxima expressão.

Esta consulta é equivalente à anterior, mas utiliza subqueries:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Carga RU:** 22.17 RUs

Assuma que apenas um item na matriz de tags corresponde ao filtro e que existem cinco itens tanto para os nutrientes como para as matrizes de porções. As `JOIN` expressões expandir-se-ão para 1 x 1 x 5 x 5 = 25 itens, em oposição a 1.000 itens na primeira consulta.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Consultas onde a contagem de documentos recuperados é igual à contagem de documentos de saída

Se a **contagem de documentos recuperados** for aproximadamente igual à **contagem de documentos de saída,** o motor de consulta não teve de digitalizar muitos documentos desnecessários. Para muitas consultas, como as que usam a `TOP` palavra-chave, a **contagem de documentos recuperados** pode exceder a **contagem de documentos de saída** por 1. Não precisas de te preocupar com isto.

### <a name="minimize-cross-partition-queries"></a>Minimizar consultas de partição cruzada

A Azure Cosmos DB utiliza [divisórias](partitioning-overview.md) para escalar contentores individuais à medida que a Unidade de Pedido e as necessidades de armazenamento de dados aumentam. Cada divisória física tem um índice separado e independente. Se a sua consulta tiver um filtro de igualdade que corresponda à chave de partição do seu recipiente, terá de verificar apenas o índice de partição relevante. Esta otimização reduz o número total de RUs que a consulta requer.

Se tiver um grande número de RUs provisões (mais de 30.000) ou uma grande quantidade de dados armazenados (mais de 100 GB), provavelmente tem um recipiente suficientemente grande para ver uma redução significativa nos encargos ru de consulta.

Por exemplo, se criar um recipiente com a chave de partição do Grupo, as seguintes consultas terão de verificar apenas uma única partição física:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

As consultas que tenham um `IN` filtro com a chave de partição apenas verificarão as divisórias físicas relevantes e não irão "fan-out":

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

As consultas que tenham filtros de alcance na chave de partição, ou que não tenham filtros na tecla de partição, terão de "abanar" e verificar os resultados de cada índice de partição física:

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Otimizar consultas que tenham filtros em várias propriedades

Embora as consultas que tenham filtros em várias propriedades utilizem normalmente um índice de gama, serão mais eficientes se puderem ser servidas a partir de um índice composto. Para pequenas quantidades de dados, esta otimização não terá um impacto significativo. Pode ser útil, no entanto, para grandes quantidades de dados. Só é possível otimizar, no máximo, um filtro de não igualdade por índice composto. Se a sua consulta tiver vários filtros de não igualdade, escolha um deles que utilizará o índice composto. O resto continuará a usar índices de gama. O filtro de não igualdade deve ser definido por último no índice composto. [Saiba mais sobre índices compostos.](index-policy.md#composite-indexes)

Aqui estão alguns exemplos de consultas que poderiam ser otimizadas com um índice composto:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
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

## <a name="optimizations-that-reduce-query-latency"></a>Otimizações que reduzem a latência da consulta

Em muitos casos, a taxa RU pode ser aceitável quando a latência da consulta ainda é demasiado elevada. As seguintes secções dão uma visão geral das dicas para reduzir a latência da consulta. Se executar a mesma consulta várias vezes no mesmo conjunto de dados, normalmente terá a mesma carga RU de cada vez. Mas a latência da consulta pode variar entre execuções de consultas.

### <a name="improve-proximity"></a>Melhorar a proximidade

As consultas que são geridas a partir de uma região diferente da conta DB do Azure Cosmos terão maior latência do que se fossem geridas dentro da mesma região. Por exemplo, se estiver a executar código no seu computador de secretária, deve esperar que a latência seja dezenas ou centenas de milissegundos mais elevados (ou mais) do que se a consulta viesse de uma máquina virtual dentro da mesma região de Azure Cosmos DB. É simples [distribuir globalmente dados em Azure Cosmos DB](distribute-data-globally.md) para garantir que pode aproximar os seus dados da sua app.

### <a name="increase-provisioned-throughput"></a>Aumento da produção a provisionada

Em Azure Cosmos DB, a sua produção provisida é medida em Unidades de Pedido (RUs). Imagine que tem uma consulta que consome 5 RUs de produção. Por exemplo, se você fornece 1.000 RUs, você seria capaz de executar essa consulta 200 vezes por segundo. Se tentasse executar a consulta quando não havia produção suficiente disponível, a Azure Cosmos DB devolveria um erro HTTP 429. Qualquer um dos SDKs API do Núcleo atual (SQL) tentará automaticamente esta consulta depois de esperar por um curto período de tempo. Os pedidos de aceleração demoram mais tempo, pelo que o aumento da produção a provisionada pode melhorar a latência da consulta. Pode observar o [número total de pedidos de aceleração](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) na lâmina **métrica** do portal Azure.

### <a name="increase-maxconcurrency"></a>Aumentar a MaxConcurrency

Consultas paralelas funcionam consultando várias divisórias em paralelo. Mas os dados de uma recolha individual de divisórias são recolhidos em série no que diz respeito à consulta. Assim, se definir MaxConcurrency para o número de divisórias, você tem a melhor chance de alcançar a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não souber o número de divisórias, pode definir MaxConcurrency (ou MaxDegreesOfParallelism em versões SDK mais antigas) para um número elevado. O sistema escolherá o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau máximo de paralelismo.

### <a name="increase-maxbuffereditemcount"></a>Aumentar MaxBufferedItemCount

As consultas são projetadas para obter resultados pré-obtendos enquanto o lote atual de resultados está sendo processado pelo cliente. A pré-busca ajuda a melhorar a latência geral de uma consulta. A definição de MaxBufferedItemCount limita o número de resultados pré-recedidos. Se definir este valor para o número esperado de resultados devolvidos (ou um número mais elevado), a consulta pode obter o maior benefício da pré-obtenção. Se definir este valor para -1, o sistema determinará automaticamente o número de itens a tampão.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para obter informações sobre como medir RUs por consulta, obter estatísticas de execução para afinar as suas consultas, e muito mais:

* [Obtenha métricas de execução de consulta SQL usando .NET SDK](profile-sql-api-query.md)
* [Otimização do desempenho de consulta com o Azure Cosmos DB](./sql-api-query-metrics.md)
* [Sugestões de desempenho para o SDK .NET](performance-tips.md)
* [Dicas de desempenho para Java v4 SDK](performance-tips-java-sdk-v4-sql.md)