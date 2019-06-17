---
title: Descrição geral do idioma OData - Azure Search
description: Descrição geral da linguagem de OData para filtros, selecione e ordem-by para consultas de pesquisa do Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 166c23088fe0388199ca51efde05153bb5697e38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063693"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>Descrição geral da linguagem de OData para `$filter`, `$orderby`, e `$select` no Azure Search

O Azure Search suporta um subconjunto da sintaxe da expressão OData para **$filter**, **$orderby**, e **$select** expressões. Expressões de filtro são avaliadas durante a consulta de análise, a restringir a pesquisa a campos específicos ou a adicionar critérios de correspondência utilizados durante as análises de índice. Expressões order by são aplicadas como um passo de pós-processamento ao longo de um conjunto de resultados para ordenar os documentos que são devolvidos. Selecionadas expressões determinam quais campos de documentos estão incluídos no conjunto de resultados. A sintaxe dessas expressões é diferente do [simples](query-simple-syntax.md) ou [completo](query-lucene-syntax.md) sintaxe que é utilizada na consulta o **pesquisa** parâmetro, embora haja uma sobreposição na sintaxe para referenciar campos.

Este artigo fornece uma descrição geral da linguagem de expressão OData usada em filtros, por ordem e selecionadas expressões. O idioma é apresentado "baixo para cima", começando com os elementos mais básicos e criação neles. A sintaxe de nível superior para cada parâmetro é descrita num artigo separado:

- [sintaxe de $filter](search-query-odata-filter.md)
- [sintaxe de $orderby](search-query-odata-orderby.md)
- [sintaxe de $select](search-query-odata-select.md)

Intervalo de expressões de OData de simples a altamente complexas, mas eles todos partilham elementos comuns. As partes mais básicas de uma expressão de OData na pesquisa do Azure são:

- **Caminhos de campo**, que se referem a campos específicos do seu índice.
- **Constantes**, que são valores literais de um determinado tipo de dados.

