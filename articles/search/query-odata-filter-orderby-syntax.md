---
title: Visão geral da linguagem OData
titleSuffix: Azure Cognitive Search
description: Visão geral da linguagem OData para filtros, selecionar e ordenar para consultas do Azure Pesquisa Cognitiva.
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
ms.openlocfilehash: e0db41098287ff011416932a0d44a1cb9f76127d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786166"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Visão geral da linguagem OData para `$filter`, `$orderby`e `$select` no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva dá suporte a um subconjunto da sintaxe de expressão OData para expressões **$Filter**, **$OrderBy**e **$Select** . As expressões de filtro são avaliadas durante a análise de consulta, restringindo a pesquisa a campos específicos ou adicionando critérios de correspondência usados durante verificações de índice. As expressões order-by são aplicadas como uma etapa de pós-processamento em um conjunto de resultados para classificar os documentos que são retornados. As expressões Select determinam quais campos de documento são incluídos no conjunto de resultados. A sintaxe dessas expressões é distinta da sintaxe de consulta [simples](query-simple-syntax.md) ou [completa](query-lucene-syntax.md) usada no parâmetro de **pesquisa** , embora haja sobreposição na sintaxe para fazer referência a campos.

Este artigo fornece uma visão geral da linguagem de expressão OData usada em filtros, pedidos e expressões SELECT. O idioma é apresentado "de baixo para cima", começando com os elementos mais básicos e criando-os. A sintaxe de nível superior para cada parâmetro é descrita em um artigo separado:

- [sintaxe de $filter](search-query-odata-filter.md)
- [sintaxe de $orderby](search-query-odata-orderby.md)
- [sintaxe de $select](search-query-odata-select.md)

As expressões OData variam de simples a altamente complexa, mas todas compartilham elementos comuns. As partes mais básicas de uma expressão OData no Azure Pesquisa Cognitiva são:

- **Caminhos de campo**, que se referem a campos específicos do índice.
- **Constantes**, que são valores literais de um determinado tipo de dados.

