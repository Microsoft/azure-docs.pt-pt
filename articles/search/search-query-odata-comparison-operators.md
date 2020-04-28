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
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113219"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>OData comparando operadores `eq`em `ne` `gt`Pesquisa `lt` `ge`Cognitiva Azure - , , ,`le`

A operação mais básica numa expressão de [filtro OData](query-odata-filter-orderby-syntax.md) em Pesquisa Cognitiva Azure é comparar um campo com um determinado valor. Dois tipos de comparação são possíveis: comparação de igualdade e comparação de faixas. Pode utilizar os seguintes operadores para comparar um campo com um valor constante:

Operadores de igualdade:

- `eq`: Testar se um campo é **igual a** um valor constante
- `ne`: Testar se um campo não é **igual a** um valor constante

Operadores de gama:

- `gt`: Testar se um campo é **maior do que** um valor constante
- `lt`: Testar se um campo é **inferior** a um valor constante
- `ge`: Testar se um campo é **maior ou igual a** um valor constante
- `le`: Testar se um campo é **inferior ou igual a** um valor constante

Pode utilizar os operadores de gama em combinação com os [operadores lógicos](search-query-odata-logical-operators.md) para testar se um campo se encontra dentro de uma determinada gama de valores. Veja os [exemplos](#examples) mais tarde neste artigo.

> [!NOTE]
> Se preferir, pode colocar o valor constante no lado esquerdo do operador e o nome de campo no lado direito. Para os operadores de gama, o significado da comparação é invertido. Por exemplo, se o valor constante `gt` estiver à esquerda, testaria se o valor constante é maior do que o campo. Também pode utilizar os operadores de comparação `geo.distance`para comparar o resultado de uma função, como, por exemplo, com um valor. Para funções booleanas como `search.ismatch`, `true` comparar `false` o resultado com ou é opcional.

## <a name="syntax"></a>Sintaxe

O seguinte EBNF ([Formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de uma expressão OData que utiliza os operadores de comparação.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Há duas formas de comparação. A única diferença entre eles é se a constante aparece no lado esquerdo ou direito do operador. A expressão do outro lado do operador deve ser uma **variável** ou uma chamada de função. Uma variável pode ser um nome de campo, ou uma variável de gama no caso de uma [expressão lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Tipos de dados para comparações

Os tipos de dados de ambos os lados de um operador de comparação devem ser compatíveis. Por exemplo, se o lado esquerdo `Edm.DateTimeOffset`for um campo de tipo, então o lado direito deve ser uma constante de data-hora. Os tipos de dados numéricos são mais flexíveis. Pode comparar variáveis e funções de qualquer tipo numérico com constantes de qualquer outro tipo numérico, com algumas limitações, conforme descrito na tabela seguinte.

| Tipo variável ou de função | Tipo de valor constante | Limitações |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | A comparação está sujeita a [regras especiais para `NaN` ](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | A constante `Edm.Double`é convertida para, resultando numa perda de precisão para valores de grande magnitude |
| `Edm.Double` | `Edm.Int32` | n/d |
| `Edm.Int64` | `Edm.Double` | Comparações `NaN` `-INF`com, `INF` ou não são permitidas |
| `Edm.Int64` | `Edm.Int64` | n/d |
| `Edm.Int64` | `Edm.Int32` | A constante `Edm.Int64` é convertida antes da comparação |
| `Edm.Int32` | `Edm.Double` | Comparações `NaN` `-INF`com, `INF` ou não são permitidas |
| `Edm.Int32` | `Edm.Int64` | n/d |
| `Edm.Int32` | `Edm.Int32` | n/d |

Para comparações que não são permitidas, `Edm.Int64` como `NaN`comparar um campo de tipo com, a API de pesquisa cognitiva Azure devolverá um erro "HTTP 400: Mau Pedido".

> [!IMPORTANT]
> Embora as comparações de tipo numérico sejam flexíveis, recomendamos vivamente comparações de escrita em filtros para que o valor constante seja do mesmo tipo de dados que a variável ou função com que está a ser comparado. Isto é especialmente importante quando se misturam valores flutuantes e inteiros, onde são possíveis conversões implícitas que perdem precisão.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Casos `null` especiais para e`NaN`

Ao utilizar operadores de comparação, é importante lembrar que todos os `null`campos não-coletores em Pesquisa Cognitiva Azure podem potencialmente ser . A tabela que se segue mostra todos os resultados `null`possíveis para uma expressão de comparação em que qualquer um dos lados pode ser:

| Operador | Resultado quando apenas o campo ou variável é`null` | Resultado quando apenas a constante é`null` | Resultado quando tanto o campo ou variável e a constante são`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Erro de pedido de mau pedido | HTTP 400: Erro de pedido de mau pedido |
| `lt` | `false` | HTTP 400: Erro de pedido de mau pedido | HTTP 400: Erro de pedido de mau pedido |
| `ge` | `false` | HTTP 400: Erro de pedido de mau pedido | HTTP 400: Erro de pedido de mau pedido |
| `le` | `false` | HTTP 400: Erro de pedido de mau pedido | HTTP 400: Erro de pedido de mau pedido |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Em resumo, `null` é igual apenas a si mesmo, e não é menos ou maior do que qualquer outro valor.

Se o seu índice `Edm.Double` tiver `NaN` campos de tipo e fizer o upload de valores para esses campos, terá de ter em conta isso ao escrever filtros. A Azure Cognitive Search implementa a norma IEEE 754 para manipular `NaN` valores, e comparações com tais valores produzem resultados não óbvios, como mostra a tabela seguinte.

| Operador | Resultado quando pelo menos um operand é`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Em resumo, `NaN` não é igual a qualquer valor, incluindo a si mesmo.

### <a name="comparing-geo-spatial-data"></a>Comparando dados geoespaciais

Não se pode comparar diretamente `Edm.GeographyPoint` um campo de tipo com `geo.distance` um valor constante, mas pode utilizar a função. Esta função devolve `Edm.Double`um valor de tipo, para que possa compará-la com uma constante numérica para filtrar com base na distância de coordenadas geo-espaciais constantes. Veja os [exemplos](#examples) abaixo.

### <a name="comparing-string-data"></a>Comparar dados de cordas

As cordas podem ser comparadas em filtros para correspondências exatas utilizando os `eq` e `ne` os operadores. Estas comparações são sensíveis aos casos.

## <a name="examples"></a>Exemplos

Combine documentos `Rating` onde o campo é entre 3 e 5, inclusive:

    Rating ge 3 and Rating le 5

Combine documentos `Location` onde o campo fica a menos de 2 km da latitude e longitude dada:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Documentos de `LastRenovationDate` correspondência em que o campo é maior ou igual a 1 de janeiro de 2015, meia-noite UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Documentos de `Details/Sku` correspondência `null`onde o campo não é:

    Details/Sku ne null

Combine documentos para hotéis onde pelo menos um quarto tem `Rooms/Type` o tipo "Quarto Deluxe", onde a cadeia do campo corresponde exatamente ao filtro:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
