---
title: Referência de operador lógico OData-Azure Search
description: Operadores lógicos OData, and, or e not, em Azure Search consultas.
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
ms.openlocfilehash: bf4939a40a2fdf1c8fc6cf97beca0184b1604c98
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648002"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Operadores lógicos OData em Azure Search `and`- `or`,,`not`

As [expressões de filtro OData](query-odata-filter-orderby-syntax.md) no Azure Search são expressões booleanas que `true` são `false`avaliadas como ou. Você pode escrever um filtro complexo escrevendo uma série de [filtros mais simples](search-query-odata-comparison-operators.md) e compondo-os usando os operadores lógicos do booliano de [Algebra](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Um operador binário que é avaliado como `true` se as subexpressão à esquerda e à direita são avaliadas como. `true`
- `or`: Um operador binário que é avaliado como `true` se uma das subexpressãos esquerda ou direita for avaliada como. `true`
- `not`: Um operador unário que é avaliado `true` como se sua subexpressão for avaliada `false`como, e vice-versa.

Eles, juntamente com os [ `any` operadores de coleção `all`e ](search-query-odata-collection-operators.md), permitem que você construa filtros que podem expressar critérios de pesquisa muito complexos.

## <a name="syntax"></a>Sintaxe

O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de uma expressão OData que usa os operadores lógicos.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md) para o EBNF completo.

Há duas formas de expressões lógicas: Binary (`and`/`or`), em que há duas subexpressãos e unário (`not`), em que há apenas uma. As subexpressãos podem ser expressões booleanas de qualquer tipo:

- Campos ou variáveis de intervalo do tipo`Edm.Boolean`
- Funções que retornam valores do `Edm.Boolean`tipo, `geo.intersects` como ou`search.ismatch`
- [Expressões de comparação](search-query-odata-comparison-operators.md), como`rating gt 4`
- [Expressões de coleção](search-query-odata-collection-operators.md), como`Rooms/any(room: room/Type eq 'Deluxe Room')`
- Os literais `true` boolianos `false`ou.
- Outras expressões lógicas construídas `or`usando `and`, `not`e.

> [!IMPORTANT]
> Há algumas situações em que nem todos os tipos de subexpressão podem ser usados com `and` / `or`, especialmente dentro de expressões lambda. Consulte [operadores de coleção OData no Azure Search](search-query-odata-collection-operators.md#limitations) para obter detalhes.

### <a name="logical-operators-and-null"></a>Operadores lógicos e`null`

A maioria das expressões booleanas, como funções e comparações, não pode produzir `null` valores, e os operadores lógicos não podem ser aplicados diretamente `x and null` `null` ao literal (por exemplo, não é permitido). No entanto, os campos `null`boolianos podem ser, portanto, você precisa estar `and`ciente de como `not` os operadores, `or`e se comportam na presença de NULL. Isso é resumido na tabela a seguir, `b` em que é um campo `Edm.Boolean`do tipo:

| Expressão | Resultado quando `b` é`null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Quando um `b` campo booliano aparece sozinho em uma expressão de filtro, ele se comporta como se tivesse sido escrito `b` `b eq true`, portanto, se for `null`, a expressão será avaliada `false`como. Da mesma `not b` forma, comporta- `not (b eq true)`se como, portanto, ele `true`é avaliado como. Dessa forma, `null` os campos se comportam `false`da mesma maneira que. Isso é consistente com a forma como eles se comportam quando `and` combinados `or`com outras expressões usando e, conforme mostrado na tabela acima. Apesar disso, uma comparação direta com `false` (`b eq false` `false`) ainda será avaliada. Em outras palavras, `null` o não é igual `false`a, embora se comporta como em expressões booleanas.

## <a name="examples"></a>Exemplos

Corresponder documentos em que `rating` o campo está entre 3 e 5, inclusive:

    rating ge 3 and rating le 5

Corresponder documentos em que todos os elementos `ratings` do campo sejam menores que 3 ou maiores que 5:

    ratings/all(r: r lt 3 or r gt 5)

Corresponder documentos onde o `location` campo está dentro do polígono fornecido e o documento não contém o termo "público".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Corresponder documentos para hotéis em Vancouver, Canadá em que há uma sala de luxo com uma taxa de base inferior a 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
