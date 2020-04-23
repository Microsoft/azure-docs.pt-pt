---
title: Problemas de consulta ao usar O Azure Cosmos DB
description: Aprenda a identificar, diagnosticar e resolver problemas com problemas de consulta do Azure Cosmos DB SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 4a8b61f3719a60af567d10f8839987e613babc9e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870460"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Problemas de consulta ao usar O Azure Cosmos DB

Este artigo percorre uma abordagem geral recomendada para consultas de resolução de problemas em Azure Cosmos DB. Embora não deva considerar os passos delineados neste artigo uma defesa completa contra potenciais problemas de consulta, incluímos as dicas de desempenho mais comuns aqui. Você deve usar este artigo como um ponto de partida para resolução de problemas consultas lentas ou caras no núcleo De Db Azure Cosmos (SQL) API. Também pode utilizar [registos](cosmosdb-monitor-resource-logs.md) de diagnóstico para identificar consultas que são lentas ou que consomem quantidades significativas de entrada.

Você pode categorizar amplamente otimizações de consulta em Azure Cosmos DB:

- Otimizações que reduzem a carga da Unidade de Pedido (RU) da consulta
- Otimizações que apenas reduzem a latência

Se reduzir a carga de RU de uma consulta, certamente também diminuirá a latência.

Este artigo fornece exemplos que pode recriar utilizando o conjunto de dados [nutricionais.](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)

## <a name="common-sdk-issues"></a>Problemas comuns do SDK

- Para obter o melhor desempenho, siga as [dicas de Desempenho.](performance-tips.md)
    > [!NOTE]
    > Para um melhor desempenho, recomendamos o processamento do anfitrião windows 64 bits. O SQL SDK inclui um ServiceInterop.dll nativo para analisar e otimizar consultas localmente. ServiceInterop.dll é suportado apenas na plataforma Windows x64. Para o Linux e outras plataformas não suportadas onde o ServiceInterop.dll não está disponível, será feita uma chamada adicional de rede para obter a consulta otimizada.
- Pode definir `MaxItemCount` um para as suas consultas, mas não pode especificar uma contagem mínima de artigos.
    - O código deve manusear qualquer `MaxItemCount`tamanho da página, de zero a .
    - O número de itens numa página será sempre inferior `MaxItemCount`ao especificado . No `MaxItemCount` entanto, é estritamente um máximo e pode haver menos resultados do que este montante.
- Por vezes, as consultas podem ter páginas vazias mesmo quando há resultados numa página futura. As razões para isto podem ser:
    - O SDK pode estar a fazer várias chamadas de rede.
    - A consulta pode estar a demorar muito tempo a recuperar os documentos.
- Todas as consultas têm um token de continuação que permitirá que a consulta continue. Certifique-se de drenar completamente a consulta. Olhe para as amostras de `while` SDK e use um loop para `FeedIterator.HasMoreResults` drenar toda a consulta.

## <a name="get-query-metrics"></a>Obtenha métricas de consulta

Quando otimizauma consulta em Azure Cosmos DB, o primeiro passo é sempre [obter as métricas](profile-sql-api-query.md) de consulta para a sua consulta. Estas métricas também estão disponíveis através do portal Azure:

