---
title: Referência do operador de comparação OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para a utilização de operadores de comparação OData (eq, ne, gt, lt, ge e le) em consultas de Pesquisa Cognitiva Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fc5803f96c30ea1df362676aa8c4104bb0b69db3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934876"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Operadores de comparação OData em Azure Cognitive Search - `eq` , , , , , `ne` `gt` `lt` `ge` e `le`

A operação mais básica numa [expressão de filtro OData](query-odata-filter-orderby-syntax.md) na Pesquisa Cognitiva Azure é comparar um campo com um determinado valor. Dois tipos de comparação são possíveis: comparação de igualdade e comparação de alcance. Pode utilizar os seguintes operadores para comparar um campo com um valor constante:

Operadores para a igualdade:

- `eq`: Testar se um campo é **igual a** um valor constante
- `ne`: Testar se um campo não é **igual a** um valor constante

Operadores de gama:

- `gt`: Testar se um campo é **maior do que** um valor constante
- `lt`: Testar se um campo é **inferior a** um valor constante
- `ge`: Testar se um campo é **maior ou igual a** um valor constante
- `le`: Testar se um campo é **inferior ou igual a** um valor constante

Pode utilizar os operadores de gama em combinação com os [operadores lógicos](search-query-odata-logical-operators.md) para testar se um campo está dentro de uma determinada gama de valores. Veja os [exemplos](#examples) mais tarde neste artigo.

> [!NOTE]
> Se preferir, pode colocar o valor constante no lado esquerdo do operador e o nome de campo no lado direito. Para os operadores de gama, o significado da comparação é invertido. Por exemplo, se o valor constante estiver à esquerda, `gt` testaria se o valor constante é maior do que o campo. Também pode utilizar os operadores de comparação para comparar o resultado de uma função, `geo.distance` como, por exemplo, com um valor. Para funções booleanas tais como `search.ismatch` , comparando o resultado `true` com ou seja `false` opcional.

## <a name="syntax"></a>Syntax

O seguinte formulário EBNF ([Formulário de Backus-Naur Alargado)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática de uma expressão OData que utiliza os operadores de comparação.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

Há duas formas de expressão de comparação. A única diferença entre eles é se a constante aparece no lado esquerdo ou direito do operador. A expressão do outro lado do operador deve ser uma **chamada variável** ou de função. Uma variável pode ser um nome de campo, ou uma variável de alcance no caso de uma [expressão lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Tipos de dados para comparações

Os tipos de dados de ambos os lados de um operador de comparação devem ser compatíveis. Por exemplo, se o lado esquerdo for um campo de `Edm.DateTimeOffset` tipo, então o lado direito deve ser uma constante de data. Os tipos de dados numéricos são mais flexíveis. Pode comparar variáveis e funções de qualquer tipo numérico com constantes de qualquer outro tipo numérico, com algumas limitações, como descrito na tabela seguinte.

| Tipo variável ou função | Tipo de valor constante | Limitações |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | A comparação está sujeita a [regras especiais `NaN` para](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Constante é convertida `Edm.Double` para, resultando numa perda de precisão para valores de grande magnitude |
| `Edm.Double` | `Edm.Int32` | n/a |
| `Edm.Int64` | `Edm.Double` | Comparações `NaN` `-INF` com, ou `INF` não são permitidas |
| `Edm.Int64` | `Edm.Int64` | n/a |
| `Edm.Int64` | `Edm.Int32` | Constante é convertida `Edm.Int64` antes da comparação |
| `Edm.Int32` | `Edm.Double` | Comparações `NaN` `-INF` com, ou `INF` não são permitidas |
| `Edm.Int32` | `Edm.Int64` | n/a |
| `Edm.Int32` | `Edm.Int32` | n/a |

Para comparações que não são permitidas, como comparar um campo de tipo `Edm.Int64` com `NaN` , a Azure Cognitive Search REST API devolverá um erro "HTTP 400: Mau Pedido".

> [!IMPORTANT]
> Embora as comparações de tipo numérico sejam flexíveis, recomendamos vivamente a escrita de comparações em filtros para que o valor constante seja do mesmo tipo de dados que a variável ou função com que está a ser comparado. Isto é especialmente importante quando se misturam valores flutuantes e inteiros, onde são possíveis conversões implícitas que perdem precisão.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Casos especiais para `null` e `NaN`

Ao utilizar operadores de comparação, é importante lembrar que todos os campos de não-recolha em Azure Cognitive Search podem potencialmente ser `null` . A tabela a seguir mostra todos os resultados possíveis para uma expressão de comparação onde ambos os lados podem `null` estar:

| Operador | Resultado quando apenas o campo ou variável é `null` | Resultado quando apenas a constante é `null` | Resultado quando tanto o campo como a variável e a constante são `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Erro de Pedido Grave | HTTP 400: Erro de Pedido Grave |
| `lt` | `false` | HTTP 400: Erro de Pedido Grave | HTTP 400: Erro de Pedido Grave |
| `ge` | `false` | HTTP 400: Erro de Pedido Grave | HTTP 400: Erro de Pedido Grave |
| `le` | `false` | HTTP 400: Erro de Pedido Grave | HTTP 400: Erro de Pedido Grave |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Em resumo, `null` é igual apenas a si mesmo, e não é menor ou maior do que qualquer outro valor.

Se o seu índice tiver campos de tipo `Edm.Double` e carregar `NaN` valores para esses campos, terá de ter em conta isso ao escrever filtros. A Azure Cognitive Search implementa a norma IEEE 754 para `NaN` valores de manuseamento, e as comparações com tais valores produzem resultados não óbvios, como mostrado no quadro seguinte.

| Operador | Resultado quando pelo menos uma ópera é `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Em resumo, `NaN` não é igual a qualquer valor, incluindo a si mesmo.

### <a name="comparing-geo-spatial-data"></a>Comparação de dados geo-espaciais

Não se pode comparar diretamente um campo de tipo `Edm.GeographyPoint` com um valor constante, mas pode utilizar a `geo.distance` função. Esta função devolve um valor do `Edm.Double` tipo, para que possa compará-lo com uma constante numérica para filtrar com base na distância das constantes coordenadas geo-espaciais. Veja os [exemplos](#examples) abaixo.

### <a name="comparing-string-data"></a>Comparando dados de cordas

As cordas podem ser comparadas em filtros para partidas exatas utilizando o `eq` operador e `ne` os operadores. Estas comparações são sensíveis a casos.

## <a name="examples"></a>Exemplos

Documentos de correspondência em que o `Rating` campo é entre 3 e 5, inclusive:

```text
Rating ge 3 and Rating le 5
```

Combine documentos onde o `Location` campo esteja a menos de 2 km da latitude e longitude dada:

```text
geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0
```

Documentos de correspondência em que o `LastRenovationDate` campo é maior ou igual a 1 de janeiro de 2015, meia-noite UTC:

```text
LastRenovationDate ge 2015-01-01T00:00:00.000Z
```

Documentos de correspondência onde o `Details/Sku` campo não `null` é:

```text
Details/Sku ne null
```

Combine documentos para hotéis onde pelo menos um quarto tem o tipo "Deluxe Room", onde a cadeia do `Rooms/Type` campo combina exatamente com o filtro:

```text
Rooms/any(room: room/Type eq 'Deluxe Room')
```

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)