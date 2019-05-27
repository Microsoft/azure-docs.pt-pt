---
title: Subconsultas SQL para o Azure Cosmos DB
description: Saiba mais sobre subconsultas SQL e de seus casos de utilização comuns no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: tisande
ms.openlocfilehash: 48d0c7a022ff568582637aac36a377ca022a413c
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977359"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Exemplos de subconsulta SQL para o Azure Cosmos DB

Uma subconsulta é uma consulta aninhada dentro de outra consulta. Uma subconsulta também é chamada uma consulta interna ou selecione interna. A instrução que contenha uma subconsulta normalmente é chamada de uma consulta externa.

Este artigo descreve as subconsultas SQL e de seus casos de utilização comuns no Azure Cosmos DB.

## <a name="types-of-subqueries"></a>Tipos de subconsultas

Existem dois tipos principais de subconsultas:

* **Correlacionado**: Uma subconsulta que faz referência a valores da consulta externa. A subconsulta é avaliada uma vez para cada linha que processa a consulta externa.
* **Não correlacionado**: Uma subconsulta que é independente da consulta externa. Pode ser executado em seu próprio sem depender do externo consulta.

> [!NOTE]
> Azure Cosmos DB suporta apenas as subconsultas correlacionadas.

As subconsultas podem ser classificadas de ainda mais com base no número de linhas e colunas que elas retornam. Existem três tipos:
* **tabela**: Devolve a várias linhas e de várias colunas.
* **Valores múltiplos**: Devolve a várias linhas e uma única coluna.
* **Scalar**: Devolve uma única linha e de uma única coluna.

Consultas SQL no Azure Cosmos DB sempre retornam uma única coluna (um valor simples ou um documento complexo). Por conseguinte, apenas as subconsultas com múltiplos valores e escalares são aplicáveis no Azure Cosmos DB. Pode usar uma subconsulta de valor múltiplos apenas na cláusula FROM como uma expressão relacional. Pode usar uma subconsulta escalar como uma expressão escalar no, SELECIONE ou cláusula WHERE ou como uma expressão relacional na cláusula FROM.


## <a name="multi-value-subqueries"></a>Subconsultas de valores múltiplos

Valores múltiplos subconsultas retornam um conjunto de documentos e são sempre utilizadas na cláusula FROM. Eles são usados para:

* Otimizar as expressões de associação. 
* Uma vez a avaliação de expressões caras e fazer referência a várias vezes.

### <a name="optimize-join-expressions"></a>Otimizar as expressões de associação

Valores múltiplos subconsultas podem otimizar as expressões de associação ao enviar predicados após cada expressão select-muitos, em vez de após associações de entre todos os na cláusula WHERE.

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

Para esta consulta, o índice corresponderá a qualquer documento que tem uma etiqueta com a nome "infant fórmula." É um item nutrient com um valor entre 0 e 10 e um item de serviço com um valor superior a 1. A expressão de associação aqui executará o produto cruzado de todos os itens de matrizes de etiquetas, nutrients e servings para cada documento correspondente antes de qualquer filtro ser aplicado. 

A cláusula WHERE, em seguida, será aplicada a cadeia de identificação do filtro predicado em cada < c, t, n, s >. Por exemplo, se um documento correspondente tiver 10 itens em cada um dos três matrizes, ele poderá ser expandido para 1 x 10 x 10 x 10 (ou seja, 1000) cadeias de identificação. Usando as subconsultas aqui pode ajudar em filtrar itens de matriz associado antes da associação com a seguinte expressão.

Esta consulta é equivalente à anterior, mas usa subconsultas:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Partem do princípio de que apenas um item da matriz de etiquetas corresponde ao filtro, e existem cinco itens para matrizes nutrients e servings. As expressões de associação, em seguida, serão expandido para 1 x 1 x 5 x 5 = 25 itens, em vez de 1000 itens na primeira consulta.

### <a name="evaluate-once-and-reference-many-times"></a>Avaliar uma vez e a referência número de vezes

As subconsultas podem ajudar a otimizar as consultas com expressões caras, como as funções definidas pelo utilizador (UDFs), cadeias de caracteres complexas ou expressões aritméticas. Pode usar uma subconsulta juntamente com uma expressão de associação para avaliar a expressão de uma vez, mas fazem referência a ele muitas vezes.