[![Obtenção de](./media/troubleshoot-query-performance/obtain-query-metrics.png) métricas de consulta](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Depois de obter as métricas de consulta, compare a Contagem de Documentos Recuperado com a Contagem de Documentos de Saída para a sua consulta. Utilize esta comparação para identificar as secções relevantes para rever neste artigo.

O Conde de Documentos Recuperados é o número de documentos que a consulta precisava para carregar. O Conde de Documentos de Saída é o número de documentos necessários para os resultados da consulta. Se o Conde de Documentos Recuperados for significativamente superior ao Conde de Documentos de Saída, havia pelo menos uma parte da sua consulta que não podia usar o índice e precisava de fazer uma verificação.

Consulte as seguintes secções para compreender as otimizações de consulta relevantes para o seu cenário.

### <a name="querys-ru-charge-is-too-high"></a>A acusação de RU da Consulta é muito alta.

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Contagem de documentos recuperado é significativamente maior do que a contagem de documentos de saída

- [Incluir os caminhos necessários na política de indexação.](#include-necessary-paths-in-the-indexing-policy)

- [Entenda quais as funções do sistema que usam o índice.](#understand-which-system-functions-use-the-index)

- [Entenda quais consultas agregadas usam o índice.](#understand-which-aggregate-queries-use-the-index)

- [Modificar consultas que tenham um filtro e uma cláusula ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Otimize as expressões JOIN utilizando um subquery.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Contagem de documentos recuperado é aproximadamente igual à contagem de documentos de saída

- [Evite consultas de divisóriacruzadas cruzadas.](#avoid-cross-partition-queries)

- [Otimize consultas que tenham filtros em várias propriedades.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Modificar consultas que tenham um filtro e uma cláusula ORDER BY.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>A acusação de RU da Consulta é aceitável, mas a latência ainda é muito alta.

- [Melhorar a proximidade.](#improve-proximity)

- [Aumentar a provisão.](#increase-provisioned-throughput)

- [Aumentar a moeda MaxCon.](#increase-maxconcurrency)

- [Aumentar MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Consultas em que a contagem de documentos recuperada excede a contagem de documentos de saída

 O Conde de Documentos Recuperados é o número de documentos que a consulta precisava para carregar. O Conde de Documentos de Saída é o número de documentos necessários para os resultados da consulta. Se o Conde de Documentos Recuperados for significativamente superior ao Conde de Documentos de Saída, havia pelo menos uma parte da sua consulta que não podia usar o índice e precisava de fazer uma verificação.

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

A Contagem de Documentos Recuperados (60.951) é significativamente maior do que a Contagem de Documentos de Saída (7), pelo que esta consulta precisava de fazer uma verificação. Neste caso, a função do sistema [UPPER()](sql-query-upper.md) não utiliza o índice.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Incluir os caminhos necessários na política de indexação

A sua política de indexação `WHERE` deve abranger `ORDER BY` quaisquer `JOIN`propriedades incluídas em cláusulas, cláusulas e a maioria das funções do sistema. O caminho especificado na política de índice deve coincidir (sensível a casos) à propriedade nos documentos JSON.

Se você executar uma consulta simples no conjunto de [dados](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) nutricional, você `WHERE` observa uma carga ru muito menor quando a propriedade na cláusula é indexada:

#### <a name="original"></a>Original

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

Pode adicionar propriedades à política de indexação a qualquer momento, sem qualquer efeito na disponibilidade de escrita ou desempenho. Se adicionar uma nova propriedade ao índice, as consultas que utilizam a propriedade utilizarão imediatamente o novo índice disponível. A consulta usará o novo índice enquanto está a ser construído. Assim, os resultados da consulta podem ser inconsistentes enquanto a reconstrução do índice está em andamento. Se uma nova propriedade for indexada, as consultas que usam apenas os índices existentes não serão afetadas durante a reconstrução do índice. Pode acompanhar o progresso da [transformação do índice.](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)

### <a name="understand-which-system-functions-use-the-index"></a>Entenda quais as funções do sistema que usam o índice

Se uma expressão pode ser traduzida numa gama de valores de cadeia, pode usar o índice. Caso contrário, não pode.

Aqui está a lista de algumas funções comuns de cordas que podem usar o índice:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING (str_expr, num_expr, num_expr) = str_expr, mas apenas se o primeiro num_expr for 0

Seguem-se algumas funções comuns do sistema que não utilizam o índice e devem carregar cada documento:

| **Função do sistema**                     | **Ideias para otimização**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Utilize a Pesquisa Azure para pesquisa por texto completo.                        |
| SUPERIOR/INFERIOR                             | Em vez de utilizar a função do sistema para normalizar os dados para comparações, normalize o invólucro após a inserção. Uma consulta ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` como ```SELECT * FROM c WHERE c.name = 'BOB'```se torna. |
| Funções matemáticas (não agregados) | Se precisar calcular um valor com frequência na sua consulta, considere armazenar o valor como imóvel no seu documento JSON. |

------

Outras partes da consulta ainda podem usar o índice, mesmo que as funções do sistema não o funcionem.

### <a name="understand-which-aggregate-queries-use-the-index"></a>Entenda quais consultas agregadas usam o índice

Na maioria dos casos, as funções do sistema agregado no Azure Cosmos DB usarão o índice. No entanto, dependendo dos filtros ou cláusulas adicionais numa consulta agregada, o motor de consulta pode ser obrigado a carregar um elevado número de documentos. Normalmente, o motor de consulta aplicará primeiro filtros de igualdade e gama. Após a aplicação destes filtros, o motor de consulta pode avaliar filtros adicionais e recorrer ao carregamento de documentos restantes para calcular o agregado, se necessário.

Por exemplo, tendo em conta estas duas consultas de `CONTAINS` amostra, a consulta com um filtro de função de igualdade e de funcionamento do sistema será geralmente mais eficiente do que uma consulta com apenas um `CONTAINS` filtro de função do sistema. Isto porque o filtro de igualdade é aplicado primeiro e utiliza o `CONTAINS` índice antes que os documentos precisem de ser carregados para o filtro mais caro.

Consulta com `CONTAINS` apenas filtro - maior carga RU:

```sql
SELECT COUNT(1) FROM c WHERE CONTAINS(c.description, "spinach")
```

Consulta com filtro de `CONTAINS` igualdade e filtro - menor carga RU:

```sql
SELECT AVG(c._ts) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Aqui estão exemplos adicionais de consultas agregadas que não irão utilizar totalmente o índice:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Consultas com funções do sistema que não usam o índice

Deve consultar a [página da função do sistema](sql-query-system-functions.md) relevante para ver se utiliza o índice.

```sql
SELECT MAX(c._ts) FROM c WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Consultas agregadas com funções definidas pelo utilizador (UDF's)

```sql
SELECT AVG(c._ts) FROM c WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Consultas com GROUP BY

A acusação `GROUP BY` da RU vai aumentar à `GROUP BY` medida que a cardinalidade dos imóveis na cláusula aumenta. Neste exemplo, o motor de consulta deve `c.foodGroup = "Sausages and Luncheon Meats"` carregar todos os documentos que correspondam ao filtro, pelo que se espera que a carga RU seja elevada.

```sql
SELECT COUNT(1) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" GROUP BY c.description
```

Se planeia executar frequentemente as mesmas consultas agregadas, pode ser mais eficiente construir uma visão materializada em tempo real com o [feed de mudança de DB Do Azure Cosmos](change-feed.md) do que executar consultas individuais.

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Modificar consultas que tenham um filtro e uma cláusula ORDER BY

Embora as consultas que têm `ORDER BY` um filtro e uma cláusula normalmente utilizem um índice de gama, serão mais eficientes se puderem ser servidas a partir de um índice composto. Além de modificar a política de indexação, deve adicionar `ORDER BY` todas as propriedades do índice composto à cláusula. Esta alteração à consulta assegurará que utiliza o índice composto.  Pode observar o impacto executando uma consulta no conjunto de [dados](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) nutricional:

#### <a name="original"></a>Original

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

**Carga RU:** 44.28 RUs

#### <a name="optimized"></a>Otimizado

Consulta atualizada (inclui ambas `ORDER BY` as propriedades na cláusula):

```sql
SELECT * FROM c
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

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Otimizar as expressões JOIN utilizando uma subqueria
Subqueries de vários `JOIN` valores podem otimizar expressões empurrando predicados após cada expressão `WHERE` selecionada em vez de afinal juntas cruzadas na cláusula.

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

Para esta consulta, o índice corresponderá a qualquer documento que tenha uma etiqueta com o nome "fórmula infantil", nutriçãoValor superior a 0, e servindo uma quantidade superior a 1. A `JOIN` expressão aqui irá executar o cross-product de todos os itens de tags, nutrientes e porções arrays para cada documento correspondente antes de qualquer filtro ser aplicado. A `WHERE` cláusula aplicará então o `<c, t, n, s>` predicado do filtro em cada tuple.

Por exemplo, se um documento correspondente tiver 10 itens em cada uma das três matrizes, expandir-se-á para 1 x 10 x 10 x 10 x 10 (isto é, 1.000) tuples. O uso de subqueimas aqui pode ajudar a filtrar itens de matriz unificados antes de se juntar à próxima expressão.

Esta consulta é equivalente à anterior, mas utiliza subqueries:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Carga RU:** 22.17 RUs

Assuma que apenas um item na matriz de etiquetas corresponde ao filtro e que existem cinco itens tanto para os nutrientes como para as matrizes de porções. As `JOIN` expressões expandir-se-ão para 1 x 1 x 5 x 5 = 25 itens, em oposição a 1.000 itens na primeira consulta.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Consultas onde a contagem de documentos recuperadoé igual à contagem de documentos de saída

Se a Contagem de Documentos Recuperados for aproximadamente igual à Contagem de Documentos de Saída, a consulta não teve de digitalizar muitos documentos desnecessários. Para muitas consultas, como as que usam a palavra-chave TOP, a Contagem de Documentos Recuperados pode exceder a Contagem de Documentos de Saída por 1. Não precisas de te preocupar com isto.

### <a name="avoid-cross-partition-queries"></a>Evite consultas de partição cruzada

A Azure Cosmos DB utiliza [a partilha](partitioning-overview.md) para escalar os contentores individuais à medida que a Unidade de Pedido e as necessidades de armazenamento de dados aumentam. Cada partição física tem um índice separado e independente. Se a sua consulta tiver um filtro de igualdade que corresponda à chave de partição do seu recipiente, terá de verificar apenas o índice de partição relevante. Esta otimização reduz o número total de RUs que a consulta requer.

Se tiver um grande número de RUs aprovisionados (mais de 30.000) ou uma grande quantidade de dados armazenados (mais de aproximadamente 100 GB), provavelmente tem um recipiente suficientemente grande para ver uma redução significativa dos encargos com a Consulta ru.

Por exemplo, se criar um recipiente com a chave de divisórias AlimentosGroup, as seguintes consultas terão de verificar apenas uma única partição física:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Estas consultas também seriam otimizadas pela adição da chave de partição na consulta:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

As consultas que tenham filtros de alcance na chave da divisória, ou que não tenham filtros na chave da divisória, terão de verificar o índice de todos os divisórias físicos para obter resultados:

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Otimizar consultas que têm filtros em várias propriedades

Embora as consultas que têm filtros em várias propriedades normalmente utilizem um índice de gama, elas serão mais eficientes se puderem ser servidas a partir de um índice composto. Para pequenas quantidades de dados, esta otimização não terá um impacto significativo. Poderia, no entanto, ser útil para grandes quantidades de dados. Só é possível otimizar, no máximo, um filtro de não igualdade por índice composto. Se a sua consulta tiver vários filtros de não igualdade, escolha um deles que utilize o índice composto. O resto continuará a utilizar índices de gama. O filtro de não igualdade deve ser definido em último no índice composto. [Saiba mais sobre índices compostos.](index-policy.md#composite-indexes)

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

## <a name="optimizations-that-reduce-query-latency"></a>Otimizações que reduzem a latência da consulta

Em muitos casos, a acusação de RU pode ser aceitável quando a latência da consulta ainda é demasiado elevada. As seguintes secções dão uma visão geral das dicas para reduzir a latência da consulta. Se fizer a mesma consulta várias vezes no mesmo conjunto de dados, terá a mesma carga de RU de cada vez. Mas a latência de consulta pode variar entre execuções de consulta.

### <a name="improve-proximity"></a>Melhorar a proximidade

As consultas que são geridas de uma região diferente da conta Azure Cosmos DB terão maior latência do que se fossem executadas dentro da mesma região. Por exemplo, se estiver a executar código no seu computador de secretária, deve esperar que a latência seja dezenas ou centenas de milissegundos mais alto (ou mais) do que se a consulta provir de uma máquina virtual dentro da mesma região azure que o Azure Cosmos DB. É simples [distribuir globalmente dados no Azure Cosmos DB](distribute-data-globally.md) para garantir que pode aproximar os seus dados da sua aplicação.

### <a name="increase-provisioned-throughput"></a>Aumentar o acréscimo previsto

Em Azure Cosmos DB, a sua provisão é medida em Unidades de Pedido (RUs). Imagine que tem uma consulta que consome 5 RUs de entrada. Por exemplo, se fornecer 1.000 RUs, poderá fazer essa consulta 200 vezes por segundo. Se tentasse executar a consulta quando não houvesse entrada suficiente disponível, o Azure Cosmos DB devolveria um erro HTTP 429. Qualquer um dos API SDKs atuais (SQL) irá automaticamente rejulgar esta consulta depois de esperar por um curto período de tempo. Os pedidos acelerados demoram mais tempo, pelo que o aumento da oferta pode melhorar a latência da consulta. Pode observar o [número total de pedidos estrangulados](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) na lâmina **métrica** do portal Azure.

### <a name="increase-maxconcurrency"></a>Aumentar a moeda MaxCon

Consultas paralelas funcionam consultando múltiplas divisórias em paralelo. Mas os dados de uma coleção individual dividida são recolhidos em série no que diz respeito à consulta. Assim, se definir a MaxConcurrency para o número de divisórias, tem a melhor hipótese de conseguir a consulta mais performativa, desde que todas as outras condições do sistema permaneçam as mesmas. Se não sabe o número de divisórias, pode definir maxConcurrency (ou MaxDegreesOfParallelismo em versões SDK mais antigas) para um número elevado. O sistema escolherá o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau máximo de paralelismo.

### <a name="increase-maxbuffereditemcount"></a>Aumentar MaxBufferedItemCount

As consultas são concebidas para pré-obter resultados enquanto o atual lote de resultados está a ser processado pelo cliente. A pré-busca ajuda a melhorar a latência geral de uma consulta. Definição MaxBufferedItemCount limita o número de resultados pré-rebuscados. Se definir este valor para o número esperado de resultados devolvidos (ou um número superior), a consulta pode obter o maior benefício de pré-busca. Se definir este valor para -1, o sistema determinará automaticamente o número de itens para tampão.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para obter informações sobre como medir as RUs por consulta, obtenha estatísticas de execução para afinar as suas consultas, e muito mais:

* [Obtenha métricas de execução de consulta SQL usando .NET SDK](profile-sql-api-query.md)
* [Otimização do desempenho de consulta com o Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Sugestões de desempenho para o SDK .NET](performance-tips.md)