> [!NOTE]
> A terminologia no Azure Pesquisa Cognitiva difere do [padrão OData](https://www.odata.org/documentation/) de algumas maneiras. O que chamamos de **campo** na pesquisa cognitiva do Azure é chamado **de propriedade** em OData e, da mesma forma, para **caminho de campo** versus caminho de **Propriedade**. Um **índice** que contém **documentos** no Azure pesquisa cognitiva é chamado mais geralmente no OData como um **conjunto de entidades** que contém **entidades**. A terminologia do Pesquisa Cognitiva do Azure é usada em toda essa referência.

## <a name="field-paths"></a>Caminhos de campo

O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de caminhos de campo.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Um caminho de campo é composto por um ou mais **identificadores** separados por barras. Cada identificador é uma sequência de caracteres que deve começar com uma letra ASCII ou sublinhado e conter apenas letras ASCII, dígitos ou sublinhados. As letras podem ser maiúsculas ou minúsculas.

Um identificador pode se referir ao nome de um campo ou a uma **variável de intervalo** no contexto de uma [expressão de coleta](search-query-odata-collection-operators.md) (`any` ou `all`) em um filtro. Uma variável de intervalo é como uma variável de loop que representa o elemento atual da coleção. Para coleções complexas, essa variável representa um objeto, que é o motivo pelo qual você pode usar caminhos de campo para fazer referência a subcampos da variável. Isso é análogo à notação de ponto em muitas linguagens de programação.

Exemplos de caminhos de campo são mostrados na tabela a seguir:

| Caminho do campo | Descrição |
| --- | --- |
| `HotelName` | Refere-se a um campo de nível superior do índice |
| `Address/City` | Refere-se ao subcampo `City` de um campo complexo no índice; `Address` é do tipo `Edm.ComplexType` neste exemplo |
| `Rooms/Type` | Refere-se ao subcampo `Type` de um campo de coleção complexo no índice; `Rooms` é do tipo `Collection(Edm.ComplexType)` neste exemplo |
| `Stores/Address/Country` | Refere-se ao subcampo `Country` do subcampo `Address` de um campo de coleção complexo no índice; `Stores` é do tipo `Collection(Edm.ComplexType)` e `Address` é do tipo `Edm.ComplexType` neste exemplo |
| `room/Type` | Refere-se ao subcampo `Type` da variável de intervalo de `room`, por exemplo, na expressão de filtro `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Refere-se ao subcampo `Country` do subcampo `Address` da variável de intervalo `store`, por exemplo, na expressão de filtro `Stores/any(store: store/Address/Country eq 'Canada')` |

O significado de um caminho de campo difere dependendo do contexto. Em filtros, um caminho de campo refere-se ao valor de uma *única instância* de um campo no documento atual. Em outros contextos, como **$OrderBy**, **$Select**ou na pesquisa de [campo na sintaxe Lucene completa](query-lucene-syntax.md#bkmk_fields), um caminho de campo se refere ao próprio campo. Essa diferença tem algumas conseqüências para a forma como você usa caminhos de campo em filtros.

Considere o caminho do campo `Address/City`. Em um filtro, isso se refere a uma única cidade para o documento atual, como "San Francisco". Por outro lado, `Rooms/Type` se refere ao subcampo `Type` para muitas salas (como "Standard" na primeira sala, "Deluxe" para a segunda sala e assim por diante). Como `Rooms/Type` não se refere a uma *única instância* do subcampo `Type`, ela não pode ser usada diretamente em um filtro. Em vez disso, para filtrar o tipo de sala, você usaria uma [expressão lambda](search-query-odata-collection-operators.md) com uma variável de intervalo, desta forma:

    Rooms/any(room: room/Type eq 'deluxe')

Neste exemplo, a variável de intervalo `room` aparece no caminho do campo de `room/Type`. Dessa forma, `room/Type` se refere ao tipo da sala atual no documento atual. Essa é uma única instância do subcampo `Type`, portanto, ele pode ser usado diretamente no filtro.

### <a name="using-field-paths"></a>Usando caminhos de campo

Os caminhos de campo são usados em muitos parâmetros das [APIs REST do Azure pesquisa cognitiva](https://docs.microsoft.com/rest/api/searchservice/). A tabela a seguir lista todos os locais em que eles podem ser usados, além de quaisquer restrições sobre seu uso:

| API | Nome do parâmetro | Restrições |
| --- | --- | --- |
| [Criar](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Atualizar](https://docs.microsoft.com/rest/api/searchservice/update-index) índice | `suggesters/sourceFields` | Nenhuma |
| [Criar](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Atualizar](https://docs.microsoft.com/rest/api/searchservice/update-index) índice | `scoringProfiles/text/weights` | Só pode fazer referência a campos **pesquisáveis** |
| [Criar](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Atualizar](https://docs.microsoft.com/rest/api/searchservice/update-index) índice | `scoringProfiles/functions/fieldName` | Só pode fazer referência a campos **filtráveis** |
| [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` quando `queryType` é `full` | Só pode fazer referência a campos **pesquisáveis** |
| [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Só pode fazer referência aos campos de **facetable** |
| [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Só pode fazer referência a campos **pesquisáveis** |
| [Pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Só pode fazer referência a campos **pesquisáveis** |
| [Sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions) e [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Só pode fazer referência a campos que fazem parte de um [Sugestor](index-add-suggesters.md) |
| [Pesquisar](https://docs.microsoft.com/rest/api/searchservice/search-documents), [sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions)e [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Só pode fazer referência a campos **filtráveis** |
| [Pesquisar](https://docs.microsoft.com/rest/api/searchservice/search-documents) e [sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Só pode fazer referência a campos **classificável** |
| [Pesquisar](https://docs.microsoft.com/rest/api/searchservice/search-documents), [sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions)e [Pesquisar](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Só pode fazer referência a campos **recuperáveis** |

## <a name="constants"></a>:

As constantes no OData são valores literais de um determinado tipo de [modelo de dados de entidade](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM). Consulte [tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para obter uma lista de tipos com suporte no Azure pesquisa cognitiva. Não há suporte para constantes de tipos de coleção.

A tabela a seguir mostra exemplos de constantes para cada um dos tipos de dados com suporte pelo Azure Pesquisa Cognitiva:

| Data type | Constantes de exemplo |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática para a maioria das constantes mostradas na tabela acima. A gramática para tipos geoespaciais pode ser encontrada em [funções geoespaciais OData no Azure pesquisa cognitiva](search-query-odata-geo-spatial-functions.md).

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

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

## <a name="building-expressions-from-field-paths-and-constants"></a>Criando expressões de caminhos de campo e constantes

Caminhos de campo e constantes são a parte mais básica de uma expressão OData, mas elas já são próprias expressões completas. Na verdade, o parâmetro **$Select** na pesquisa cognitiva do Azure não é nada, exceto uma lista separada por vírgulas de caminhos de campo, e **$OrderBy** não é muito mais complicado do que **$Select**. Se você tiver um campo do tipo `Edm.Boolean` no índice, poderá até mesmo escrever um filtro que não seja nada além do caminho desse campo. As constantes `true` e `false` são filtros válidos da mesma forma.

No entanto, na maioria das vezes você precisará de expressões mais complexas que se refiram a mais de um campo e constante. Essas expressões são criadas de maneiras diferentes, dependendo do parâmetro.

O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática para os parâmetros **$Filter**, **$OrderBy**e **$Select** . Elas são criadas a partir de expressões mais simples que se referem a caminhos e constantes de campo:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Os parâmetros **$OrderBy** e **$Select** são listas separadas por vírgulas de expressões mais simples. O parâmetro **$Filter** é uma expressão booliana composta por subexpressãos mais simples. Essas subexpressãos são combinadas usando operadores lógicos, como [`and`, `or`e `not`](search-query-odata-logical-operators.md), operadores de comparação como [`eq`, `lt`, `gt`e assim por diante](search-query-odata-comparison-operators.md), e operadores de coleção, como [`any` e `all`](search-query-odata-collection-operators.md).

Os parâmetros **$Filter**, **$OrderBy**e **$Select** são explorados com mais detalhes nos seguintes artigos:

- [Sintaxe de $filter OData no Azure Pesquisa Cognitiva](search-query-odata-filter.md)
- [Sintaxe de $orderby OData no Azure Pesquisa Cognitiva](search-query-odata-orderby.md)
- [Sintaxe de $select OData no Azure Pesquisa Cognitiva](search-query-odata-select.md)

## <a name="see-also"></a>Ver também  

- [Navegação facetada no Azure Pesquisa Cognitiva](search-faceted-navigation.md)
- [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
- [Pesquisar documentos &#40;do Azure pesquisa cognitiva API REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Sintaxe de consulta Lucene](query-lucene-syntax.md)
- [Sintaxe de consulta simples no Azure Pesquisa Cognitiva](query-simple-syntax.md)