A consulta seguinte executa a UDF `GetMaxNutritionValue` duas vezes:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Esta é uma consulta equivalente que executa a UDF apenas uma vez:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Tenha em atenção o comportamento de vários produtos de expressões de associação. Se a expressão de UDF pode avaliar a indefinido, deve certificar-se de que a expressão de associação sempre produz uma única linha, retornando um objeto da subconsulta em vez do valor diretamente.
>

Eis um exemplo semelhante, que retorna um objeto, em vez de um valor:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

A abordagem não está limitada a UDFs. Ele se aplica a qualquer expressão possivelmente dispendiosa. Por exemplo, pode usar a mesma abordagem com a função de matemática `avg`:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>Imitar join com dados de referência externa

Muitas vezes, poderá ter fazer referência a dados estáticos que raramente altera, tais como unidades de medida ou códigos de país. É melhor não duplicar esses dados para cada documento. Evitar essa duplicação poupar no armazenamento e melhorar o desempenho de escrita ao manter o tamanho de documentos mais pequenos. Pode usar uma subconsulta para imitar a semântica da associação interna com uma coleção de dados de referência.

Por exemplo, considere este conjunto de dados de referência:

| **Unit** | **Nome**            | **Multiplicador** | **Unidade base** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1.00E-09       | Grama          |
| µg       | Microgram           | 1.00E-06       | Grama          |
| mg       | Milligram           | 1.00E-03       | Grama          |
| g        | Grama                | 1.00E + 00       | Grama          |
| kg       | Kilogram            | 1.00E + 03       | Grama          |
| Mg       | Megagram            | 1.00E + 06       | Grama          |
| Gg       | Gigagram            | 1.00E + 09       | Grama          |
| nJ       | Nanojoule           | 1.00E-09       | Joule         |
| µJ       | Microjoule          | 1.00E-06       | Joule         |
| mJ       | Millijoule          | 1.00E-03       | Joule         |
| D        | Joule               | 1.00E + 00       | Joule         |
| kJ       | Kilojoule           | 1.00E + 03       | Joule         |
| MJ       | Megajoule           | 1.00E + 06       | Joule         |
| GJ       | Gigajoule           | 1.00E + 09       | Joule         |
| cal      | Calorie             | 1.00E + 00       | calorie       |
| kcal     | Calorie             | 1.00E + 03       | calorie       |
| IU       | Unidades de internacional |                |               |


A seguinte consulta imita a junção com estes dados para que adicionar o nome da unidade para a saída:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Subconsultas escalares

Uma expressão escalar subconsulta é uma subconsulta que é avaliada como um valor único. O valor da expressão escalar subconsulta é o valor da projeção (cláusula SELECT) da subconsulta.  Pode usar uma expressão escalar subconsulta em muitos lugares em que uma expressão escalar é válida. Por exemplo, pode usar uma subconsulta escalar em qualquer expressão em ambos os SELECT e cláusulas WHERE.

Usar uma subconsulta escalar sempre não ajuda otimizar, no entanto. Por exemplo, passando uma subconsulta escalar como argumento para um sistema ou funções definidas pelo utilizador não fornece benefícios no consumo de unidade (UR) de recursos ou latência.

As subconsultas escalares podem ser ainda mais classificadas como:
* Subconsultas escalares de expressão simples
* Subconsultas escalares agregadas

### <a name="simple-expression-scalar-subqueries"></a>Subconsultas escalares de expressão simples

Uma subconsulta escalar expressão simples é uma subconsulta correlacionada com uma cláusula SELECT que não contém quaisquer expressões de agregação. Estes subconsultas não fornecem nenhum benefícios de otimização porque o compilador converte-os numa expressão simple maior. Não existe nenhum contexto correlacionado entre as consultas internas e externas.

Aqui estão alguns exemplos:

**Exemplo 1**

```sql
SELECT 1 AS a, 2 AS b
```

Pode reescrever essa consulta, ao utilizar uma subconsulta simples expressão escalar, para:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

As duas consultas produzem esta saída:

```json
[
  { "a": 1, "b": 2 }
]
```

**Exemplo 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Pode reescrever essa consulta, ao utilizar uma subconsulta simples expressão escalar, para:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Resultado da consulta:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Exemplo 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Pode reescrever essa consulta, ao utilizar uma subconsulta simples expressão escalar, para:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Resultado da consulta:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Subconsultas escalares agregadas

