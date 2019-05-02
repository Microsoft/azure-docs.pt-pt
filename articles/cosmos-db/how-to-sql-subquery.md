---
title: Subconsultas SQL para o Azure Cosmos DB
description: Saiba mais sobre subconsultas SQL e de seus casos de utilização comuns no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: tisande
ms.openlocfilehash: 3ba547aea9034777fe76f3c911efd2648f6184fa
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514803"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Exemplos de subconsulta SQL para o Azure Cosmos DB

Uma subconsulta é uma consulta aninhada dentro de outra consulta. Uma subconsulta também é chamada uma consulta interna ou selecione interna e a instrução que contenha uma subconsulta normalmente é chamada de uma consulta externa.

Existem dois tipos de subconsultas:

* Correlacionado - a subconsulta correlacionado é uma subconsulta que faz referência a valores da consulta externa. A subconsulta é avaliada uma vez para cada linha, que é processada pela consulta externa.

* Não-correlacionado - subconsulta correlacionada A não é uma subconsulta que é independente da consulta externa e pode ser executado no seu próprio sem contar com a consulta externa.

> [!NOTE]
> Azure Cosmos DB suporta apenas subconsultas correlacionadas.

## <a name="types-of-subqueries"></a>Tipos de subconsultas

As subconsultas podem ser classificadas de ainda mais com base no número de linhas e colunas que elas retornam. Existem três tipos diferentes:
1.  **tabela**: Retorna múltiplas linhas e de várias colunas
2.  **Valores múltiplos**: Retorna múltiplas linhas e uma única coluna
3.  **Scalar**: Devolve uma única linha e de uma única coluna

> [!NOTE]
> Azure Cosmos DB suporta subconsultas com múltiplos valores e escalar

Consultas do Cosmos DB SQL do Azure devolvem sempre uma única coluna (um valor simples ou um documento complexo). Por conseguinte, apenas as subconsultas com múltiplos valores e escalar acima são aplicáveis no Azure Cosmos DB. Uma subconsulta de valor múltiplos só pode ser utilizada na cláusula FROM como uma expressão relacional, enquanto uma subconsulta escalar pode ser utilizada como uma expressão escalar no, SELECIONE ou cláusula WHERE ou como uma expressão relacional na cláusula FROM.


## <a name="multi-value-subqueries"></a>Subconsultas de valores múltiplos

Valores múltiplos subconsultas retornam um conjunto de documentos e são sempre utilizadas na cláusula FROM. São utilizados para:

* Otimizar as expressões de associação 
* Uma vez a avaliação de expressões caras e fazer referência a várias vezes

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

Para esta consulta, o índice corresponderá a qualquer documento que tem uma etiqueta com a nome "infant fórmula", um item de nutrient com um valor entre 0 e 10 e um item de serviço com um valor superior a 1. No entanto, a expressão de associação aqui irá efetuar o produto cruzado de todos os itens de matrizes de etiquetas, nutrients e servings para cada documento correspondente antes de qualquer filtro ser aplicado. A cláusula WHERE, em seguida, será aplicada a cadeia de identificação do filtro predicado em cada < c, t, n, s >. Por exemplo, se um documento correspondente tiver 10 itens em cada um dos três matrizes, ele poderá ser expandido para 1 x 10 x 10 x 10 (ou seja, 1000) cadeias de identificação. Usando as subconsultas aqui, pode ajudar a filtrar itens de matriz associado antes da associação com a seguinte expressão.

Esta consulta é equivalente com a descrita anteriormente, mas utiliza subconsultas:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Supondo que apenas um item da matriz de etiquetas corresponde ao filtro e cinco itens para nutrients e matrizes servings, as expressões de associação será expandido para 1 x 1 x 5 x 5 = 25 itens em vez de 1000 itens na primeira consulta.

### <a name="evaluate-once-and-reference-many-times"></a>Avaliar uma vez e a referência número de vezes

As subconsultas podem ajudar a otimizar as consultas com expressões caras, como as funções definidas pelo utilizador (UDF) ou de cadeia de caracteres complexa ou expressões aritméticas. Pode usar uma subconsulta juntamente com uma expressão de associação para avaliar a expressão de uma vez, mas fazem referência a ele muitas vezes.

A consulta seguinte executa a GetMaxNutritionValue UDF duas vezes:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Esta é uma consulta equivalente que seja executado apenas a UDF uma vez:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Tendo em conta o comportamento de vários produtos de expressões de associação, se a expressão de UDF foi avaliado como não definido, deve garantir que a expressão de associação sempre produz uma única linha, retornando um objeto da subconsulta em vez do valor diretamente.
>

Eis um exemplo semelhante, que retorna um objeto, em vez de um valor:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

A abordagem não se limita a UDFs, mas em vez disso, para qualquer expressão possivelmente dispendiosa. Por exemplo, poderíamos pegar a mesma abordagem com a média matemática de função:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>Imitar join com dados de referência externa

Muitas vezes, é necessário fazer referência a dados estáticos que raramente altera, tais como unidades de medidas ou códigos de país. Para esses dados, é preferível não duplicá-lo para cada documento. Evitar essa duplicação poupar no armazenamento e melhorar o desempenho de escrita ao manter o tamanho de documentos mais pequenos. Uma subconsulta pode ser aqui utilizada para imitar a semântica da associação interna com uma coleção de dados de referência.
Por exemplo, considere este conjunto de dados de referência.

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


