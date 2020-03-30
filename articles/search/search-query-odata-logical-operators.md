---
title: Referência lógica do operador oData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para a utilização de operadores lógicos OData, ou, ou não, em consultas de Pesquisa Cognitiva Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113183"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>OData operadores lógicos em `and` `or`Pesquisa Cognitiva Azure - ,`not`

[Expressões](query-odata-filter-orderby-syntax.md) de filtro OData em Pesquisa Cognitiva Azure `true` `false`são expressões booleanas que avaliam ou . Pode escrever um filtro complexo escrevendo uma série de [filtros mais simples](search-query-odata-comparison-operators.md) e compondo-os utilizando os operadores lógicos da [álgebra booleana:](https://en.wikipedia.org/wiki/Boolean_algebra)

- `and`: Um operador binário que `true` avalia se as suas subexpressões `true`esquerda e direita avaliam a .
- `or`: Um operador binário que `true` avalia se uma das suas subexpressões `true`esquerda ou direita avalia para .
- `not`: Um operador não-secundário `true` que avalie se `false`a sua subexpressão avalia para, e vice-versa.

Estes, juntamente com os [operadores `any` `all` ](search-query-odata-collection-operators.md)de recolha e, permitem-lhe construir filtros que possam expressar critérios de pesquisa muito complexos.

## <a name="syntax"></a>Sintaxe

O seguinte EBNF ([Formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de uma expressão OData que utiliza os operadores lógicos.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Existem duas formas de expressões`and`/`or`lógicas: binária , onde existem`not`duas subexpressões, e não-ary ( ), onde há apenas uma. As subexpressões podem ser expressões booleanas de qualquer tipo:

- Campos ou variáveis de gama de tipo`Edm.Boolean`
- Funções que devolução `Edm.Boolean`de `geo.intersects` valores de tipo, tais como ou`search.ismatch`
- [Expressões de comparação,](search-query-odata-comparison-operators.md)tais como`rating gt 4`
- [Expressões de coleção,](search-query-odata-collection-operators.md)tais como`Rooms/any(room: room/Type eq 'Deluxe Room')`
- Os literais `true` `false`booleanos ou.
- Outras expressões lógicas construídas usando, `and` `or`e `not`.

> [!IMPORTANT]
> Existem algumas situações em que nem todos os `and` / `or`tipos de subexpressão podem ser usados com, particularmente dentro das expressões lambda. Consulte [os operadores de recolha oData em Azure Cognitive Search](search-query-odata-collection-operators.md#limitations) para obter detalhes.

### <a name="logical-operators-and-null"></a>Operadores lógicos e`null`

A maioria das expressões booleanas, `null` como funções e comparações, `null` não pode produzir `x and null` valores, e os operadores lógicos não podem ser aplicados diretamente ao literal (por exemplo, não é permitido). No entanto, os `null`campos booleanos podem ser `and`, `or`por `not` isso é preciso estar atento à forma como os , e os operadores se comportam na presença de nulos. Isto é resumido na `b` tabela seguinte, `Edm.Boolean`onde se encontra um campo de tipo:

| Expressão | Resultado `b` quando é`null` |
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

Quando um campo `b` booleano aparece por si só numa expressão de filtro, comporta-se como `b eq true`se tivesse sido escrito , assim `b` se for `null`, a expressão avalia para `false`. Da mesma `not b` forma, `not (b eq true)`comporta-se como, `true`por isso avalia a . Desta forma, `null` os campos comportam-se da mesma forma que. `false` Isto é consistente com a forma como se `and` `or`comportam quando combinados com outras expressões usando e, como mostrado na tabela acima. Apesar disso, uma `false` `b eq false`comparação direta `false`com . ainda avaliará para . Por outras `null` palavras, `false`não é igual a , mesmo que se compore como em expressões booleanas.

## <a name="examples"></a>Exemplos

Combine documentos `rating` onde o campo é entre 3 e 5, inclusive:

    rating ge 3 and rating le 5

Combine documentos em que `ratings` todos os elementos do campo sejam inferiores a 3 ou mais de 5:

    ratings/all(r: r lt 3 or r gt 5)

Corresponda aos `location` documentos em que o campo se encontra dentro do polígono dado, e o documento não contém o termo "público".

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Documentos de correspondência para hotéis em Vancouver, Canadá onde há um quarto de luxo com uma taxa base inferior a 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
