---
title: Referência da função de pesquisa de texto completo OData
titleSuffix: Azure Cognitive Search
description: OData funções de pesquisa de texto completo, search.ismatch e search.ismatchscoring, em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: 78f9e4d8fa80fdf74bdb5cd79f4489d12696fcc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935794"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>OData funções de pesquisa de texto completo em Azure Cognitive Search - `search.ismatch` e `search.ismatchscoring`

A Azure Cognitive Search suporta a pesquisa de texto completo no contexto das expressões de [filtro OData](query-odata-filter-orderby-syntax.md) através das `search.ismatch` funções e `search.ismatchscoring` funções. Estas funções permitem combinar a pesquisa de texto completo com uma filtragem rigorosa do Boolean de formas que não são possíveis apenas utilizando o parâmetro de nível superior `search` da [API de pesquisa.](/rest/api/searchservice/search-documents)

> [!NOTE]
> As `search.ismatch` `search.ismatchscoring` funções e as funções só são suportadas em filtros na [API de Pesquisa.](/rest/api/searchservice/search-documents) Não são suportados nas APIs [sugerem](/rest/api/searchservice/suggestions) ou [autocompletas.](/rest/api/searchservice/autocomplete)

## <a name="syntax"></a>Syntax

O seguinte formulário EBNF[(Formulário Backus-Naur Alargado)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática das `search.ismatch` e `search.ismatchscoring` funções:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

### <a name="searchismatch"></a>pesquisa.ismatch

A `search.ismatch` função avalia uma consulta de pesquisa de texto completo como parte de uma expressão de filtro. Os documentos que correspondam à consulta de pesquisa serão devolvidos no conjunto de resultados. Estão disponíveis as seguintes sobrecargas desta função:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Os parâmetros são definidos na tabela seguinte:

| Nome do parâmetro | Tipo | Description |
| --- | --- | --- |
| `search` | `Edm.String` | A consulta de pesquisa (na sintaxe de consulta de Lucene [simples](query-simple-syntax.md) ou [completa).](query-lucene-syntax.md) |
| `searchFields` | `Edm.String` | Lista separada por vírgula de campos pes pesjáveis para pesquisar; incumprimentos para todos os campos pes pesjáveis no índice. Ao utilizar a pesquisa de [campo](query-lucene-syntax.md#bkmk_fields) no `search` parâmetro, os especificadores de campo na consulta Lucene sobrepõem-se a quaisquer campos especificados neste parâmetro. |
| `queryType` | `Edm.String` | `'simple'` ou; `'full'` predefinições a `'simple'` . Especifica qual a linguagem de consulta utilizada no `search` parâmetro. |
| `searchMode` | `Edm.String` | `'any'` ou `'all'` , predefinições a `'any'` . Indica se algum ou todos os termos de pesquisa no `search` parâmetro devem ser combinados para contar o documento como uma correspondência. Ao utilizar os [operadores Lucene Boolean](query-lucene-syntax.md#bkmk_boolean) no `search` parâmetro, terão precedência sobre este parâmetro. |

Todos os parâmetros acima são equivalentes aos [parâmetros correspondentes de pedido de pesquisa na API de pesquisa.](/rest/api/searchservice/search-documents)

A `search.ismatch` função devolve um valor do `Edm.Boolean` tipo, que lhe permite compor com outras subex expressões de filtro utilizando os [operadores lógicos](search-query-odata-logical-operators.md)Boolean.

> [!NOTE]
> A Azure Cognitive Search não suporta o uso `search.ismatch` ou o interior de `search.ismatchscoring` expressões lambda. Isto significa que não é possível escrever filtros sobre coleções de objetos que possam correlacionar partidas de pesquisa de texto completo com correspondências rígidas de filtro no mesmo objeto. Para obter mais detalhes sobre esta limitação, bem como exemplos, consulte [os filtros de recolha de resolução de problemas em Azure Cognitive Search](search-query-troubleshoot-collection-filters.md). Para obter informações mais aprofundadas sobre o porquê desta limitação existir, consulte [filtros de recolha de compreensão na Pesquisa Cognitiva Azure.](search-query-understand-collection-filters.md)


### <a name="searchismatchscoring"></a>search.ismatchscoring

A `search.ismatchscoring` função, tal como a `search.ismatch` função, retorna `true` para documentos que correspondam à consulta de pesquisa de texto completo passada como parâmetro. A diferença entre eles é que a pontuação de relevância dos documentos correspondentes `search.ismatchscoring` à consulta contribuirá para a pontuação geral do documento, enquanto no caso `search.ismatch` de, a pontuação do documento não será alterada. Estão disponíveis as seguintes sobrecargas desta função com parâmetros idênticos aos `search.ismatch` de:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Tanto as funções como as `search.ismatch` `search.ismatchscoring` funções podem ser utilizadas na mesma expressão do filtro.

## <a name="examples"></a>Exemplos

Encontre documentos com a palavra "cais". Esta consulta de filtro é idêntica a um [pedido de pesquisa](/rest/api/searchservice/search-documents) com `search=waterfront` .

```odata-filter-expr
    search.ismatchscoring('waterfront')
```

Encontre documentos com a palavra "hostel" e classificação superior ou igual a 4, ou documentos com a palavra "motel" e classificação igual a 5. Nota: este pedido não poderia ser expresso sem a `search.ismatchscoring` função.

```odata-filter-expr
    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5
```

Encontre documentos sem a palavra "luxo".

```odata-filter-expr
    not search.ismatch('luxury')
```

Encontre documentos com a frase "vista para o mar" ou classificação igual a 5. A `search.ismatchscoring` consulta será executada apenas contra campos e `HotelName` `Rooms/Description` .

Documentos que correspondam apenas à segunda cláusula da disjunção também serão devolvidos- hotéis com `Rating` igual a 5. Para deixar claro que esses documentos não correspondem a nenhuma das partes pontuadas da expressão, serão devolvidos com pontuação igual a zero.

```odata-filter-expr
    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5
```

Encontre documentos onde os termos "hotel" e "aeroporto" estão a menos de 5 palavras umas das outras na descrição do hotel, e onde fumar não é permitido em pelo menos alguns dos quartos. Esta consulta utiliza a [linguagem de consulta lucene completa.](query-lucene-syntax.md)

```odata-filter-expr
    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)
```

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)