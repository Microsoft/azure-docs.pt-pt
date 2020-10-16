---
title: Visão geral da linguagem OData
titleSuffix: Azure Cognitive Search
description: Visão geral do idioma OData para filtros, selecione e encomenda para consultas de Pesquisa Cognitiva Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: d04311fce81d147a0830918aee1d4a2a9c0808d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923403"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Visão geral da linguagem OData `$filter` `$orderby` para, e `$select` em Azure Cognitive Search

A Azure Cognitive Search suporta um subconjunto da sintaxe de expressão OData para **$filter**, **$orderby**e **expressões $select.** As expressões de filtro são avaliadas durante a análise de consulta, limitando a procura a campos específicos ou adicionando critérios de correspondência utilizados durante as análises de índices. As expressões de encomenda são aplicadas como um passo pós-processamento sobre um conjunto de resultados para classificar os documentos que são devolvidos. As expressões selecionadas determinam quais os campos de documentos incluídos no conjunto de resultados. A sintaxe destas expressões é distinta da sintaxe de consulta [simples](query-simple-syntax.md) ou [completa](query-lucene-syntax.md) que é usada no parâmetro **de pesquisa,** embora haja alguma sobreposição na sintaxe para campos de referência.

Este artigo fornece uma visão geral da linguagem de expressão OData utilizada em filtros, encomendas e expressões selecionadas. A linguagem é apresentada "de baixo para cima", começando pelos elementos mais básicos e baseando-se neles. A sintaxe de nível superior para cada parâmetro é descrita num artigo separado:

- [sintaxe $filter](search-query-odata-filter.md)
- [sintaxe $orderby](search-query-odata-orderby.md)
- [sintaxe $select](search-query-odata-select.md)

As expressões OData variam de simples a altamente complexas, mas todas partilham elementos comuns. As partes mais básicas de uma expressão OData na Pesquisa Cognitiva Azure são:

- **Caminhos de**campo, que se referem a campos específicos do seu índice.
- **Constantes,** que são valores literais de um determinado tipo de dados.

