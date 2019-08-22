---
title: Referência de sintaxe de expressão OData-Azure Search
description: Especificação de gramática formal e sintaxe para expressões OData em consultas Azure Search.
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
ms.openlocfilehash: 5d7e6456cd6a6648ff2ca38ecbb4f2de5479d7c9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647499"
---
# <a name="odata-expression-syntax-reference-for-azure-search"></a>Referência de sintaxe de expressão OData para Azure Search

Azure Search usa [expressões OData](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) como parâmetros em toda a API. Normalmente, as expressões OData são usadas para os `$orderby` parâmetros `$filter` e. Essas expressões podem ser complexas, contendo várias cláusulas, funções e operadores. No entanto, até mesmo expressões OData simples como caminhos de propriedade são usadas em muitas partes da API REST do Azure Search. Por exemplo, as expressões de caminho são usadas para fazer referência a subcampos de campos complexos em todos os lugares na API, como ao listar [](index-add-suggesters.md)subcampos em um Sugestor `$select` , uma [função de Pontuação](index-add-scoring-profiles.md), o parâmetro ou até mesmo a [pesquisa em campo no Lucene consultas](query-lucene-syntax.md).

Este artigo descreve todas essas formas de expressões OData usando uma gramática formal. Também há um [Diagrama interativo](#syntax-diagram) para ajudar a explorar visualmente a gramática.

## <a name="formal-grammar"></a>Gramática formal

Podemos descrever o subconjunto da linguagem OData com suporte pelo Azure Search usando uma gramática EBNF ([Backus estendida-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)). As regras são listadas "de cima para baixo", começando com as expressões mais complexas e dividindo-as em expressões mais primitivas. Na parte superior estão as regras de gramática que correspondem a parâmetros específicos da API REST do Azure Search:

- [`$filter`](search-query-odata-filter.md), definido pela `filter_expression` regra.
- [`$orderby`](search-query-odata-orderby.md), definido pela `order_by_expression` regra.
- [`$select`](search-query-odata-select.md), definido pela `select_expression` regra.
- Caminhos de campo, definidos pela `field_path` regra. Os caminhos de campo são usados em toda a API. Eles podem se referir a campos de nível superior de um índice ou subcampos com um ou mais ancestrais de [campo complexo](search-howto-complex-data-types.md) .

Depois que EBNF é um [diagrama de sintaxe](https://en.wikipedia.org/wiki/Syntax_diagram) navegável que permite explorar interativamente a gramática e as relações entre suas regras.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>Diagrama de sintaxe

Para explorar visualmente a gramática de idioma do OData com suporte pelo Azure Search, experimente o diagrama de sintaxe interativa:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Azure Search](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Consulte também  

- [Filtros no Azure Search](search-filters.md)
- [Pesquisar documentos &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Sintaxe de consulta Lucene](query-lucene-syntax.md)
- [Sintaxe de consulta simples no Azure Search](query-simple-syntax.md)
