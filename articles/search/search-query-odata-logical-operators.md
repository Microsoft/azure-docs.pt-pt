---
title: Referência de operador lógico do OData
titleSuffix: Azure Cognitive Search
description: Documentação de sintaxe e referência para usar operadores lógicos do OData e, ou, e não, no Azure Pesquisa Cognitiva consultas.
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
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113183"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Operadores lógicos OData no Azure Pesquisa Cognitiva-`and`, `or``not`

As [expressões de filtro OData](query-odata-filter-orderby-syntax.md) no Azure pesquisa cognitiva são expressões booleanas que são avaliadas como `true` ou `false`. Você pode escrever um filtro complexo escrevendo uma série de [filtros mais simples](search-query-odata-comparison-operators.md) e compondo-os usando os operadores lógicos do [booliano de algebra](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: um operador binário que é avaliado como `true` se as subexpressãos esquerda e direita são avaliadas como `true`.
- `or`: um operador binário que é avaliado como `true` se uma de suas subexpressãos esquerda ou direita for avaliada como `true`.
- `not`: um operador unário que é avaliado como `true` se sua subexpressão é avaliada como `false`, e vice-versa.

Eles, juntamente com os [operadores de coleção `any` e `all`](search-query-odata-collection-operators.md), permitem que você construa filtros que podem expressar critérios de pesquisa muito complexos.

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
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Há duas formas de expressões lógicas: Binary (`and`/`or`), em que há duas subexpressãos e unário (`not`), em que há apenas uma. As subexpressãos podem ser expressões booleanas de qualquer tipo:

- Campos ou variáveis de intervalo do tipo `Edm.Boolean`
- Funções que retornam valores do tipo `Edm.Boolean`, como `geo.intersects` ou `search.ismatch`
- [Expressões de comparação](search-query-odata-comparison-operators.md), como `rating gt 4`
- [Expressões de coleção](search-query-odata-collection-operators.md), como `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Os literais boolianos `true` ou `false`.
- Outras expressões lógicas construídas usando `and`, `or`e `not`.

> [!IMPORTANT]
> Há algumas situações em que nem todos os tipos de subexpressão podem ser usados com `and`/`or`, especialmente dentro de expressões lambda. Consulte [operadores de coleção OData no Azure pesquisa cognitiva](search-query-odata-collection-operators.md#limitations) para obter detalhes.

### <a name="logical-operators-and-null"></a>Operadores lógicos e `null`

A maioria das expressões boolianas, como funções e comparações, não pode produzir `null` valores, e os operadores lógicos não podem ser aplicados diretamente ao literal de `null` (por exemplo, `x and null` não é permitido). No entanto, os campos Boolianos podem ser `null`, portanto, você precisa estar ciente de como os operadores `and`, `or`e `not` se comportam na presença de NULL. Isso é resumido na tabela a seguir, em que `b` é um campo do tipo `Edm.Boolean`:

| Expressão | Resultado quando `b` é `null` |
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

Quando um campo booliano `b` aparece sozinho em uma expressão de filtro, ele se comporta como se tivesse sido escrito `b eq true`, portanto, se `b` for `null`, a expressão será avaliada como `false`. Da mesma forma, `not b` se comporta como `not (b eq true)`, portanto, ele é avaliado como `true`. Dessa forma, `null` campos se comportam da mesma maneira que `false`. Isso é consistente com a forma como eles se comportam quando combinados com outras expressões usando `and` e `or`, conforme mostrado na tabela acima. Apesar disso, uma comparação direta com `false` (`b eq false`) ainda será avaliada como `false`. Em outras palavras, `null` não é igual a `false`, embora se comporta como em expressões booleanas.

## <a name="examples"></a>Exemplos

Corresponder documentos em que o campo de `rating` está entre 3 e 5, inclusive:

    rating ge 3 and rating le 5

Corresponder documentos onde todos os elementos do campo de `ratings` são menores que 3 ou maiores que 5:

    ratings/all(r: r lt 3 or r gt 5)

Corresponder documentos em que o campo de `location` está dentro do polígono fornecido e o documento não contém o termo "público".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Corresponder documentos para hotéis em Vancouver, Canadá em que há uma sala de luxo com uma taxa de base inferior a 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Passos seguintes  

- [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;do Azure pesquisa cognitiva API REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
