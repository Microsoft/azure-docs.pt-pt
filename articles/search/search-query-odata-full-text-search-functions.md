---
title: Referência da função de pesquisa de texto completo OData-Azure Search
description: Funções de pesquisa de texto completo OData, Search. IsMatch e Search. ismatchscoring, em Azure Search consultas.
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
ms.openlocfilehash: c3b28c8799b09ddfe008df8539709c5a704ac6b4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648020"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>Funções de pesquisa de texto completo OData no Azure Search `search.ismatch` -e`search.ismatchscoring`

O Azure Search dá suporte à pesquisa de texto completo no contexto de [expressões de filtro OData](query-odata-filter-orderby-syntax.md) por meio das `search.ismatch` funções e. `search.ismatchscoring` Essas funções permitem combinar a pesquisa de texto completo com filtragem booliana estrita de maneiras que não são possíveis apenas usando o parâmetro de nível `search` superior da API de [pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> As `search.ismatch` funções `search.ismatchscoring` e têm suporte apenas em filtros na [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents). Eles não têm suporte nas APIs [sugerir](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Sintaxe

O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática `search.ismatch` das `search.ismatchscoring` funções e:

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
> [Diagrama de sintaxe do OData para Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md) para o EBNF completo.

### <a name="searchismatch"></a>Pesquisar. IsMatch

A `search.ismatch` função avalia uma consulta de pesquisa de texto completo como parte de uma expressão de filtro. Os documentos que correspondem à consulta de pesquisa serão retornados no conjunto de resultados. As seguintes sobrecargas dessa função estão disponíveis:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Os parâmetros são definidos na tabela a seguir:

| Nome do parâmetro | Type | Descrição |
| --- | --- | --- |
| `search` | `Edm.String` | A consulta de pesquisa (em sintaxe de consulta de Lucene [simples](query-simple-syntax.md) ou [completa](query-lucene-syntax.md) ). |
| `searchFields` | `Edm.String` | Lista separada por vírgulas de campos pesquisáveis para pesquisa; o padrão é todos os campos pesquisáveis no índice. Ao usar [](query-lucene-syntax.md#bkmk_fields) a `search` pesquisa de campo no parâmetro, os especificadores de campo na consulta Lucene substituem todos os campos especificados nesse parâmetro. |
| `queryType` | `Edm.String` | `'simple'`ou `'full'`; o padrão é `'simple'`. Especifica qual linguagem de consulta foi usada no `search` parâmetro. |
| `searchMode` | `Edm.String` | `'any'`ou `'all'`, usa como `'any'`padrão. Indica se um ou todos os termos de pesquisa no `search` parâmetro devem ser correspondidos a fim de contar o documento como uma correspondência. Ao usar os [operadores boolianos](query-lucene-syntax.md#bkmk_boolean) do Lucene `search` no parâmetro, eles terão precedência sobre esse parâmetro. |

Todos os parâmetros acima são equivalentes aos [parâmetros de solicitação de pesquisa correspondentes na API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

A `search.ismatch` função retorna um valor do tipo `Edm.Boolean`, que permite que você o redija com outras subexpressãos de filtro usando os [operadores lógicos](search-query-odata-logical-operators.md)boolianos.

> [!NOTE]
> Azure Search não dá suporte ao `search.ismatch` uso `search.ismatchscoring` de expressões lambda ou internas. Isso significa que não é possível gravar filtros em coleções de objetos que podem correlacionar correspondências de pesquisa de texto completo com correspondências de filtro estritas no mesmo objeto. Para obter mais detalhes sobre essa limitação, bem como exemplos, consulte [solução de problemas de filtros de coleção no Azure Search](search-query-troubleshoot-collection-filters.md). Para obter informações mais detalhadas sobre o motivo da existência dessa limitação, consulte [noções básicas sobre filtros de coleta no Azure Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Pesquisar. ismatchscoring

A `search.ismatchscoring` função, como a `search.ismatch` função, retorna `true` para documentos que correspondem à consulta de pesquisa de texto completo passada como um parâmetro. A diferença entre eles é que a pontuação de relevância dos documentos que `search.ismatchscoring` correspondem à consulta contribuirá para a pontuação geral do documento, enquanto, `search.ismatch`no caso do, a Pontuação do documento não será alterada. As seguintes sobrecargas dessa função estão disponíveis com parâmetros idênticos aos de `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

As `search.ismatch` funções e `search.ismatchscoring` podem ser usadas na mesma expressão de filtro.

## <a name="examples"></a>Exemplos

Localizar documentos com a palavra "orla marítima". Essa consulta de filtro é idêntica a uma solicitação de `search=waterfront` [pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) com.

    search.ismatchscoring('waterfront')

Localize documentos com a palavra "Hostel" e classificação maior ou igual a 4 ou documentos com a palavra "Motel" e classificação igual a 5. Observe que essa solicitação não pôde ser expressa sem a `search.ismatchscoring` função.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Localizar documentos sem a palavra "luxo".

    not search.ismatch('luxury')

Localize documentos com a frase "exibição do oceano" ou classificação igual a 5. A `search.ismatchscoring` consulta será executada somente em campos `HotelName` e `Rooms/Description`.

Os documentos que corresponderem somente à segunda cláusula da disjunção serão retornados também para hotéis com `Rating` igual a 5. Para tornar claro que esses documentos não corresponderam a nenhuma das partes pontuadas da expressão, eles serão retornados com pontuação igual a zero.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Localize documentos em que os termos "Hotel" e "aeroporto" estejam dentro de 5 palavras entre si na descrição do Hotel, e onde o fumante não é permitido em pelo menos algumas das salas. Essa consulta usa a [linguagem de consulta Lucene completa](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
