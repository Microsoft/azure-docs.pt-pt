---
title: Referência de função de pesquisa em texto completo do OData - Azure Search
description: Funções de pesquisa em texto completo de OData, search.ismatch e search.ismatchscoring, nas consultas de pesquisa do Azure.
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
ms.openlocfilehash: 158312a7afe88e7b9885376c5d28b01958acbbfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079811"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>As funções de pesquisa em texto completo de OData no Azure Search - `search.ismatch` e `search.ismatchscoring`

O Azure Search oferece suporte a pesquisa em texto completo no contexto do [expressões de filtro de OData](query-odata-filter-orderby-syntax.md) através do `search.ismatch` e `search.ismatchscoring` funções. Estas funções permitem-lhe combinar a pesquisa em texto completo com a filtragem booleano estrita de forma a que não é possível apenas com o uso de nível superior `search` parâmetro do [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> O `search.ismatch` e `search.ismatchscoring` funções só são suportadas em filtros na [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents). Não são suportados no [sugere](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) APIs.

## <a name="syntax"></a>Sintaxe

A seguinte EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática da `search.ismatch` e `search.ismatchscoring` funções:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

### <a name="searchismatch"></a>search.ismatch

O `search.ismatch` função avalia uma consulta de pesquisa em texto completo, como parte de uma expressão de filtro. Os documentos que correspondam à consulta de pesquisa serão retornados no conjunto de resultados. As seguintes sobrecargas dessa função estão disponíveis:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Os parâmetros são definidos na tabela a seguir:

| Nome do parâmetro | Tipo | Descrição |
| --- | --- | --- |
| `search` | `Edm.String` | A consulta de pesquisa (em qualquer um [simples](query-simple-syntax.md) ou [completo](query-lucene-syntax.md) sintaxe de consulta Lucene). |
| `searchFields` | `Edm.String` | Lista separada por vírgulas de campos pesquisáveis para procurar no; assume a predefinição de todos os campos pesquisáveis no índice. Ao usar [respondidas pesquisa](query-lucene-syntax.md#bkmk_fields) no `search` parâmetro, os especificadores de campo na consulta Lucene substituem quaisquer campos especificados neste parâmetro. |
| `queryType` | `Edm.String` | `'simple'` ou `'full'`; o padrão é `'simple'`. Especifica qual linguagem de consulta foi utilizada no `search` parâmetro. |
| `searchMode` | `Edm.String` | `'any'` ou `'all'`, o padrão é `'any'`. Indica se uma ou todas a pesquisa de termos no `search` parâmetro deve corresponder para a contagem do documento como uma correspondência. Ao utilizar o [operadores booleanos de Lucene](query-lucene-syntax.md#bkmk_boolean) no `search` parâmetro, irão ter precedência sobre este parâmetro. |

Todos os parâmetros acima são equivalentes a correspondente [parâmetros de solicitação de pesquisa na API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

O `search.ismatch` função devolve um valor do tipo `Edm.Boolean`, que permite que componha-lo com outros subexpressões de filtro com o valor de booleano [operadores lógicos](search-query-odata-logical-operators.md).

> [!NOTE]
> O Azure Search não suporta a utilização `search.ismatch` ou `search.ismatchscoring` no interior de expressões lambda. Isso significa que não é possível para filtros de escrita em coleções de objetos que podem correlacionar correspondências de pesquisa em texto completo com correspondências de filtro strict no mesmo objeto. Para obter mais detalhes sobre esta limitação, bem como exemplos, consulte [resolução de problemas de filtros de recolha no Azure Search](search-query-troubleshoot-collection-filters.md). Para obter mais informações aprofundadas sobre por que esta limitação existe, consulte [Noções básicas sobre filtros de recolha no Azure Search](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>search.ismatchscoring

O `search.ismatchscoring` funcione, como o `search.ismatch` funcione, devolve `true` para documentos que correspondam à consulta de pesquisa em texto completo transmitida como um parâmetro. A diferença entre eles é que a relevância pontuação de documentos que correspondam a `search.ismatchscoring` consulta irá contribuir para a classificação de documento geral, embora, no caso de `search.ismatch`, a pontuação de documento não ser alterada. As seguintes sobrecargas dessa função estão disponíveis com parâmetros idênticos do `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Tanto o `search.ismatch` e `search.ismatchscoring` funções podem ser utilizadas na mesma expressão de filtro.

## <a name="examples"></a>Exemplos

Encontre os documentos com a palavra "frente marítima". Esta consulta de filtro é idêntica para um [solicitação de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) com `search=waterfront`.

    search.ismatchscoring('waterfront')

Encontre os documentos com hostel"palavra" e classificação superior ou igual a 4 ou documentos com a palavra "motel" e classificação igual a 5. Tenha em atenção de que este pedido não foi possível ser expressado sem o `search.ismatchscoring` função.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Encontre documentos sem a palavra "luxo".

    not search.ismatch('luxury')

Encontre os documentos com a frase "Oceano view" ou uma classificação igual a 5. O `search.ismatchscoring` consulta será executada apenas em relação a campos `HotelName` e `Rooms/Description`.

Apenas a segunda cláusula da disjunção de correspondentes serão devolvidos documentos demasiado – hotéis com `Rating` igual a 5. Para torná-lo claro que esses documentos não correspondam a qualquer uma das partes com a pontuação da expressão, vai ser devolvidos com score igual a zero.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Encontrar os documentos onde o hotel"termos" e "aeroporto" estão dentro de 5 palavras uns dos outros na descrição do hotel e onde fumar não é permitido em, pelo menos, algumas dos ambientes. Esta consulta utiliza a [completa de linguagem de consulta Lucene](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
