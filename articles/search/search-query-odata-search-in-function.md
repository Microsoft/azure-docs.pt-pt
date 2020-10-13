---
title: Referência de função search.in OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para a utilização da função search.in nas consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: 9ad6f89392846564631b70f0acfb5658a050be80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88922825"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Função OData `search.in` em Pesquisa Cognitiva Azure

Um cenário comum nas [expressões de filtro OData](query-odata-filter-orderby-syntax.md) é verificar se um único campo em cada documento é igual a um de muitos valores possíveis. Por exemplo, é assim que algumas aplicações [implementam o corte de segurança](search-security-trimming-for-azure-search.md) -- verificando um campo contendo um ou mais IDs principais contra uma lista de IDs principais que representam o utilizador que emite a consulta. Uma maneira de escrever uma consulta como esta é usar o [`eq`](search-query-odata-comparison-operators.md) e [`or`](search-query-odata-logical-operators.md) os operadores:

```odata-filter-expr
    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')
```

No entanto, há uma maneira mais curta de escrever isto, usando a `search.in` função:

```odata-filter-expr
    group_ids/any(g: search.in(g, '123, 456, 789'))
```

> [!IMPORTANT]
> Além de ser mais curto e fácil de ler, o uso `search.in` também proporciona [benefícios](#bkmk_performance) de desempenho e evita [certas limitações de tamanho dos filtros](search-query-odata-filter.md#bkmk_limits) quando há centenas ou mesmo milhares de valores para incluir no filtro. Por esta razão, recomendamos vivamente a `search.in` utilização, em vez de uma disjunção mais complexa das expressões de igualdade.

> [!NOTE]
> A versão 4.01 da norma OData introduziu recentemente o [ `in` operador](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), que tem um comportamento semelhante ao da `search.in` função em Azure Cognitive Search. No entanto, a Azure Cognitive Search não suporta este operador, pelo que deve utilizar a `search.in` função.

## <a name="syntax"></a>Syntax

O seguinte EBNF[(Formulário Backus-Naur Alargado)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática da `search.in` função:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

A `search.in` função testa se um determinado campo de cordas ou variável de alcance é igual a uma de uma determinada lista de valores. A igualdade entre a variável e cada valor da lista é determinada de forma sensível a casos, da mesma forma que para o `eq` operador. Portanto, uma expressão como `search.in(myfield, 'a, b, c')` é equivalente a , `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'` exceto que vai produzir um desempenho `search.in` muito melhor.

Existem duas sobrecargas da `search.in` função:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Os parâmetros são definidos na tabela seguinte:

| Nome do parâmetro | Tipo | Descrição |
| --- | --- | --- |
| `variable` | `Edm.String` | Uma referência de campo de cordas (ou uma variável de alcance sobre um campo de recolha de cordas no caso em que `search.in` é usado dentro de uma ou `any` `all` expressão). |
| `valueList` | `Edm.String` | Uma cadeia contendo uma lista delimitada de valores que corresponda ao `variable` parâmetro. Se o `delimiters` parâmetro não for especificado, os delimiters predefinidos são o espaço e a vírgula. |
| `delimiters` | `Edm.String` | Uma corda onde cada personagem é tratado como um separador ao analisar o `valueList` parâmetro. O valor predefinido deste parâmetro é `' ,'` o que significa que quaisquer valores com espaços e/ou vírgulas entre eles serão separados. Se precisar de utilizar separadores que não espaços e vírgulas porque os seus valores incluem esses caracteres, pode especificar delimiters alternativos, como `'|'` neste parâmetro. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Desempenho de `search.in`

Se `search.in` utilizar, pode esperar tempo de resposta de segundo quando o segundo parâmetro contiver uma lista de centenas ou milhares de valores. Não existe um limite explícito no número de itens a que pode `search.in` passar, embora ainda esteja limitado pelo tamanho máximo de pedido. No entanto, a latência vai crescer à medida que o número de valores aumenta.

## <a name="examples"></a>Exemplos

Encontre todos os hotéis com nome igual a 'Sea View motel' ou 'Budget hotel'. As frases contêm espaços, que é um delimiter padrão. Pode especificar um limontinho alternativo em cotações simples como o terceiro parâmetro de cadeia:  

```odata-filter-expr
    search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

Encontre todos os hotéis com nome igual a 'Sea View motel' ou 'Budget hotel' separados por '/'):

```odata-filter-expr
    search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

Encontre todos os hotéis com quartos com a etiqueta 'wifi' ou 'tub':

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))
```

Encontre uma correspondência em frases dentro de uma coleção, tais como "toalheiros aquecidos" ou "secador de cabelo incluído" em etiquetas.

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

Encontre todos os hotéis sem a etiqueta 'motel' ou 'cabine':

```odata-filter-expr
    Tags/all(tag: not search.in(tag, 'motel, cabin'))
```

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)