---
title: Visão geral da linguagem OData
titleSuffix: Azure Cognitive Search
description: Visão geral da linguagem OData para filtros, selecione e encomenda por consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: f3a1be435e297ab4a9ba7f8bfbd5f3ce3451d8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153881"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Visão geral da `$filter`linguagem `$orderby`OData para, e `$select` em Pesquisa Cognitiva Azure

A Azure Cognitive Search suporta um subconjunto da sintaxe de expressão OData para **$filter**, **$orderby**e **$select** expressões. As expressões de filtro são avaliadas durante a consulta, limitando a pesquisa a campos específicos ou adicionando critérios de correspondência usados durante as análises de índice. As expressões por encomenda são aplicadas como um passo pós-processamento sobre um resultado definido para classificar os documentos que são devolvidos. As expressões selecionadas determinam quais os campos de documentos incluídos no conjunto de resultados. A sintaxe destas expressões é distinta da sintaxe de consulta [simples](query-simple-syntax.md) ou [completa](query-lucene-syntax.md) que é usada no parâmetro de **pesquisa,** embora haja alguma sobreposição na sintaxe para campos de referenciação.

Este artigo fornece uma visão geral da linguagem de expressão OData utilizada em filtros, por encomenda e expressões selecionadas. A linguagem é apresentada "de baixo para cima", começando pelos elementos mais básicos e baseando-os. A sintaxe de alto nível para cada parâmetro é descrita num artigo separado:

- [$filter sintaxe](search-query-odata-filter.md)
- [$orderby sintaxe](search-query-odata-orderby.md)
- [sintaxe $select](search-query-odata-select.md)

As expressões OData variam de simples a altamente complexas, mas todas partilham elementos comuns. As partes mais básicas de uma expressão OData em Pesquisa Cognitiva Azure são:

- **Caminhos**de campo, que se referem a campos específicos do seu índice.
- **Constantes,** que são valores literais de um determinado tipo de dados.

