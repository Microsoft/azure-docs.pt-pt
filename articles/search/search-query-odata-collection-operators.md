---
title: Referência do operador de recolha OData
titleSuffix: Azure Cognitive Search
description: Ao criar expressões de filtro em consultas de Pesquisa Cognitiva Azure, utilize "todos" operadores em expressões lambda quando o filtro estiver num campo de recolha ou de recolha complexo.
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
ms.openlocfilehash: 4c1243d5d9122539466e94b6bbfdd5ced588e69a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88934910"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Operadores de recolha OData em Pesquisa Cognitiva Azure - `any` e `all`

Ao escrever uma [expressão de filtro OData](query-odata-filter-orderby-syntax.md) para usar com Azure Cognitive Search, é frequentemente útil filtrar em campos de recolha. Pode conseguir isso utilizando os `any` operadores e `all` operadores.

## <a name="syntax"></a>Syntax

O seguinte formulário EBNF[(Formulário Backus-Naur Alargado)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática de uma expressão OData que utiliza `any` ou `all` .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

Há três formas de expressão que filtram as coleções.

- Os dois primeiros iteram sobre um campo de recolha, aplicando um predicado dado sob a forma de expressão lambda a cada elemento da coleção.
  - Uma expressão que usa `all` devoluções `true` se o predicado for verdadeiro para cada elemento da coleção.
  - Uma expressão que usa `any` devoluções `true` se o predicado for verdadeiro para pelo menos um elemento da coleção.
- A terceira forma de filtro de recolha utiliza `any` sem uma expressão lambda para testar se um campo de recolha está vazio. Se a coleção tiver elementos, `true` retorna. Se a coleção estiver vazia, ela `false` regressa.

Uma **expressão de lambda** num filtro de coleção é como o corpo de um loop numa linguagem de programação. Define uma variável, chamada **variável de gama,** que detém o elemento atual da coleção durante a iteração. Também define outra expressão booleana que é o critério do filtro a aplicar à variável de gama para cada elemento da coleção.

## <a name="examples"></a>Exemplos

Documentos de correspondência cujo `tags` campo contém exatamente a cadeia "wifi":

```text
tags/any(t: t eq 'wifi')
```

Documentos de correspondência onde cada elemento do `ratings` campo cai entre 3 e 5, inclusive:

```text
ratings/all(r: r ge 3 and r le 5)
```

Combine documentos em que qualquer uma das coordenadas de geo no `locations` campo esteja dentro do polígono dado:

```text
locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))
```

Combine documentos onde o `rooms` campo está vazio:

```text
not rooms/any()
```

Documentos de correspondência onde para todos os quartos, o `rooms/amenities` campo contém "tv" e `rooms/baseRate` é inferior a 100:

```text
rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)
```

## <a name="limitations"></a>Limitações

Nem todas as características das expressões de filtro estão disponíveis dentro do corpo de uma expressão lambda. As limitações diferem consoante o tipo de dados do campo de recolha que pretende filtrar. A tabela seguinte resume as limitações.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Para obter mais detalhes sobre estas limitações, bem como exemplos, consulte [os filtros de recolha de resolução de problemas em Azure Cognitive Search](search-query-troubleshoot-collection-filters.md). Para obter informações mais aprofundadas sobre o porquê destas limitações existirem, consulte [filtros de recolha de compreensão na Pesquisa Cognitiva Azure.](search-query-understand-collection-filters.md)

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)