---
title: Referência sintaxe de expressão OData
titleSuffix: Azure Cognitive Search
description: Especificação formal de gramática e sintaxe para expressões OData em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: f3422fd10e062ae87bc165491e0d01ac2b4943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793240"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure

A Pesquisa Cognitiva Azure utiliza [expressões OData](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) como parâmetros em toda a API. Mais frequentemente, as expressões OData são usadas para os `$orderby` e `$filter` parâmetros. Estas expressões podem ser complexas, contendo várias cláusulas, funções e operadores. No entanto, mesmo expressões simples de OData como caminhos de propriedade são usadas em muitas partes da API de pesquisa cognitiva Azure. Por exemplo, as expressões de caminho supõem-se para se referir em subcampos de campos complexos em todo `$select` o lado na API, como quando se listam subcampos num [sugestor](index-add-suggesters.md), uma [função de pontuação,](index-add-scoring-profiles.md)o parâmetro ou mesmo a [procura em consultas lucene.](query-lucene-syntax.md)

Este artigo descreve todas estas formas de expressões OData usando uma gramática formal. Há também um [diagrama interativo](#syntax-diagram) para ajudar a explorar visualmente a gramática.

## <a name="formal-grammar"></a>Gramática formal

Podemos descrever o subconjunto da linguagem OData suportada pela Azure Cognitive Search usando uma gramática EBNF (Forma Estendida de[Backus-Naur).](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) As regras são listadas como "top-down", começando pelas expressões mais complexas, e dividindo-as em expressões mais primitivas. No topo estão as regras gramaticais que correspondem a parâmetros específicos da API de Pesquisa Cognitiva Azure:

- [`$filter`](search-query-odata-filter.md), definido `filter_expression` pela regra.
- [`$orderby`](search-query-odata-orderby.md), definido `order_by_expression` pela regra.
- [`$select`](search-query-odata-select.md), definido `select_expression` pela regra.
- Caminhos de `field_path` campo, definidos pela regra. Os caminhos de campo são utilizados em toda a API. Podem referir-se a campos de alto nível de um índice, ou sub-campos com um ou mais [ancestrais de campo complexos.](search-howto-complex-data-types.md)

Depois do EBNF é um diagrama de [sintaxe](https://en.wikipedia.org/wiki/Syntax_diagram) viável que lhe permite explorar interativamente a gramática e as relações entre as suas regras.

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

Para explorar visualmente a gramática da linguagem OData suportada pela Pesquisa Cognitiva Azure, experimente o diagrama de sintaxe interativa:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Consulte também  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Sintaxe de consulta Lucene](query-lucene-syntax.md)
- [Sintaxe de consulta simples em Pesquisa Cognitiva Azure](query-simple-syntax.md)