> [!NOTE]
> Terminologia em Pesquisa Cognitiva Azure difere do [padrão OData](https://www.odata.org/documentation/) de algumas maneiras. O que chamamos de **campo** em Azure Cognitive Search é chamado de **propriedade** em OData, e similarmente para caminho de **campo** versus caminho de **propriedade**. Um **índice** que contém **documentos** em Pesquisa Cognitiva Azure é referido mais genericamente no OData como um **conjunto de entidades** que contém **entidades.** A terminologia de Pesquisa Cognitiva Azure é utilizada ao longo desta referência.

## <a name="field-paths"></a>Caminhos de campo

O seguinte EBNF ( Forma Estendida de[Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática dos caminhos de campo.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Um percurso de campo é composto por um ou mais **identificadores separados** por cortes. Cada identificador é uma sequência de caracteres que deve começar com uma letra ASCII ou sublinhar, e conter apenas letras, dígitos ou sublinhados ASCII. As letras podem ser maiúsculas ou inferiores.

Um identificador pode referir-se quer ao nome de um campo, quer`any` a `all`uma **variável** de gama no contexto de uma expressão de [recolha](search-query-odata-collection-operators.md) ( ou ) num filtro. Uma variável de gama é como uma variável de loop que representa o elemento atual da coleção. Para coleções complexas, essa variável representa um objeto, razão pela qual você pode usar caminhos de campo para se referir a sub-campos da variável. Isto é análogo a notar pontos em muitas linguagens de programação.

Exemplos de percursos de campo são mostrados na tabela seguinte:

| Caminho de campo | Descrição |
| --- | --- |
| `HotelName` | Refere-se a um campo de alto nível do índice |
| `Address/City` | Refere-se `City` ao subcampo de um campo complexo no índice; `Address` é de `Edm.ComplexType` tipo neste exemplo |
| `Rooms/Type` | Refere-se `Type` ao subcampo de um complexo campo de recolha no índice; `Rooms` é de `Collection(Edm.ComplexType)` tipo neste exemplo |
| `Stores/Address/Country` | Refere-se `Country` ao subcampo `Address` do subcampo de um complexo campo de recolha no índice; `Stores` é de `Collection(Edm.ComplexType)` `Address` tipo e `Edm.ComplexType` é de tipo neste exemplo |
| `room/Type` | Refere-se `Type` ao sub-campo `room` da variável gama, por exemplo na expressão do filtro`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Refere-se `Country` ao subcampo `Address` do subcampo `store` da variável gama, por exemplo na expressão do filtro`Stores/any(store: store/Address/Country eq 'Canada')` |

O significado de um caminho de campo difere dependendo do contexto. Nos filtros, um caminho de campo refere-se ao valor de uma *única instância* de um campo no documento atual. Noutros contextos, como **$orderby**, **$select**, ou em [busca em campo na sintaxe lucene completa](query-lucene-syntax.md#bkmk_fields), um percurso de campo refere-se ao próprio campo. Esta diferença tem algumas consequências para a forma como utiliza os caminhos de campo nos filtros.

Considere o `Address/City`caminho de campo. Num filtro, isto refere-se a uma única cidade para o documento atual, como "São Francisco". Em contraste, `Rooms/Type` refere-se ao `Type` sub-campo para muitas salas (como "standard" para a primeira sala, "deluxe" para o segundo quarto, e assim por diante). Uma `Rooms/Type` vez que não se refere a `Type`uma única *instância* do sub-campo, não pode ser usado diretamente num filtro. Em vez disso, para filtrar no tipo de quarto, você usaria uma [expressão lambda](search-query-odata-collection-operators.md) com uma variável de gama, como esta:

    Rooms/any(room: room/Type eq 'deluxe')

Neste exemplo, a `room` variável de `room/Type` alcance aparece no caminho de campo. Desta forma, `room/Type` refere-se ao tipo de sala atual no documento atual. Esta é uma instância `Type` única do subcampo, por isso pode ser utilizado diretamente no filtro.

### <a name="using-field-paths"></a>Usando caminhos de campo

Os percursos de campo são utilizados em muitos parâmetros das APIs de [REPOUSO de Pesquisa Cognitiva Azure](https://docs.microsoft.com/rest/api/searchservice/). A tabela seguinte lista todos os locais onde podem ser utilizados, além de quaisquer restrições à sua utilização:

| API | Nome do parâmetro | Restrições |
| --- | --- | --- |
| [Criar](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Atualizar](https://docs.microsoft.com/rest/api/searchservice/update-index) Índice | `suggesters/sourceFields` | Nenhuma |
| [Criar](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Atualizar](https://docs.microsoft.com/rest/api/searchservice/update-index) Índice | `scoringProfiles/text/weights` | Só pode se referir a campos **pesquisáveis** |
| [Criar](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Atualizar](https://docs.microsoft.com/rest/api/searchservice/update-index) Índice | `scoringProfiles/functions/fieldName` | Só pode se referir a campos **filtrados** |
| [Procurar](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`quando `queryType` é`full` | Só pode se referir a campos **pesquisáveis** |
| [Procurar](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Só pode se referir a campos **de facetable** |
| [Procurar](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Só pode se referir a campos **pesquisáveis** |
| [Procurar](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Só pode se referir a campos **pesquisáveis** |
| [Sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions) e [Autocompletar](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Só pode se referir a campos que fazem parte de um [sugestor](index-add-suggesters.md) |
| [Pesquisa,](https://docs.microsoft.com/rest/api/searchservice/search-documents) [Sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions)e [Autocompletar](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Só pode se referir a campos **filtrados** |
| [Pesquisar](https://docs.microsoft.com/rest/api/searchservice/search-documents) e [Sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Só pode se referir a campos **classificados** |
| [Pesquisa,](https://docs.microsoft.com/rest/api/searchservice/search-documents) [Sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions)e [Procurar](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Só pode se referir a campos **recuperáveis** |

## <a name="constants"></a>Constantes

As constantes no OData são valores literais de um determinado modelo de Modelo de Dados de [Entidade](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM). Consulte os tipos de [dados suportados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para obter uma lista de tipos suportados na Pesquisa Cognitiva Azure. As constantes dos tipos de coleção não são suportadas.

A tabela que se segue apresenta exemplos de constantes para cada um dos tipos de dados suportados pela Pesquisa Cognitiva Azure:

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

As constantes de cordas no OData são delimitadas por citações individuais. Se precisar de construir uma consulta com uma constante de cordas que possa conter citações únicas, pode escapar às citações incorporadas duplicando-as.

Por exemplo, uma frase com um apóstrofe não formado como "O carro de `'Alice''s car'`Alice" seria representada no OData como a constante de cordas .

> [!IMPORTANT]
> Ao construir filtros programáticamente, é importante lembrar-se de escapar às constantes de cordas que vêm da entrada do utilizador. Isto é para mitigar a possibilidade de ataques de [injeção](https://wikipedia.org/wiki/SQL_injection), especialmente quando se utilizam filtros para implementar [o aparador](search-security-trimming-for-azure-search.md)de segurança .

### <a name="constants-syntax"></a>Sintaxe de constantes

O seguinte EBNF ([Formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática para a maioria das constantes mostradas na tabela acima. A gramática para tipos geo-espaciais pode ser encontrada em [funções geo-espaciais OData em Pesquisa Cognitiva Azure](search-query-odata-geo-spatial-functions.md).

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
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

## <a name="building-expressions-from-field-paths-and-constants"></a>Construindo expressões de caminhos de campo e constantes

Os caminhos de campo e as constantes são a parte mais básica de uma expressão OData, mas já são expressões completas. Na verdade, o parâmetro **$select** em Azure Cognitive Search não passa de uma lista separada de caminhos de campo separados, e **$orderby** não é muito mais complicado do que **$select**. Se por acaso tiver `Edm.Boolean` um campo de tipo no seu índice, pode até escrever um filtro que não passa de um caminho desse campo. As `true` constantes `false` e são filtros igualmente válidos.

No entanto, na maior parte do tempo vai precisar de expressões mais complexas que se referem a mais do que um campo e constante. Estas expressões são construídas de diferentes maneiras dependendo do parâmetro.

O Seguinte EBNF ( Forma Estendida de[Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática para os **parâmetros $filter**, **$orderby**e **$select.** Estes são construídos a partir de expressões mais simples que se referem a caminhos de campo e constantes:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Os parâmetros **$orderby** e **$select** são listas separadas pela vírposta de expressões mais simples. O parâmetro **$filter** é uma expressão booleana que é composta por subexpressões mais simples. Estas subexpressões são combinadas utilizando operadores lógicos [ `and` `or`como, `not`e ](search-query-odata-logical-operators.md)operadores de comparação, [ `eq`como, `lt` `gt`e por diante,](search-query-odata-comparison-operators.md)e operadores de recolha, tais como [ `any` e `all` ](search-query-odata-collection-operators.md).

Os parâmetros **$filter**, **$orderby**e **$select** são explorados mais detalhadamente nos seguintes artigos:

- [OData $filter sintaxe na Pesquisa Cognitiva de Azure](search-query-odata-filter.md)
- [OData $orderby sintaxe na Pesquisa Cognitiva azure](search-query-odata-orderby.md)
- [OData $select sintaxe na Pesquisa Cognitiva de Azure](search-query-odata-select.md)

## <a name="see-also"></a>Consulte também  

- [Navegação facetada na Pesquisa Cognitiva Azure](search-faceted-navigation.md)
- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Sintaxe de consulta Lucene](query-lucene-syntax.md)
- [Sintaxe de consulta simples em Pesquisa Cognitiva Azure](query-simple-syntax.md)
