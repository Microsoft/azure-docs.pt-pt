---
title: Referência do operador de coleção OData-Azure Search
description: Operadores de coleção OData, any e All e expressões lambda em Azure Search consultas.
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
ms.openlocfilehash: e057d0b57162d10aab13d8b1f77e0eaddca2ec2a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647641"
---
# <a name="odata-collection-operators-in-azure-search---any-and-all"></a>Operadores de coleção OData no Azure Search `any` -e`all`

Ao escrever uma [expressão de filtro OData](query-odata-filter-orderby-syntax.md) para usar com Azure Search, geralmente é útil filtrar nos campos de coleção. Você pode conseguir isso usando os `any` operadores `all` e.

## <a name="syntax"></a>Sintaxe

O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de uma expressão OData `any` que `all`usa ou.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md) para o EBNF completo.

Há três formas de expressão que filtram coleções.

- As duas primeiras iteram em um campo de coleção, aplicando um predicado fornecido na forma de uma expressão lambda para cada elemento da coleção.
  - Uma expressão que `all` usa `true` retorna se o predicado é verdadeiro para cada elemento da coleção.
  - Uma expressão que `any` usa `true` retorna se o predicado é true para pelo menos um elemento da coleção.
- A terceira forma de filtro de coleção `any` usa sem uma expressão lambda para testar se um campo de coleção está vazio. Se a coleção tiver qualquer elemento, ela retornará `true`. Se a coleção estiver vazia, ela retornará `false`.

Uma **expressão lambda** em um filtro de coleção é como o corpo de um loop em uma linguagem de programação. Ele define uma variável, chamada **variável de intervalo**, que contém o elemento atual da coleção durante a iteração. Ele também define outra expressão booliana que é o critério de filtro a ser aplicado à variável de intervalo para cada elemento da coleção.

## <a name="examples"></a>Exemplos

Corresponder documentos cujo `tags` campo contém exatamente a cadeia de caracteres "WiFi":

    tags/any(t: t eq 'wifi')

Corresponder documentos em que cada elemento do `ratings` campo cai entre 3 e 5, inclusive:

    ratings/all(r: r ge 3 and r le 5)

Corresponder documentos em que qualquer uma das coordenadas geográficas no `locations` campo esteja dentro do polígono determinado:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Corresponder documentos onde o `rooms` campo está vazio:

    not rooms/any()

Corresponder documentos em que para todas as salas `rooms/amenities` , o campo conterá " `rooms/baseRate` TV" e menor que 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Limitações

Nem todos os recursos de expressões de filtro estão disponíveis no corpo de uma expressão lambda. As limitações são diferentes dependendo do tipo de dados do campo de coleção que você deseja filtrar. A tabela a seguir resume as limitações.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Para obter mais detalhes sobre essas limitações, bem como exemplos, consulte [solução de problemas de filtros de coleta no Azure Search](search-query-troubleshoot-collection-filters.md). Para obter informações mais detalhadas sobre por que essas limitações existem, consulte [noções básicas sobre filtros de coleta no Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