> [!NOTE]
> Terminologia do Azure Search é diferente da [padrão OData](https://www.odata.org/documentation/) de diversas formas. O que chamamos uma **campo** no Azure Search é chamado um **propriedade** OData e da mesma forma para **caminho do campo** versus **caminho de propriedade**. Uma **índice** que contém **documentos** no Azure Search é referido em mais geral em OData como uma **conjunto de entidades** contendo **entidades**. A terminologia do Azure Search é utilizada em toda esta referência.

## <a name="field-paths"></a>Caminhos de campo

A seguinte EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de caminhos de campo.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

Um caminho do campo é composto por um ou mais **identificadores** separados por barras. Cada identificador é uma seqüência de caracteres que tem de começar com uma letra de ASCII ou caráter de sublinhado e conter apenas ASCII letras, dígitos ou carateres de sublinhado. As letras podem ser superior ou em minúsculas.

Um identificador pode referir-se até o nome de um campo, ou para um **variável de alcance** no contexto de um [expressão de coleção](search-query-odata-collection-operators.md) (`any` ou `all`) num filtro. Uma variável de alcance é como uma variável de loop que representa o elemento atual da coleção. Para coleções complexas, essa variável representa um objeto, que é por isso que pode usar os caminhos de campo para referenciar campos secundárias da variável. Isso é análogo a notação de ponto em várias linguagens de programação.

Exemplos de caminhos de campo são mostrados na tabela a seguir:

| Caminho do campo | Descrição |
| --- | --- |
| `HotelName` | Refere-se a um campo de nível superior do índice |
| `Address/City` | Refere-se para o `City` inferiores campo de um campo complexo no índice; `Address` é do tipo `Edm.ComplexType` neste exemplo |
| `Rooms/Type` | Refere-se para o `Type` inferiores campo de um campo de coleção complexa no índice; `Rooms` é do tipo `Collection(Edm.ComplexType)` neste exemplo |
| `Stores/Address/Country` | Refere-se para o `Country` inferiores campo o `Address` inferiores campo de um campo de coleção complexa no índice; `Stores` é do tipo `Collection(Edm.ComplexType)` e `Address` é do tipo `Edm.ComplexType` neste exemplo |
| `room/Type` | Refere-se para o `Type` inferiores campo o `room` variável de alcance, por exemplo, da expressão de filtro `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Refere-se para o `Country` inferiores campo do `Address` inferiores campo o `store` variável de alcance, por exemplo, da expressão de filtro `Stores/any(store: store/Address/Country eq 'Canada')` |

O significado de um caminho de campo difere consoante o contexto. Filtros, um caminho do campo refere-se para o valor de um *única instância* de um campo no documento atual. Em outros contextos, tal como **$orderby**, **$select**, ou no [respondida pesquisa na sintaxe Lucene completa](query-lucene-syntax.md#bkmk_fields), um caminho do campo refere-se para o campo em si. Essa diferença possui algumas conseqüências de como utilizar caminhos de campo em filtros.

Considere o caminho do campo `Address/City`. Um filtro, isso se refere a uma cidade única para o documento atual, como "San Francisco". Por outro lado, `Rooms/Type` refere-se para o `Type` inferiores campo para ambientes de muitas (como "padrão" para a sala primeiro, "deluxe" para o segundo espaço e assim por diante). Uma vez que `Rooms/Type` não fazer referência a uma *instância única* do campo secundárias `Type`, não pode ser utilizado diretamente num filtro. Em vez disso, para filtrar no tipo de espaço, usaria uma [expressão lambda](search-query-odata-collection-operators.md) com uma variável de alcance, como este:

    Rooms/any(room: room/Type eq 'deluxe')

Neste exemplo, a variável de alcance `room` aparece no `room/Type` caminho do campo. Dessa forma, `room/Type` refere-se para o tipo da sala atual no documento atual. Esta é uma única instância do `Type` secundárias campo, para que possa ser utilizada diretamente no filtro.

### <a name="using-field-paths"></a>Usando caminhos de campo

Caminhos de campo são utilizados em vários parâmetros do [API do Azure Search](https://docs.microsoft.com/rest/api/searchservice/). A tabela seguinte apresenta uma lista de todos os locais onde eles podem ser usados, além de quaisquer restrições na respetiva utilização:

| API | Nome do parâmetro | Restrições |
| --- | --- | --- |
| [Crie](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) índice | `suggesters/sourceFields` | Nenhuma |
| [Crie](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) índice | `scoringProfiles/text/weights` | Só podem referenciar **pesquisável** campos |
| [Crie](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) índice | `scoringProfiles/functions/fieldName` | Só podem referenciar **filtráveis** campos |
| [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` Quando `queryType` é `full` | Só podem referenciar **pesquisável** campos |
| [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Só podem referenciar **facetável** campos |
| [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Só podem referenciar **pesquisável** campos |
| [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Só podem referenciar **pesquisável** campos |
| [Sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions) e [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Só podem referenciar campos que fazem parte de um [sugestor](index-add-suggesters.md) |
| [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents), [sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions), e [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Só podem referenciar **filtráveis** campos |
| [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) e [Sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Só podem referenciar **ordenável** campos |
| [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents), [sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions), e [pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Só podem referenciar **recuperável** campos |

## <a name="constants"></a>Constantes

Constantes em OData são valores literais de um determinado [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) tipo (EDM). Ver [tipos de dados suportados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para obter uma lista dos tipos suportados no Azure Search. Não são suportadas constantes de tipos de coleção.

A tabela seguinte mostra exemplos de constantes para cada um dos tipos de dados suportados pelo Azure Search:

| Tipo de dados | Constantes de exemplo |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

A seguinte EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática para a maioria das constantes mostradas na tabela acima. A gramática para os tipos geoespaciais pode ser encontrada na [OData funções geoespaciais no Azure Search](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
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
```

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

## <a name="building-expressions-from-field-paths-and-constants"></a>Expressões de criação de caminhos de campo e constantes

Caminhos de campo e constantes são a parte mais básica de uma expressão de OData, mas eles já estão expressões completas propriamente ditas. Na verdade, o **$select** parâmetro no Azure Search é nada além de uma lista separada por vírgulas de caminhos de campo, e **$orderby** não é muito mais complicado que **$select**. Se tiver um campo do tipo `Edm.Boolean` no seu índice, pode até escrever um filtro que não é nada, mas o caminho do campo. As constantes `true` e `false` são da mesma forma filtros válidos.

No entanto, na maioria das vezes precisará expressões mais complexas que se referem a mais de um campo e constante. Estas expressões são criadas de diversas formas consoante o parâmetro.

A seguinte EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática para o **$filter**, **$orderby**, e **$select** parâmetros. Estes são criados a partir de expressões mais simples que se referem a caminhos de campo e constantes:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

O **$orderby** e **$select** parâmetros são as duas listas separados por vírgulas das expressões mais simples. O **$filter** parâmetro é uma expressão booleana que é composta de subexpressões mais simples. Estes as subexpressões são combinadas com operadores lógicos como [ `and`, `or`, e `not` ](search-query-odata-logical-operators.md), operadores de comparação, tais como [ `eq`, `lt`, `gt`, e assim por diante](search-query-odata-comparison-operators.md)e operadores de coleção, tais como [ `any` e `all` ](search-query-odata-collection-operators.md).

O **$filter**, **$orderby**, e **$select** parâmetros são explorados detalhadamente mais nos seguintes artigos:

- [Sintaxe de OData $filter no Azure Search](search-query-odata-filter.md)
- [Sintaxe de OData $orderby no Azure Search](search-query-odata-orderby.md)
- [Sintaxe de OData $select no Azure Search](search-query-odata-select.md)

## <a name="see-also"></a>Consulte também  

- [Navegação por facetas no Azure Search](search-faceted-navigation.md)
- [Filtros no Azure Search](search-filters.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Sintaxe de consulta Lucene](query-lucene-syntax.md)
- [Sintaxe de consulta simples no Azure Search](query-simple-syntax.md)