A seguinte consulta imita a junção com estes dados, de modo que podemos adicionar o nome da unidade para a saída:

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

Uma expressão escalar subconsulta é uma subconsulta que é avaliada como um valor único. O valor da expressão escalar subconsulta é o valor da projeção (cláusula SELECT) da subconsulta.  Uma expressão escalar subconsulta pode ser usada em muitos locais uma expressão escalar é válida. Por exemplo, uma subconsulta escalar pode ser utilizada em qualquer expressão em ambos os SELECT e cláusulas WHERE.
No entanto, usar uma subconsulta escalar não sempre ajudar a otimizar. Por exemplo, passando uma subconsulta escalar como argumento para um sistema ou funções definidas pelo utilizador não fornece benefícios no consumo de RU ou latência.

As subconsultas escalares podem ser ainda mais classificadas como:
* Subconsultas escalares de expressão simples
* Subconsultas escalares agregadas

### <a name="simple-expression-scalar-subqueries"></a>Subconsultas escalares de expressão simples

Uma subconsulta escalar expressão simples é uma subconsulta correlacionada que tem uma cláusula SELECT que não contenha quaisquer expressões de agregação. Estes subconsultas não fornecem nenhum benefícios de otimização porque o compilador converte-os numa expressão simple maior. Não existe nenhum contexto correlacionado entre a consulta interna e externa.

Aqui estão alguns exemplos:

**Exemplo 1**

```sql
SELECT 1 AS a, 2 AS b
```

Esta consulta pode ser reescrita usando subconsulta simples expressão escalar para:

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

Esta consulta pode ser reescrita usando subconsulta simples expressão escalar para:

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

Esta consulta pode ser reescrita usando subconsulta simples expressão escalar para:

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

## <a name="aggregate-scalar-subqueries"></a>Subconsultas escalares agregadas

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

Uma subconsulta com várias expressões de função de agregação:

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

Uma consulta com uma subconsulta agregada da projeção e o filtro:

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

Uma forma mais ideal de escrever esta consulta é Junte-se sobre a subconsulta e fazer referência a subconsulta alias em ambos os SELECT e as cláusulas WHERE. Esta consulta é mais eficiente, pois precisamos de executar a subconsulta apenas dentro da instrução join e não na projeção e o filtro.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

### <a name="exists-expression"></a>Expressão EXISTS

O Azure Cosmos DB oferece suporte a expressões EXISTS. Esta é uma subconsulta agregada de escalar integrada do Azure Cosmos DB SQL API. EXISTS é uma expressão booleana que utiliza uma expressão de subconsulta e devolve VERDADEIRO se a subconsulta devolve quaisquer linhas; caso contrário, devolve FALSO.
Uma vez que a API de SQL do Azure Cosmos DB não distingue entre expressões booleanas e quaisquer outras expressões escalares, EXISTS pode ser utilizado em ambas as SELECIONE e cláusulas WHERE. Tal não acontecia T-SQL, onde é restrita ao filtro de uma expressão booleana (por exemplo, EXISTS, entre e).

Se a subconsulta EXISTS retorna um valor único que não está definido, em seguida, EXISTS será avaliada como false. Por exemplo, considere a seguinte consulta, que é avaliada como false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


No entanto, se a palavra-chave de valor na subconsulta acima for omitida, em seguida, a consulta será avaliada como true:
```sql
SELECT EXISTS (SELECT undefined) 
```

A subconsulta irá colocar a lista de valores na lista de seleção num objeto. Se a lista selecionada não tiver nenhum valor, a subconsulta irá devolver o valor único '{}"que é definido e, portanto, existe avalia como verdadeiro.

### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>Exemplo: Reconfiguração ARRAY_CONTAINS e associação como EXISTS

É um caso de utilização comuns de ARRAY_CONTAINS filtrar um documento pela existência de um item numa matriz. Neste caso, estamos a verificar se a matriz de etiquetas contém uma item com o nome laranja.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

A mesma consulta pode ser reescrita para usar EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Além disso, ARRAY_CONTAINS só é capaz de verificar se um valor é igual a qualquer elemento dentro de uma matriz. Se os filtros mais complexos são necessárias nas propriedades de matriz, uma associação é necessária.

Considere a seguinte consulta que os filtros com base nas unidades e nutritionValue propriedades na matriz: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Para cada um dos documentos na coleção, um produto cruzado é executado com seus elementos de matriz. Esta operação de associação torna possível filtrar propriedades dentro da matriz. No entanto, o consumo de RU esta consulta será significativo. Por exemplo, se 1000 documentos tiveram 100 itens em cada matriz, irá expandir a 1.000 x 100 (ou seja, 100.000) cadeias de identificação.

Usando `EXISTS` pode ajudar a evitar esse Caro entre produtos:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Neste caso, podemos filtrar em elementos de matriz dentro a subconsulta EXISTS. Se um elemento de matriz corresponde ao filtro, então estamos de projeto e `EXISTS` avalia como verdadeiro.

Podemos também alias EXISTS e referenciá-lo na projecção:

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

### <a name="array-expression"></a>Expressão de matriz

O `ARRAY` expressão pode ser utilizada para os resultados de uma consulta como uma matriz do projeto. Esta expressão só pode ser utilizada na cláusula SELECT da consulta.

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

Tal como acontece com outras subconsultas, filtra com o `ARRAY` expressão são possíveis.

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
