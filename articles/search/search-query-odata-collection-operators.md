---
title: Referência de operador de coleção OData - Azure Search
description: Os operadores de coleção do OData, e qualquer e as expressões lambda em consultas de pesquisa do Azure.
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
ms.openlocfilehash: 7afafe158732b14ebe314eeee5d015acddc55b72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079941"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Operadores de coleção OData na Azure Search - `any` e `all`

Ao escrever um [expressão de filtro de OData](query-odata-filter-orderby-syntax.md) para utilizar com o Azure Search, muitas vezes é útil para filtrar em campos de coleção. Pode conseguir isto com o `any` e `all` operadores.

## <a name="syntax"></a>Sintaxe

A seguinte EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de uma expressão de OData que utiliza `any` ou `all`.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

Existem três formas de expressão filtrar coleções.

- Os dois primeiros iterar sobre um campo de coleção, aplicar um predicado tendo em conta sob a forma de uma expressão lambda para cada elemento da coleção.
  - Uma expressão a utilizar `all` retorna `true` se o predicado é verdadeiro para cada elemento da coleção.
  - Uma expressão a utilizar `any` retorna `true` se o predicado é verdadeiro para pelo menos um elemento da coleção.
- A terceira forma de coleção filtrar utiliza `any` sem uma expressão lambda para testar se um campo de coleção está vazio. Se a coleção tem todos os elementos, ela retorna `true`. Se a coleção estiver vazia, ele retorna `false`.

R **expressão lambda** numa coleção de filtro é como o corpo de um loop numa linguagem de programação. Ele define uma variável, chamado de **variável de alcance**, que contém o elemento atual da coleção durante a iteração. Também define outra expressão booleana que é o critério de filtro a aplicar à variável de alcance para cada elemento da coleção.

## <a name="examples"></a>Exemplos

Correspondência documenta cujos `tags` campo contém exatamente a cadeia de caracteres "Wi-Fi":

    tags/any(t: t eq 'wifi')

Correspondência de documentos onde todos os elementos do `ratings` campo se encontra entre 3 e 5, inclusive:

    ratings/all(r: r ge 3 and r le 5)

Documentos de correspondência em que qualquer um da geo a coordena a `locations` campo está dentro do polígono especificado:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Correspondência de documentos onde o `rooms` campo estiver vazio:

    not rooms/any()

Corresponda nos documentos onde para todos os ambientes, o `rooms/amenities` campo contém "de tv" e `rooms/baseRate` é menor do que 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Limitações

Não todas as funcionalidades de expressões de filtro estão disponível dentro do corpo de uma expressão lambda. As limitações são diferentes consoante o tipo de dados do campo coleção que pretende filtrar. A tabela seguinte resume as limitações.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Para obter mais detalhes sobre essas limitações, bem como exemplos, consulte [resolução de problemas de filtros de recolha no Azure Search](search-query-troubleshoot-collection-filters.md). Para obter informações mais aprofundadas sobre por que essas limitações existe, consulte [Noções básicas sobre filtros de recolha no Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
