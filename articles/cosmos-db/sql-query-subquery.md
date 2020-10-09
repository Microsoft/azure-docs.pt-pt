---
title: Subqueries SQL para Azure Cosmos DB
description: Conheça as subqueries SQL e os seus casos de uso comum e diferentes tipos de subqueries em Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74870569"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Exemplos sub-22 sql para Azure Cosmos DB

Uma subquery é uma consulta aninhada dentro de outra consulta. Uma subquery também é chamada de consulta interna ou seleção interna. A afirmação que contém um subquery é tipicamente chamada de consulta externa.

Este artigo descreve subqueries SQL e seus casos de uso comum em Azure Cosmos DB. Todas as consultas de amostra deste doc podem ser executadas contra um conjunto de dados nutricionais que é pré-carregado no [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Tipos de subqueries

Existem dois tipos principais de subqueries:

* **Correlacionado**: Uma subconserção que faz referências a valores da consulta exterior. A subquery é avaliada uma vez para cada linha que a consulta exterior processa.
* **Não correlacionado:** uma subquery que é independente da consulta exterior. Pode ser executado por si só sem depender da consulta exterior.

> [!NOTE]
> AZure Cosmos DB suporta apenas subqueries correlacionados.

Os subqueries podem ser classificados com base no número de linhas e colunas que retornam. Existem três tipos:
* **Tabela**: Devolve várias linhas e várias colunas.
* **Multi-valor**: Devolve várias linhas e uma única coluna.
* **Scalar**: Devolve uma única linha e uma única coluna.

As consultas SQL em Azure Cosmos DB devolvem sempre uma única coluna (seja um valor simples ou um documento complexo). Portanto, apenas subqueries multi-valor e scalar são aplicáveis em Azure Cosmos DB. Pode utilizar um subquery de vários valores apenas na cláusula FROM como expressão relacional. Pode utilizar um sublote escala como expressão escalar na cláusula SELECT ou WHERE, ou como expressão relacional na cláusula FROM.

## <a name="multi-value-subqueries"></a>Subqueries multi-valor

Subqueries de vários valores devolvem um conjunto de documentos e são sempre utilizados dentro da cláusula FROM. São usados para:

* Otimização de expressões de JOIN. 
* Avaliar expressões caras uma vez e fazer referência várias vezes.

## <a name="optimize-join-expressions"></a>Otimizar expressões de JOIN

Subqueries multi-valor podem otimizar expressões JOIN empurrando predicados após cada expressão selecionada e não depois de todas as uniões cruzadas na cláusula WHERE.

Considere a consulta seguinte:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Para esta consulta, o índice corresponderá a qualquer documento que tenha uma etiqueta com o nome "fórmula infantil". É um item de nutrientes com um valor entre 0 e 10, e um item de serviço com uma quantidade superior a 1. A expressão JOIN aqui executará o produto transversal de todos os itens de tags, nutrientes e porções de conjuntos para cada documento correspondente antes de qualquer filtro ser aplicado. 

A cláusula WHERE aplicará então o predicado do filtro em cada <c, t, n, s> tuple. Por exemplo, se um documento correspondente tivesse 10 itens em cada uma das três matrizes, expandir-se-á para 1 x 10 x 10 x 10 (ou seja, 1.000) tuples. A utilização de subqueries aqui pode ajudar a filtrar itens de matriz juntas antes de se juntar à próxima expressão.

Esta consulta é equivalente à anterior, mas utiliza subqueries:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Assuma que apenas um item na matriz de tags corresponde ao filtro, e há cinco itens para nutrientes e porções. As expressões JOIN expandir-se-ão então para 1 x 1 x 5 x 5 = 25 itens, em oposição a 1.000 itens na primeira consulta.

## <a name="evaluate-once-and-reference-many-times"></a>Avaliar uma vez e fazer referência muitas vezes

Subqueries podem ajudar a otimizar consultas com expressões caras, tais como funções definidas pelo utilizador (UDFs), cordas complexas ou expressões aritméticas. Você pode usar um subquery juntamente com uma expressão JOIN para avaliar a expressão uma vez, mas referencia-la muitas vezes.

A seguinte consulta executa o UDF `GetMaxNutritionValue` duas vezes:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Aqui está uma consulta equivalente que executa o UDF apenas uma vez:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Tenha em mente o comportamento transversal das expressões JOIN. Se a expressão UDF puder avaliar para indefinido, deve certificar-se de que a expressão JOIN produz sempre uma única linha devolvendo um objeto do subquery em vez do valor diretamente.
>

Aqui está um exemplo semelhante que devolve um objeto em vez de um valor:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

A abordagem não se limita aos UDFs. Aplica-se a qualquer expressão potencialmente cara. Por exemplo, pode seguir a mesma abordagem com a função `avg` matemática:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Imitar juntar-se a dados de referência externos

Muitas vezes é necessário fazer referência a dados estáticos que raramente mudam, como unidades de medição ou códigos de país. É melhor não duplicar estes dados para cada documento. Evitar esta duplicação poupará no armazenamento e melhorará o desempenho da escrita mantendo o tamanho do documento menor. Você pode usar um sublote para imitar semântica interior com uma coleção de dados de referência.

Por exemplo, considere este conjunto de dados de referência:

| **Unidade** | **Nome**            | **Multiplicador** | **Unidade base** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanograma            | 1.00E-09       | Grama          |
| μg       | Micrograma           | 1.00E-06       | Grama          |
| mg       | Miligrama           | 1.00E-03       | Grama          |
| g        | Grama                | 1.00E+00       | Grama          |
| kg       | Quilograma            | 1.00E+03       | Grama          |
| Mg       | Megagrama            | 1.00E+06       | Grama          |
| Gg       | Gigagram            | 1.00E+09       | Grama          |
| nJ       | Nanojoule           | 1.00E-09       | Rio Joule         |
| μJ       | Microjoule          | 1.00E-06       | Rio Joule         |
| mJ       | Moinho          | 1.00E-03       | Rio Joule         |
| J        | Rio Joule               | 1.00E+00       | Rio Joule         |
| kJ       | Kilojoule           | 1.00E+03       | Rio Joule         |
| MJ       | Megajoule           | 1.00E+06       | Rio Joule         |
| GJ       | Gigajoule           | 1.00E+09       | Rio Joule         |
| cal      | Caloria             | 1.00E+00       | calorias       |
| kcal     | Caloria             | 1.00E+03       | calorias       |
| IU       | Unidades internacionais |                |               |


A seguinte consulta imita a junção destes dados de modo a adicionar o nome da unidade à saída:

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

## <a name="scalar-subqueries"></a>Subqueries de Scalar

Uma expressão subquery scalar é uma subquery que avalia a um único valor. O valor da expressão do subquery scalar é o valor da projeção (cláusula SELECT) do subquery.  Você pode usar uma expressão de subquery scalar em muitos lugares onde uma expressão escalar é válida. Por exemplo, pode utilizar um subquery escalar em qualquer expressão nas cláusulas SELECT e WHERE.

No entanto, usar um subquery escalar nem sempre ajuda a otimizar. Por exemplo, passar um subquery escalar como argumento para um sistema ou funções definidas pelo utilizador não proporciona nenhum benefício no consumo ou latência da unidade de recursos (RU).

Subqueries scalar podem ser classificados como:
* Subqueries scalar de expressão simples
* Subqueries agregados de scalar

## <a name="simple-expression-scalar-subqueries"></a>Subqueries scalar de expressão simples

Um subquery de expressão simples é um subquery correlacionado que tem uma cláusula SELECT que não contém nenhuma expressão agregada. Estas subqueries não oferecem benefícios de otimização porque o compilador os converte numa expressão simples maior. Não há nenhum contexto correlacionado entre as consultas internas e externas.

Aqui estão alguns exemplos:

**Exemplo 1**

```sql
SELECT 1 AS a, 2 AS b
```

Pode reescrever esta consulta, utilizando uma subpresa de expressão simples, para:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Ambas as consultas produzem esta saída:

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

Pode reescrever esta consulta, utilizando uma subpresa de expressão simples, para:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Saída de consulta:

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

Pode reescrever esta consulta, utilizando uma subpresa de expressão simples, para:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Saída de consulta:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Subqueries agregados de scalar

Um subquery scalar agregado é um subquery que tem uma função agregada na sua projeção ou filtro que avalia a um único valor.

**Exemplo 1:**

Aqui está um subquery com uma única expressão de função agregada na sua projeção:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Saída de consulta:

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

Aqui está um subquery com múltiplas expressões de função agregada:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Saída de consulta:

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

Aqui está uma consulta com um subquery agregado tanto na projeção como no filtro:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Saída de consulta:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Uma forma mais ideal de escrever esta consulta é juntar-se ao subquery e referenciar o pseudónimo subquery tanto nas cláusulas SELECT como WHERE. Esta consulta é mais eficiente porque você precisa executar o subloteia apenas dentro da declaração de junção, e não tanto na projeção como no filtro.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>Expressão existente

Azure Cosmos DB suporta expressões existem. Este é um subquery scalar agregado incorporado na API API API AZure Cosmos DB SQL. EXISTE é uma expressão booleana que toma uma expressão de subquery e retorna verdadeira se o subquery retorna alguma linha. Caso contrário, declara-se falso.

Como o AZure Cosmos DB SQL API não diferencia entre expressões booleanas e quaisquer outras expressões escalares, pode utilizar existe tanto nas cláusulas SELECT como WHERE. Isto é diferente do T-SQL, onde uma expressão booleana (por exemplo, EXISTE, ENTRE e IN) é restrita ao filtro.

Se o subquery exists retornar um único valor que não seja desdefinido, o EXIST avaliará o falso. Por exemplo, considere a seguinte consulta que avalia a falsa:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Se a palavra-chave VALUE no subquery anterior for omitida, a consulta avaliará de forma verdadeira:
```sql
SELECT EXISTS (SELECT undefined) 
```

O sublotei incluirá a lista de valores na lista selecionada num objeto. Se a lista selecionada não tiver valores, o subquery devolverá o valor único' {} '. Este valor é definido, pelo que existe avalia a verdade.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Exemplo: Reescrever ARRAY_CONTAINS e juntar-se como existe

Um caso de uso comum de ARRAY_CONTAINS é filtrar um documento pela existência de um item numa matriz. Neste caso, estamos a verificar se a matriz de etiquetas contém um item chamado "laranja".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Pode reescrever a mesma consulta para usar:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Além disso, ARRAY_CONTAINS só podem verificar se um valor é igual a qualquer elemento dentro de uma matriz. Se precisar de filtros mais complexos nas propriedades da matriz, utilize o JOIN.

Considere a seguinte consulta que filtra com base nas unidades e `nutritionValue` propriedades da matriz: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Para cada um dos documentos da coleção, é realizado um produto transversal com os seus elementos de matriz. Esta operação JOIN permite filtrar as propriedades dentro da matriz. No entanto, o consumo de RU desta consulta será significativo. Por exemplo, se 1.000 documentos tivessem 100 itens em cada matriz, expandir-se-á para 1.000 x 100 (ou seja, 100.000) tuples.

A utilização de EXISTÊNCIAs pode ajudar a evitar este produto transversal caro:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Neste caso, filtra-se os elementos de matriz dentro da sublogação EXISTENTE. Se um elemento de matriz corresponder ao filtro, projeta-o e existe avalia-o de verdade.

Também pode ser pseudónimo e referenciar na projeção:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Saída de consulta:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>Expressão ARRAY

Pode utilizar a expressão ARRAY para projetar os resultados de uma consulta como matriz. Pode utilizar esta expressão apenas dentro da cláusula SELECT da consulta.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Saída de consulta:

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

Tal como acontece com outras subqueries, os filtros com a expressão ARRAY são possíveis.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Saída de consulta:

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

As expressões de matriz também podem vir depois da cláusula FROM em subqueries.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Saída de consulta:

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

## <a name="next-steps"></a>Passos seguintes

- [Amostras de Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento do modelo](modeling-data.md)
