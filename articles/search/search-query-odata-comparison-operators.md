---
title: Referência de operador de comparação de OData - Azure Search
description: Operadores de comparação de OData, eq, ne, gt, lt, ge e le, nas consultas de pesquisa do Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: b51bf3d77283ae828f47fdb0355d2deb43f071a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079928"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>Operadores de comparação de OData no Azure Search - `eq`, `ne`, `gt`, `lt`, `ge`, e `le`

A operação mais básica dentro de um [expressão de filtro de OData](query-odata-filter-orderby-syntax.md) no Azure Search é comparar um campo para um determinado valor. Dois tipos de comparação são possíveis, comparação de igualdade e comparação de intervalo. Pode utilizar os seguintes operadores para comparar um campo como um valor constante:

Operadores de igualdade:

- `eq`: Testar se um campo é **igual a** um valor constante
- `ne`: Testar se um campo é **não é igual a** um valor constante

Operadores de intervalo:

- `gt`: Testar se um campo é **superior a** um valor constante
- `lt`: Testar se um campo é **inferior a** um valor constante
- `ge`: Testar se um campo é **maior que ou igual a** um valor constante
- `le`: Testar se um campo é **inferior ou igual a** um valor constante

Pode utilizar os operadores de intervalo em combinação com o [operadores lógicos](search-query-odata-logical-operators.md) para testar se um campo é dentro de um determinado intervalo de valores. Consulte a [exemplos](#examples) mais adiante neste artigo.

> [!NOTE]
> Se preferir, pode colocar o valor da constante no lado esquerdo do operador e o nome do campo no lado direito. Para os operadores de intervalo, o significado da comparação é revertido. Por exemplo, se o valor da constante é à esquerda, `gt` poderia testar se o valor da constante é maior do que o campo. Também pode utilizar os operadores de comparação para comparar o resultado de uma função, como `geo.distance`, com um valor. Para Boolean as funções, como `search.ismatch`, comparar o resultado `true` ou `false` é opcional.

## <a name="syntax"></a>Sintaxe

A seguinte EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de uma expressão de OData que utiliza os operadores de comparação.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

Existem duas formas de expressões de comparação. A única diferença entre eles é se a constante é apresentado do esquerda - ou direita lado do operador. A expressão no outro lado do operador deve ser um **variável** ou uma chamada de função. Uma variável pode ser um nome de campo ou uma variável de alcance no caso de um [expressão lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Tipos de dados para comparações

Os tipos de dados em ambos os lados de um operador de comparação têm de ser compatíveis. Por exemplo, se o lado esquerdo for um campo do tipo `Edm.DateTimeOffset`, em seguida, o lado direito tem de ser uma constante de data / hora. Tipos de dados numéricos são mais flexíveis. Pode comparar as variáveis e funções de qualquer tipo numérico com constantes de qualquer outro tipo numérico, com algumas limitações, conforme descrito na tabela seguinte.

| Tipo de variável ou função | Tipo de valor constante | Limitações |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Comparação está sujeito [especial de regras para `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Constante é convertido em `Edm.Double`, o que resulta em perda de precisão para valores de grande magnitude |
| `Edm.Double` | `Edm.Int32` | n/d |
| `Edm.Int64` | `Edm.Double` | Comparações a serem `NaN`, `-INF`, ou `INF` não são permitidos |
| `Edm.Int64` | `Edm.Int64` | n/d |
| `Edm.Int64` | `Edm.Int32` | Constante é convertido em `Edm.Int64` antes de comparação |
| `Edm.Int32` | `Edm.Double` | Comparações a serem `NaN`, `-INF`, ou `INF` não são permitidos |
| `Edm.Int32` | `Edm.Int64` | n/d |
| `Edm.Int32` | `Edm.Int32` | n/d |

Para comparações que não são permitidas, como um campo do tipo de comparar `Edm.Int64` para `NaN`, a API de REST de pesquisa do Azure irá devolver um "HTTP 400: Erro de pedido incorreto".

> [!IMPORTANT]
> Apesar de comparações de tipo numérico são flexíveis, é altamente recomendável escrever comparações em filtros para que o valor da constante é do mesmo tipo de dados como a variável ou função para o qual está a ser comparado. Isto é especialmente importante quando mistura de ponto flutuante e os valores de número inteiro, em que são possíveis conversões implícitas que perdem a precisão.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Casos de especial para `null` e `NaN`

Ao utilizar os operadores de comparação, é importante lembrar-se de que todos os campos não coleção no Azure Search podem ser `null`. A tabela seguinte mostra todos os possíveis resultados para uma expressão de comparação em que podem ser ambos os lados `null`:

| Operador | Resultado quando é apenas o campo ou uma variável `null` | Resultado quando é apenas a constante `null` | Resultado quando o campo ou variável e a constante `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Erro de pedido incorreto | HTTP 400: Erro de pedido incorreto |
| `lt` | `false` | HTTP 400: Erro de pedido incorreto | HTTP 400: Erro de pedido incorreto |
| `ge` | `false` | HTTP 400: Erro de pedido incorreto | HTTP 400: Erro de pedido incorreto |
| `le` | `false` | HTTP 400: Erro de pedido incorreto | HTTP 400: Erro de pedido incorreto |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Em resumo, `null` igual apenas para si próprio e não menos ou superior a qualquer outro valor.

Se o seu índice tem campos do tipo `Edm.Double` e carregar `NaN` valores para esses campos, precisará levar em conta que ao criar filtros. O Azure Search implementa o padrão IEEE 754 para tratar do `NaN` valores e comparações com esses valores produzam resultados não óbvias, como mostrado na seguinte tabela.

| Operador | Resultado quando é, pelo menos, um operando `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Em resumo, `NaN` não é igual a qualquer valor, incluindo ele próprio.

### <a name="comparing-geo-spatial-data"></a>Comparação de dados geoespaciais

Não é possível comparar diretamente um campo do tipo `Edm.GeographyPoint` com um valor constante, mas pode usar o `geo.distance` função. Esta função devolve um valor do tipo `Edm.Double`, por isso, pode compará-lo com um numérico constante para filtrar com base na distância de coordenadas geoespaciais constantes. Consulte a [exemplos](#examples) abaixo.

### <a name="comparing-string-data"></a>Comparação de dados de cadeia de caracteres

Cadeias de caracteres podem ser comparadas em filtros para correspondências exatas a utilizar o `eq` e `ne` operadores. Essas comparações diferenciam maiúsculas de minúsculas.

## <a name="examples"></a>Exemplos

Correspondência de documentos onde o `Rating` campo é entre 3 e 5, inclusive:

    Rating ge 3 and Rating le 5

Correspondência de documentos onde o `Location` campo é inferior a 2 quilômetros de latitude e longitude:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Correspondência de documentos onde o `LastRenovationDate` campo é maior que ou igual a 1 de Janeiro de 2015, meia-noite UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Correspondência de documentos onde o `Details/Sku` pole není `null`:

    Details/Sku ne null

Corresponda nos documentos para hotéis em que, pelo menos, uma sala tem o tipo "Sala Deluxe", em que a cadeia de caracteres do `Rooms/Type` campo corresponde ao filtro exatamente:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
