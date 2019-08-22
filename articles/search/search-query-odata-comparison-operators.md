---
title: Referência do operador de comparação de OData-Azure Search
description: Operadores de comparação OData, EQ, ne, gt, lt, GE e Le, em Azure Search consultas.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: a8bd8b05fd874e05e5e59042d461f4a4286c81e4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648067"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>Operadores de comparação OData em Azure Search `eq`- `ne`, `gt`, `lt`, `ge`, e`le`

A operação mais básica em uma [expressão de filtro OData](query-odata-filter-orderby-syntax.md) no Azure Search é comparar um campo com um determinado valor. Dois tipos de comparação são possíveis – comparação de igualdade e comparação de intervalo. Você pode usar os seguintes operadores para comparar um campo com um valor constante:

Operadores de igualdade:

- `eq`: Testar se um campo é **igual a** um valor constante
- `ne`: Testar se um campo **não é igual a** um valor constante

Operadores de intervalo:

- `gt`: Testar se um campo é **maior que** um valor constante
- `lt`: Testar se um campo é **menor que** um valor constante
- `ge`: Testar se um campo é **maior ou igual a** um valor constante
- `le`: Testar se um campo é **menor ou igual a** um valor constante

Você pode usar os operadores de intervalo em combinação com os [operadores lógicos](search-query-odata-logical-operators.md) para testar se um campo está dentro de um determinado intervalo de valores. Consulte os [exemplos](#examples) mais adiante neste artigo.

> [!NOTE]
> Se preferir, você pode colocar o valor constante no lado esquerdo do operador e o nome do campo no lado direito. Para operadores de intervalo, o significado da comparação é invertido. Por exemplo, se o valor constante estiver à esquerda, `gt` o testará se o valor constante é maior que o campo. Você também pode usar os operadores de comparação para comparar o resultado de uma função, `geo.distance`como, com um valor. Para funções booleanas como `search.ismatch`, comparando o resultado para `false` `true` ou é opcional.

## <a name="syntax"></a>Sintaxe

O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de uma expressão OData que usa os operadores de comparação.

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
> [Diagrama de sintaxe do OData para Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md) para o EBNF completo.

Há duas formas de expressões de comparação. A única diferença entre eles é se a constante aparece no lado esquerdo ou direito do operador. A expressão no outro lado do operador deve ser uma **variável** ou uma chamada de função. Uma variável pode ser um nome de campo ou uma variável de intervalo no caso de uma [expressão lambda](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Tipos de dados para comparações

Os tipos de dados em ambos os lados de um operador de comparação devem ser compatíveis. Por exemplo, se o lado esquerdo é um campo do tipo `Edm.DateTimeOffset`, o lado direito deve ser uma constante de data e hora. Os tipos de dados numéricos são mais flexíveis. Você pode comparar variáveis e funções de qualquer tipo numérico com constantes de qualquer outro tipo numérico, com algumas limitações, conforme descrito na tabela a seguir.

| Variável ou tipo de função | Tipo de valor constante | Limitações |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | A comparação está sujeita a [regras especiais `NaN` para](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | A constante é convertida em `Edm.Double`, resultando em uma perda de precisão para valores de grande magnitude |
| `Edm.Double` | `Edm.Int32` | n/d |
| `Edm.Int64` | `Edm.Double` | Comparações `NaN`com `-INF`,, `INF` ou não são permitidas |
| `Edm.Int64` | `Edm.Int64` | n/d |
| `Edm.Int64` | `Edm.Int32` | A `Edm.Int64` constante é convertida antes da comparação |
| `Edm.Int32` | `Edm.Double` | Comparações `NaN`com `-INF`,, `INF` ou não são permitidas |
| `Edm.Int32` | `Edm.Int64` | n/d |
| `Edm.Int32` | `Edm.Int32` | n/d |

Para comparações que não são permitidas, como comparar um campo do `Edm.Int64` tipo `NaN`com, a API REST do Azure Search retornará um "http 400: Erro de solicitação inadequada ".

> [!IMPORTANT]
> Embora as comparações de tipo numérico sejam flexíveis, é altamente recomendável escrever comparações em filtros para que o valor constante seja do mesmo tipo de dados que a variável ou função à qual ele está sendo comparado. Isso é especialmente importante ao misturar valores inteiros e de ponto flutuante, em que conversões implícitas que perdem precisão são possíveis.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Casos especiais para `null` e`NaN`

Ao usar operadores de comparação, é importante lembrar que todos os campos que não são de coleção em Azure Search potencialmente `null`podem ser. A tabela a seguir mostra todos os resultados possíveis para uma expressão de comparação em que um dos lados `null`pode ser:

| Operator | Resultado quando apenas o campo ou a variável for`null` | Resultado quando apenas a constante for`null` | Resultado quando o campo ou a variável e a constante são`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Erro de solicitação inadequado | HTTP 400: Erro de solicitação inadequado |
| `lt` | `false` | HTTP 400: Erro de solicitação inadequado | HTTP 400: Erro de solicitação inadequado |
| `ge` | `false` | HTTP 400: Erro de solicitação inadequado | HTTP 400: Erro de solicitação inadequado |
| `le` | `false` | HTTP 400: Erro de solicitação inadequado | HTTP 400: Erro de solicitação inadequado |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Em resumo, `null` é igual apenas a si mesmo e não é menor ou maior que qualquer outro valor.

Se o índice tiver campos do tipo `Edm.Double` e você carregar `NaN` valores para esses campos, será necessário considerar isso ao gravar filtros. Azure Search implementa o padrão IEEE 754 para manipulação `NaN` de valores, e as comparações com esses valores produzem resultados não óbvios, conforme mostrado na tabela a seguir.

| Operator | Resultado quando pelo menos um operando for`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Em resumo, `NaN` não é igual a qualquer valor, incluindo a si mesmo.

### <a name="comparing-geo-spatial-data"></a>Comparando dados geoespaciais

Não é possível comparar diretamente um campo do `Edm.GeographyPoint` tipo com um valor constante, mas você pode usar `geo.distance` a função. Essa função retorna um valor do tipo `Edm.Double`, de modo que você pode compará-lo com uma constante numérica para filtrar com base na distância das coordenadas geoespaciais constantes. Consulte os [exemplos](#examples) abaixo.

### <a name="comparing-string-data"></a>Comparando dados de cadeia de caracteres

As cadeias de caracteres podem ser comparadas em `eq` filtros `ne` para correspondências exatas usando os operadores e. Essas comparações diferenciam maiúsculas de minúsculas.

## <a name="examples"></a>Exemplos

Corresponder documentos em que `Rating` o campo está entre 3 e 5, inclusive:

    Rating ge 3 and Rating le 5

Corresponder documentos em que `Location` o campo é inferior a 2 quilômetros da latitude e longitude determinada:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Corresponder documentos em que `LastRenovationDate` o campo é maior ou igual a 1º de janeiro de 2015, meia-noite UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Corresponder documentos em que `Details/Sku` o campo não `null`é:

    Details/Sku ne null

Corresponder documentos para hotéis em que pelo menos uma sala tem o tipo "sala de luxo", em que `Rooms/Type` a cadeia de caracteres do campo corresponde exatamente ao filtro:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
