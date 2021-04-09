---
title: Referência lógica do operador OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para a utilização de operadores lógicos OData, e, ou, e não, em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: 27d5427d34de591f9cfeab2310d79a2fde217624
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88917878"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Operadores lógicos OData em Pesquisa Cognitiva Azure - `and` , `or``not`

[Expressões de filtro OData](query-odata-filter-orderby-syntax.md) em Azure Cognitive Search são expressões booleanas que avaliam `true` ou `false` . Pode escrever um filtro complexo escrevendo uma série de [filtros mais simples](search-query-odata-comparison-operators.md) e compondo-os utilizando os operadores lógicos da [álgebra booleana:](https://en.wikipedia.org/wiki/Boolean_algebra)

- `and`: Um operador binário que avalie `true` se as suas subex expressões esquerda e direita avaliam `true` a .
- `or`: Um operador binário que avalie `true` se uma das suas subex expressões esquerda ou direita avaliar a `true` .
- `not`: Um operador nãoário que avalie se a `true` sua subexposição avalia e `false` vice-versa.

Estes, juntamente com os operadores de [recolha `any` e, `all` ](search-query-odata-collection-operators.md)permitem-lhe construir filtros que possam expressar critérios de pesquisa muito complexos.

## <a name="syntax"></a>Syntax

O seguinte formulário EBNF ([Formulário de Backus-Naur Alargado)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática de uma expressão OData que utiliza os operadores lógicos.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

Existem duas formas de expressões lógicas: binárias, `and` / `or` onde existem duas subexpressões, e unary `not` (), onde há apenas uma. As subexpressões podem ser expressões booleanas de qualquer tipo:

- Campos ou variáveis de gama de tipo `Edm.Boolean`
- Funções que devolvem valores de `Edm.Boolean` tipo, tais como `geo.intersects` ou `search.ismatch`
- [Expressões de comparação,](search-query-odata-comparison-operators.md)tais como `rating gt 4`
- [Expressões de recolha,](search-query-odata-collection-operators.md)tais como `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Os literais booleanos `true` ou `false` .
- Outras expressões lógicas construídas com `and` `or` , e `not` .

> [!IMPORTANT]
> Existem algumas situações em que nem todos os tipos de subexposição podem ser `and` / `or` usados, particularmente no interior das expressões de lambda. Consulte [os operadores de recolha OData na Azure Cognitive Search](search-query-odata-collection-operators.md#limitations) para obter detalhes.

### <a name="logical-operators-and-null"></a>Operadores lógicos e `null`

A maioria das expressões booleanas, tais como funções e comparações, não podem produzir `null` valores, e os operadores lógicos não podem ser aplicados diretamente ao `null` literal (por exemplo, `x and null` não é permitido). No entanto, os campos booleanos podem ser `null` , por isso é preciso estar ciente de como os , e os `and` `or` `not` operadores se comportam na presença de nulos. Isto é resumido na tabela seguinte, onde `b` está um campo de `Edm.Boolean` tipo:

| Expression | Resultado quando `b` é `null` |
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

Quando um campo Boolean `b` aparece por si só numa expressão de filtro, comporta-se como se tivesse sido escrito , `b eq true` então se for , a `b` expressão avalia a `null` `false` . Da mesma forma, `not b` `not (b eq true)` comporta-se como, por isso avalia a `true` . Desta forma, `null` os campos comportam-se da mesma forma `false` que. Isto é consistente com a forma como se comportam quando combinados com outras expressões usando `and` e , como mostrado na tabela `or` acima. Apesar disso, uma comparação direta com `false` `b eq false` . `false` Por outras palavras, `null` não é igual a , mesmo que se `false` comporte como em expressões booleanas.

## <a name="examples"></a>Exemplos

Documentos de correspondência em que o `rating` campo é entre 3 e 5, inclusive:

```odata-filter-expr
    rating ge 3 and rating le 5
```

Corresponda documentos em que todos os elementos do campo sejam inferiores a `ratings` 3 ou superiores a 5:

```odata-filter-expr
    ratings/all(r: r lt 3 or r gt 5)
```

Combine documentos em que o `location` campo se encontra dentro do polígono dado, e o documento não contém o termo "público".

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')
```

Combine documentos para hotéis em Vancouver, Canadá onde há um quarto de luxo com uma taxa base inferior a 160:

```odata-filter-expr
    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)
```

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)