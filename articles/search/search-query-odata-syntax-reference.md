---
title: Referência de sintaxe da expressão OData - Azure Search
description: Formal especificação de gramática e de sintaxe para expressões de OData em consultas de pesquisa do Azure.
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
ms.openlocfilehash: cccfb749af07d1deeeda6e94de9c2cd5ce5396f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079668"
---
# <a name="odata-expression-syntax-reference-for-azure-search"></a>Referência de sintaxe de expressão OData para o Azure Search

Utiliza o Azure Search [expressões de OData](http://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) como parâmetros por toda a API. Normalmente, as expressões de OData são utilizadas para o `$orderby` e `$filter` parâmetros. Essas expressões podem ser complexos, contendo várias cláusulas, funções e operadores. No entanto, as expressões de OData até mesmo simples, como propriedade caminhos são utilizados em muitas partes da API de REST da Azure Search. Por exemplo, as expressões de caminho são utilizadas para fazer referência a campos secundárias dos campos complexos em qualquer lugar na API, tal como quando lista campos inferiores numa [sugestor](index-add-suggesters.md), uma [função de classificação](index-add-scoring-profiles.md), o `$select` parâmetro , ou até mesmo [respondida pesquisa nas consultas de Lucene](query-lucene-syntax.md).

Este artigo descreve todos esses formulários de expressões de OData, usando uma gramática formal. Há também uma [diagrama interativo](#syntax-diagram) para ajudar a explorar visualmente a gramática.

## <a name="formal-grammar"></a>Gramática formal

Podemos descrever o subconjunto da linguagem de OData suportada pelo Azure Search utilizando um EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) gramática. Regras estão listadas "cima para baixo", começando com as expressões mais complexas e dividindo-los em expressões mais primitivas. Na parte superior são as regras de gramática que correspondem aos parâmetros específicos da API de REST de pesquisa do Azure:

- [`$filter`](search-query-odata-filter.md), definido pelo `filter_expression` regra.
- [`$orderby`](search-query-odata-orderby.md), definido pelo `order_by_expression` regra.
- [`$select`](search-query-odata-select.md), definido pelo `select_expression` regra.
- Campo caminhos, definidos pelo `field_path` regra. Caminhos de campo são utilizados em toda a API. Eles podem fazer referência a qualquer um dos campos de nível superior de um índice, ou secundária campos com uma ou mais [campo complexo](search-howto-complex-data-types.md) predecessores.

Depois da EBNF é um passível de pesquisa [diagrama da sintaxe](https://en.wikipedia.org/wiki/Syntax_diagram) que permite-lhe explorar interativamente a gramática e as relações entre suas regras.

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

## <a name="syntax-diagram"></a>Diagrama da sintaxe

Para explorar visualmente a gramática de linguagem de OData suportada pelo Azure Search, experimente o diagrama da sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Consulte também  

- [Filtros no Azure Search](search-filters.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Sintaxe de consulta Lucene](query-lucene-syntax.md)
- [Sintaxe de consulta simples no Azure Search](query-simple-syntax.md)
