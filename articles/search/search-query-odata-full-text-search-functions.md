---
title: Referência da função de pesquisa de texto completo OData
titleSuffix: Azure Cognitive Search
description: OData funções de pesquisa de texto completo, search.ismatch e search.ismatchscoring, in Azure Cognitive Search consultas.
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
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793356"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>OData funções de pesquisa de texto `search.ismatch` completo em Pesquisa Cognitiva Azure - e`search.ismatchscoring`

A Pesquisa Cognitiva Azure suporta a pesquisa de texto completo `search.ismatch` no `search.ismatchscoring` contexto das expressões de [filtro OData](query-odata-filter-orderby-syntax.md) através das e funções. Estas funções permitem combinar a pesquisa de texto completo com a filtragem booleanrigorosa `search` rigorosa de formas que não são possíveis apenas utilizando o parâmetro de nível superior da [API](https://docs.microsoft.com/rest/api/searchservice/search-documents)de pesquisa .

> [!NOTE]
> As `search.ismatch` `search.ismatchscoring` funções e funções são suportadas apenas em filtros na [API](https://docs.microsoft.com/rest/api/searchservice/search-documents)de pesquisa . Não são suportados nas APIs [Sugestivas](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [Autocompletas.](https://docs.microsoft.com/rest/api/searchservice/autocomplete)

## <a name="syntax"></a>Sintaxe

O seguinte EBNF ( Forma Estendida de[Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática das `search.ismatch` funções: `search.ismatchscoring`

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

### <a name="searchismatch"></a>search.ismatch

A `search.ismatch` função avalia uma consulta de pesquisa de texto completo como parte de uma expressão de filtro. Os documentos que correspondam à consulta de pesquisa serão devolvidos no conjunto de resultados. Estão disponíveis as seguintes sobrecargas desta função:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Os parâmetros são definidos na tabela seguinte:

| Nome do parâmetro | Tipo | Descrição |
| --- | --- | --- |
| `search` | `Edm.String` | A consulta de pesquisa (em sintaxe de consulta [lucene simples](query-simple-syntax.md) ou [completa).](query-lucene-syntax.md) |
| `searchFields` | `Edm.String` | Lista separada da vírposta de campos pesquisáveis para pesquisar; incumprimentos em todos os campos pesquisáveis no índice. Ao utilizar a pesquisa `search` no [parâmetro,](query-lucene-syntax.md#bkmk_fields) os especificadores de campo na consulta Lucene sobrepõem-se a quaisquer campos especificados neste parâmetro. |
| `queryType` | `Edm.String` | `'simple'`ou; `'full'` incumprimentos `'simple'`para . Especifica que linguagem de consulta `search` foi usada no parâmetro. |
| `searchMode` | `Edm.String` | `'any'`ou, `'all'`predefinições para `'any'`. Indica se qualquer ou todos os `search` termos de pesquisa no parâmetro devem ser combinados para contar o documento como um fósforo. Ao utilizar os [operadores Lucene Boolean](query-lucene-syntax.md#bkmk_boolean) no `search` parâmetro, terão precedência sobre este parâmetro. |

Todos os parâmetros acima referidos são equivalentes aos parâmetros de pedido de [procura correspondentes na API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

A `search.ismatch` função devolve `Edm.Boolean`um valor de tipo, que lhe permite compor com outras subexpressões de filtro utilizando os [operadores lógicos](search-query-odata-logical-operators.md)booleanos .

> [!NOTE]
> A Pesquisa Cognitiva Azure `search.ismatch` `search.ismatchscoring` não suporta o uso ou dentro das expressões lambda. Isto significa que não é possível escrever filtros sobre coleções de objetos que possam correlacionar jogos de pesquisa de texto completo com correspondências rígidas de filtro no mesmo objeto. Para mais detalhes sobre esta limitação, bem como exemplos, consulte filtros de recolha de [resolução de problemas em Pesquisa Cognitiva Azure](search-query-troubleshoot-collection-filters.md). Para obter informações mais aprofundadas sobre o porquê desta limitação existir, consulte filtros de [recolha De compreensão em Pesquisa Cognitiva Azure](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>search.ismatchscoring

A `search.ismatchscoring` função, `search.ismatch` tal `true` como a função, retorna para documentos que correspondam à consulta de pesquisa de texto completo passada como parâmetro. A diferença entre eles é que a `search.ismatchscoring` pontuação de relevância dos documentos correspondentes `search.ismatch`à consulta contribuirá para a pontuação total do documento, enquanto no caso de , a pontuação do documento não será alterada. As seguintes sobrecargas desta função estão `search.ismatch`disponíveis com parâmetros idênticos aos de:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Tanto `search.ismatch` as `search.ismatchscoring` funções como as funções podem ser utilizadas na mesma expressão de filtro.

## <a name="examples"></a>Exemplos

Encontre documentos com a palavra "cais". Esta consulta de filtro é idêntica `search=waterfront`a um pedido de [pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) com .

    search.ismatchscoring('waterfront')

Encontre documentos com a palavra "hostel" e classificação maior ou igual a 4, ou documentos com a palavra "motel" e classificação igual a 5. Nota: este pedido não poderia `search.ismatchscoring` ser expresso sem a função.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Encontre documentos sem a palavra "luxo".

    not search.ismatch('luxury')

Encontre documentos com a frase "vista para o mar" ou classificação igual a 5. A `search.ismatchscoring` consulta será executada apenas `HotelName` `Rooms/Description`contra campos e .

Documentos que correspondam apenas à segunda cláusula da disjunção `Rating` também serão devolvidos. Para deixar claro que esses documentos não correspondem a nenhuma das partes pontuadas da expressão, serão devolvidos com pontuação igual a zero.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Encontre documentos onde os termos "hotel" e "aeroporto" estejam a 5 palavras um do outro na descrição do hotel, e onde não é permitido fumar em pelo menos alguns dos quartos. Esta consulta utiliza toda a [linguagem lucene.](query-lucene-syntax.md)

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