> [!NOTE]
> A terminologia na Pesquisa Cognitiva Azure difere do [padrão OData](https://www.odata.org/documentation/) de algumas maneiras. O que chamamos de **campo** em Azure Cognitive Search é chamado de **propriedade** em OData, e similarmente para **caminho de campo** versus **propriedade.** Um **índice** que contém **documentos** na Pesquisa Cognitiva Azure é referido de forma mais geral no OData como um **conjunto de entidades** que contêm **entidades.** A terminologia de Pesquisa Cognitiva Azure é usada ao longo desta referência.

## <a name="field-paths"></a>Caminhos de campo

O seguinte EBNF[(Formulário Backus-Naur Alargado)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática dos caminhos de campo.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

Um caminho de campo é composto por um ou mais **identificadores separados** por cortes. Cada identificador é uma sequência de caracteres que deve começar com uma letra ASCII ou sublinhar, e conter apenas letras, dígitos ou sublinhados ASCII. As letras podem ser maiúsculas ou minúsculas.

Um identificador pode referir-se ao nome de um campo, ou a uma **variável** de alcance no contexto de uma [expressão](search-query-odata-collection-operators.md) de recolha `any` `all` (ou) num filtro. Uma variável de gama é como uma variável de loop que representa o elemento atual da coleção. Para coleções complexas, esta variável representa um objeto, razão pela qual pode usar caminhos de campo para se referir a sub-campos da variável. Isto é análogo à notação de pontos em muitas linguagens de programação.

Exemplos de percursos de campo são mostrados na tabela seguinte:

| Caminho de campo | Descrição |
| --- | --- |
| `HotelName` | Refere-se a um campo de alto nível do índice |
| `Address/City` | Refere-se ao `City` sub-campo de um campo complexo no índice; `Address` é de tipo neste `Edm.ComplexType` exemplo |
| `Rooms/Type` | Refere-se ao `Type` sub-campo de um campo de recolha complexo no índice; `Rooms` é de tipo neste `Collection(Edm.ComplexType)` exemplo |
| `Stores/Address/Country` | Refere-se ao `Country` sub-campo do `Address` sub-campo de um campo de recolha complexo no índice; `Stores` é de tipo e é de tipo neste `Collection(Edm.ComplexType)` `Address` `Edm.ComplexType` exemplo |
| `room/Type` | Refere-se ao `Type` sub-campo da `room` variável de gama, por exemplo na expressão do filtro `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Refere-se ao `Country` sub-campo do `Address` sub-campo da variável de `store` gama, por exemplo na expressão do filtro `Stores/any(store: store/Address/Country eq 'Canada')` |

O significado de um caminho de campo difere dependendo do contexto. Nos filtros, um caminho de campo refere-se ao valor de uma *única instância* de um campo no documento atual. Em outros contextos, como **$orderby,** **$select**, ou em [busca de campo na sintaxe lucene completa,](query-lucene-syntax.md#bkmk_fields)um caminho de campo refere-se ao próprio campo. Esta diferença tem algumas consequências na forma como utiliza caminhos de campo em filtros.

Considere o caminho de `Address/City` campo. Num filtro, isto refere-se a uma única cidade para o documento atual, como "São Francisco". Em contraste, `Rooms/Type` `Type` refere-se ao sub-campo para muitos quartos (como "standard" para o primeiro quarto, "deluxe" para a segunda sala, e assim por diante). Uma `Rooms/Type` vez que não se refere a um único *caso* do sub-campo, não pode ser `Type` usado diretamente num filtro. Em vez disso, para filtrar no tipo de quarto, você usaria uma [expressão lambda](search-query-odata-collection-operators.md) com uma variável de gama, como esta:

```odata
Rooms/any(room: room/Type eq 'deluxe')
```

Neste exemplo, a variável de alcance `room` aparece no caminho de `room/Type` campo. Desta forma, `room/Type` refere-se ao tipo de sala atual no documento atual. Este é um único exemplo do `Type` sub-campo, para que possa ser usado diretamente no filtro.

### <a name="using-field-paths"></a>Usando caminhos de campo

Os caminhos de campo são utilizados em muitos parâmetros das APIs de [Repouso cognitivo Azure](/rest/api/searchservice/). A tabela a seguir lista todos os locais onde podem ser utilizados, além de quaisquer restrições à sua utilização:

| API | Nome do parâmetro | Restrições |
| --- | --- | --- |
| [Criar](/rest/api/searchservice/create-index) ou [atualizar](/rest/api/searchservice/update-index) índice | `suggesters/sourceFields` | Nenhum |
| [Criar](/rest/api/searchservice/create-index) ou [atualizar](/rest/api/searchservice/update-index) índice | `scoringProfiles/text/weights` | Só pode referir-se a campos **pesjáveis** |
| [Criar](/rest/api/searchservice/create-index) ou [atualizar](/rest/api/searchservice/update-index) índice | `scoringProfiles/functions/fieldName` | Só pode referir-se a campos **filtrados** |
| [Pesquisa](/rest/api/searchservice/search-documents) | `search` quando `queryType` é `full` | Só pode referir-se a campos **pesjáveis** |
| [Pesquisa](/rest/api/searchservice/search-documents) | `facet` | Só pode se referir a campos **facetais** |
| [Pesquisa](/rest/api/searchservice/search-documents) | `highlight` | Só pode referir-se a campos **pesjáveis** |
| [Pesquisa](/rest/api/searchservice/search-documents) | `searchFields` | Só pode referir-se a campos **pesjáveis** |
| [Sugerir](/rest/api/searchservice/suggestions) e [Autocompleto](/rest/api/searchservice/autocomplete) | `searchFields` | Só pode referir-se a campos que fazem parte de um [sugestivo](index-add-suggesters.md) |
| [Pesquisar](/rest/api/searchservice/search-documents), [sugerir](/rest/api/searchservice/suggestions)e [autocompleto](/rest/api/searchservice/autocomplete) | `$filter` | Só pode referir-se a campos **filtrados** |
| [Pesquisar](/rest/api/searchservice/search-documents) e [Sugerir](/rest/api/searchservice/suggestions) | `$orderby` | Só pode se referir a campos **ordenados** |
| [Pesquisar](/rest/api/searchservice/search-documents), [Sugerir](/rest/api/searchservice/suggestions)e [Procurar](/rest/api/searchservice/lookup-document) | `$select` | Só pode se referir a campos **recuperáveis** |

## <a name="constants"></a>Constantes

As constantes no OData são valores literais de um determinado [modelo de dados](/dotnet/framework/data/adonet/entity-data-model) da entidade (EDM). Consulte [os tipos de dados suportados](/rest/api/searchservice/supported-data-types) para uma lista de tipos suportados na Pesquisa Cognitiva Azure. As constantes dos tipos de recolha não são suportadas.

A tabela a seguir mostra exemplos de constantes para cada um dos tipos de dados suportados pela Azure Cognitive Search:

| Tipo de dados | Exemplo de constantes |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Escapando de personagens especiais em constantes de cordas

As constantes de corda no OData são delimitadas por cotações únicas. Se precisar de construir uma consulta com uma constante de corda que possa conter citações individuais, pode escapar às citações incorporadas duplicando-as.

Por exemplo, uma frase com um apóstrofo nãoformado como "O carro de Alice" seria representada no OData como a constante de cordas `'Alice''s car'` .

> [!IMPORTANT]
> Ao construir filtros programáticamente, é importante lembrar-se de escapar às constantes de cordas que vêm da entrada do utilizador. Isto é para mitigar a possibilidade de [ataques](https://wikipedia.org/wiki/SQL_injection)de injeção , especialmente quando se utilizam filtros para implementar [aparas de segurança.](search-security-trimming-for-azure-search.md)

### <a name="constants-syntax"></a>Sintaxe de constantes

O seguinte EBNF[(Formulário Backus-Naur Alargado)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática para a maioria das constantes mostradas na tabela acima. A gramática para tipos geo-espaciais pode ser encontrada em [funções geo-espaciais OData em Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).

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

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

## <a name="building-expressions-from-field-paths-and-constants"></a>Construindo expressões de caminhos de campo e constantes

Os caminhos de campo e as constantes são a parte mais básica de uma expressão OData, mas já são expressões completas. Na verdade, o parâmetro **$select** na Pesquisa Cognitiva de Azure não passa de uma lista separada por vírgulas de caminhos de campo, e **$orderby** não é muito mais complicado do que **$select**. Se por acaso tiver um campo de tipo `Edm.Boolean` no seu índice, pode até escrever um filtro que não passa do caminho desse campo. As constantes `true` e `false` são também filtros válidos.

No entanto, a maior parte do tempo você precisará de expressões mais complexas que se referem a mais de um campo e constante. Estas expressões são construídas de diferentes formas dependendo do parâmetro.

O seguinte EBNF[(Formulário Backus-Naur Alargado)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática para os **parâmetros $filter**, **$orderby**e **$select.** Estes são construídos a partir de expressões mais simples que se referem a caminhos de campo e constantes:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

Os **parâmetros $orderby** e **$select** são ambas listas separadas por vírgulas de expressões mais simples. O **parâmetro $filter** é uma expressão booleana que é composta por subexpressões mais simples. Estas subexpressões são combinadas com operadores lógicos [ `and` `or` como, e, `not` ](search-query-odata-logical-operators.md)operadores de comparação [ `eq` `lt` como, e assim por `gt` diante,](search-query-odata-comparison-operators.md)e operadores de recolha como [ `any` e `all` ](search-query-odata-collection-operators.md).

Os **parâmetros $filter**, **$orderby**e **$select** são explorados mais detalhadamente nos seguintes artigos:

- [OData $filter sintaxe na pesquisa cognitiva de Azure](search-query-odata-filter.md)
- [OData $orderby sintaxe na pesquisa cognitiva de Azure](search-query-odata-orderby.md)
- [OData $select sintaxe na pesquisa cognitiva do Azure](search-query-odata-select.md)

## <a name="see-also"></a>Consulte também  

- [Navegação facetada em Pesquisa Cognitiva Azure](search-faceted-navigation.md)
- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Sintaxe de consulta Lucene](query-lucene-syntax.md)
- [Sintaxe de consulta simples na Pesquisa Cognitiva Azure](query-simple-syntax.md)