---
title: Referência de operador lógico de OData - Azure Search
description: Operadores lógicos do OData e, em alternativa e não, em consultas de pesquisa do Azure.
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
ms.openlocfilehash: de93765117b4cafe70e5ad277e32ca0a1fa8d90a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079772"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Operadores lógicos de OData no Azure Search - `and`, `or`, `not`

[Expressões de filtro de OData](query-odata-filter-orderby-syntax.md) no Azure Search são expressões booleanas avaliadas como `true` ou `false`. Pode escrever um filtro complexo com a criação de uma série de [filtros mais simples](search-query-odata-comparison-operators.md) e compor-las com os operadores lógicos partir [booleano álgebra](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Um operador binário, que é avaliada como `true` se ambas as respetivas esquerdas e direita subexpressões avaliam como `true`.
- `or`: Um operador binário, que é avaliada como `true` se qualquer um dos respetivas as subexpressões esquerdas ou direita é avaliada como `true`.
- `not`: Um operador unário, que é avaliada como `true` se sua subexpressão for avaliada como `false`e vice-versa.

Estes, em conjunto com o [operadores de coleção `any` e `all` ](search-query-odata-collection-operators.md), permitem-lhe construir filtros que podem expressar critérios de pesquisa muito complexa.

## <a name="syntax"></a>Sintaxe

A seguinte EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de uma expressão de OData que utiliza os operadores lógicos.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

Existem duas formas de expressões lógicas: binário (`and`/`or`), em que existem dois as subexpressões e unário (`not`), em que existe apenas um. As subexpressões podem ser expressões booleanas de qualquer tipo:

- Campos ou intervalo de variáveis do tipo `Edm.Boolean`
- As funções que retornam valores do tipo `Edm.Boolean`, tais como `geo.intersects` ou `search.ismatch`
- [Expressões de comparação](search-query-odata-comparison-operators.md), por exemplo, `rating gt 4`
- [Expressões de coleção](search-query-odata-collection-operators.md), por exemplo, `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Os literais booleanos `true` ou `false`.
- Outras expressões lógicas construído de acordo com `and`, `or`, e `not`.

> [!IMPORTANT]
> Existem algumas situações em que nem todos os tipos de subexpressão podem ser utilizados com `and` / `or`, particularmente no expressões lambda. Ver [operadores de coleção OData na Azure Search](search-query-odata-collection-operators.md#limitations) para obter detalhes.

### <a name="logical-operators-and-null"></a>Operadores lógicos e `null`

A maioria das expressões booleanas como funções e comparações não é possível produzir `null` valores e os operadores lógicos não não possível aplicar a `null` literal diretamente (por exemplo, `x and null` não é permitida). No entanto, podem ser campos Boolianos `null`, por isso, precisa estar ciente de como o `and`, `or`, e `not` operadores se comportam na presença de null. Isso está resumido na tabela a seguir, onde `b` é um campo do tipo `Edm.Boolean`:

| expressão | Resultado quando `b` é `null` |
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

Quando um campo Booleano `b` aparece por si só, numa expressão de filtro, ele se comporta como se ele tivesse sido escrito `b eq true`, então, se `b` é `null`, a expressão avalia `false`. Da mesma forma, `not b` se comporta como `not (b eq true)`, para que ela é avaliada como `true`. Dessa forma, `null` campos se comportam da mesma como `false`. Isto é consistente com a forma como se comportam quando combinada com outras expressões utilizando `and` e `or`, conforme mostrado na tabela acima. Apesar disso, uma comparação direta para `false` (`b eq false`) ainda será avaliado como `false`. Em outras palavras, `null` não é igual a `false`, apesar de esta se comporta como ele em expressões booleanas.

## <a name="examples"></a>Exemplos

Correspondência de documentos onde o `rating` campo é entre 3 e 5, inclusive:

    rating ge 3 and rating le 5

Correspondência de documentos onde todos os elementos do `ratings` campo são menos de 3 ou superior a 5:

    ratings/all(r: r lt 3 or r gt 5)

Correspondência de documentos onde o `location` campo está dentro do polígono especificado e o documento não contém o termo "public".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Corresponda nos documentos para hotéis em Vancouver, Canadá onde existe uma sala deluxe com uma base de velocidade inferior a 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