Uma subconsulta escalar agregada é uma subconsulta que tem uma função de agregação na sua projecção ou um filtro que é avaliada como um valor único.

**Exemplo 1:**

Aqui está uma subconsulta com uma expressão de função de agregação única no seu projeção:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Resultado da consulta:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Exemplo 2**

Aqui está uma subconsulta com várias expressões de função de agregação:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Resultado da consulta:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Exemplo 3**

Eis uma consulta com uma subconsulta agregada da projeção e o filtro:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Resultado da consulta:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Uma forma mais ideal de escrever esta consulta é Junte-se sobre a subconsulta e fazer referência a subconsulta alias em ambos os SELECT e as cláusulas WHERE. Esta consulta é mais eficiente porque tem de executar a subconsulta apenas dentro da instrução join e não na projeção e o filtro.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

#### <a name="exists-expression"></a>Expressão EXISTS

O Azure Cosmos DB oferece suporte a expressões EXISTS. Esta é uma subconsulta agregada de escalar integrada do Azure Cosmos DB SQL API. EXISTS é uma expressão booleana que utiliza uma expressão de subconsulta e devolve VERDADEIRO se a subconsulta devolve quaisquer linhas. Caso contrário, devolve FALSO.

Uma vez que a API de SQL do Azure Cosmos DB não distingue entre expressões booleanas e quaisquer outras expressões escalares, pode usar EXISTS em ambas as SELECIONE e cláusulas WHERE. Tal não acontecia T-SQL, onde é restrita ao filtro de uma expressão booleana (por exemplo, EXISTS, entre e).

Se a subconsulta EXISTS retornar um valor único que indefinido, existe será avaliada como false. Por exemplo, considere a seguinte consulta, que é avaliada como false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Se a palavra-chave de valor na subconsulta anterior for omitida, a consulta será avaliado como true:
```sql
SELECT EXISTS (SELECT undefined) 
```

A subconsulta irá colocar a lista de valores na lista selecionada num objeto. Se a lista selecionada não tiver nenhum valor, a subconsulta irá devolver o valor único '{}'. Este valor é definido, pelo que EXISTS avalia como verdadeiro.

#### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>Exemplo: Reconfiguração ARRAY_CONTAINS e associação como EXISTS

É um caso de utilização comuns de ARRAY_CONTAINS filtrar um documento pela existência de um item numa matriz. Neste caso, estamos está a verificar se a matriz de etiquetas contém um item com o nome "laranja".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Pode reescrever a mesma consulta para utilizar EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Além disso, ARRAY_CONTAINS só pode verificar se um valor é igual a qualquer elemento dentro de uma matriz. Se precisar de filtros mais complexos nas propriedades de matriz, utilize a associação.

Considere a seguinte consulta que filtra com base nas unidades e `nutritionValue` propriedades na matriz: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Para cada um dos documentos na coleção, um produto cruzado é executado com seus elementos de matriz. Esta operação de associação torna possível filtrar propriedades dentro da matriz. No entanto, o consumo de RU esta consulta será significativo. Por exemplo, se 1000 documentos tiveram 100 itens em cada matriz, irá expandir a 1.000 x 100 (ou seja, 100.000) cadeias de identificação.

Usar EXISTS pode ajudar a evitar esse Caro entre produtos:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Neste caso, filtrar em elementos de matriz dentro a subconsulta EXISTS. Se um elemento de matriz corresponde ao filtro, em seguida, o projeto e EXISTS avalia como verdadeiro.

Também pode alias EXISTS e referenciá-lo na projecção:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Resultado da consulta:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

#### <a name="array-expression"></a>Expressão de matriz

Pode utilizar a expressão de matriz para projetar os resultados de uma consulta como uma matriz. Pode utilizar esta expressão apenas dentro da cláusula SELECT da consulta.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Resultado da consulta:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Tal como acontece com outras subconsultas, filtros com a expressão de matriz são possíveis.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Resultado da consulta:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Expressões de matriz podem também resultar após a cláusula FROM em subconsultas.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Resultado da consulta:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Passos Seguintes

- [Exemplos de consulta SQL](how-to-sql-query.md)
- [Exemplos do Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dados de documento do modelo](modeling-data.md)
