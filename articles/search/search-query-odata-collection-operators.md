---
title: Referência de operador de coleção OData
titleSuffix: Azure Cognitive Search
description: Ao criar expressões de filtro no Azure Pesquisa Cognitiva consultas, use os operadores "any" e "All" em expressões lambda quando o filtro estiver em um campo de coleção ou coleção complexa.
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113243"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Operadores de coleção OData no Azure Pesquisa Cognitiva-`any` e `all`

Ao escrever uma [expressão de filtro OData](query-odata-filter-orderby-syntax.md) para usar com o Azure pesquisa cognitiva, geralmente é útil filtrar nos campos de coleção. Você pode conseguir isso usando os operadores de `any` e `all`.

## <a name="syntax"></a>Sintaxe

O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de uma expressão OData que usa `any` ou `all`.

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
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Há três formas de expressão que filtram coleções.

- As duas primeiras iteram em um campo de coleção, aplicando um predicado fornecido na forma de uma expressão lambda para cada elemento da coleção.
  - Uma expressão usando `all` retorna `true` se o predicado for true para todos os elementos da coleção.
  - Uma expressão usando `any` retornará `true` se o predicado for true para pelo menos um elemento da coleção.
- A terceira forma de filtro de coleção usa `any` sem uma expressão lambda para testar se um campo de coleção está vazio. Se a coleção tiver qualquer elemento, ela retornará `true`. Se a coleção estiver vazia, ela retornará `false`.

Uma **expressão lambda** em um filtro de coleção é como o corpo de um loop em uma linguagem de programação. Ele define uma variável, chamada **variável de intervalo**, que contém o elemento atual da coleção durante a iteração. Ele também define outra expressão booliana que é o critério de filtro a ser aplicado à variável de intervalo para cada elemento da coleção.

## <a name="examples"></a>Exemplos

Corresponder documentos cujo campo de `tags` contém exatamente a cadeia de caracteres "WiFi":

    tags/any(t: t eq 'wifi')

Corresponder documentos em que cada elemento do campo de `ratings` cai entre 3 e 5, inclusive:

    ratings/all(r: r ge 3 and r le 5)

Corresponder documentos em que qualquer uma das coordenadas geográficas no campo `locations` está dentro do polígono fornecido:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Corresponder documentos em que o campo de `rooms` está vazio:

    not rooms/any()

Corresponder documentos em que para todas as salas, o campo de `rooms/amenities` contém "TV" e `rooms/baseRate` é menor que 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Limitações

Nem todos os recursos de expressões de filtro estão disponíveis no corpo de uma expressão lambda. As limitações são diferentes dependendo do tipo de dados do campo de coleção que você deseja filtrar. A tabela a seguir resume as limitações.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Para obter mais detalhes sobre essas limitações, bem como exemplos, consulte [solução de problemas de filtros de coleta no Azure pesquisa cognitiva](search-query-troubleshoot-collection-filters.md). Para obter informações mais detalhadas sobre por que essas limitações existem, consulte [noções básicas sobre filtros de coleta no Azure pesquisa cognitiva](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passos seguintes  

- [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;do Azure pesquisa cognitiva API REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
