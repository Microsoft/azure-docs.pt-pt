---
title: Referência do operador de recolha oData
titleSuffix: Azure Cognitive Search
description: Ao criar expressões de filtro em consultas de Pesquisa Cognitiva Azure, utilize "quaisquer" e "todos" operadores em expressões lambda quando o filtro estiver num campo de recolha ou de recolha complexo.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113243"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Operadores de recolha oData `any` em Pesquisa Cognitiva Azure - e`all`

Ao escrever uma expressão de [filtro OData](query-odata-filter-orderby-syntax.md) para usar com a Pesquisa Cognitiva Azure, é muitas vezes útil filtrar nos campos de recolha. Pode conseguir isso `any` usando `all` os e operadores.

## <a name="syntax"></a>Sintaxe

O seguinte EBNF ([Formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a `any` `all`gramática de uma expressão OData que usa ou .

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
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Há três formas de expressão que filtram as coleções.

- Os dois primeiros itérão sobre um campo de recolha, aplicando um predicado dado sob a forma de expressão lambda a cada elemento da coleção.
  - Uma expressão `all` `true` usando devoluções se o predicado for verdadeiro para cada elemento da coleção.
  - Uma expressão `any` `true` usando devoluções se o predicado for verdadeiro para pelo menos um elemento da coleção.
- A terceira forma de `any` filtro de recolha utiliza sem expressão lambda para testar se um campo de recolha está vazio. Se a coleção tiver elementos, retorna. `true` Se a coleção estiver `false`vazia, ela regressa.

Uma **expressão de lambda** em um filtro de coleção é como o corpo de um loop em uma linguagem de programação. Define uma variável, chamada variável de **gama,** que detém o elemento atual da coleção durante a iteração. Também define outra expressão booleana que é o critério do filtro para aplicar à variável de gama para cada elemento da coleção.

## <a name="examples"></a>Exemplos

Documentos `tags` de correspondência cujo campo contém exatamente a corda "wifi":

    tags/any(t: t eq 'wifi')

Combine documentos em `ratings` que cada elemento do campo cai entre 3 e 5, inclusive:

    ratings/all(r: r ge 3 and r le 5)

Combine documentos em que qualquer `locations` uma das coordenadas geo-coordenadas no campo esteja dentro do polígono dado:

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

Documentos de `rooms` correspondência onde o campo está vazio:

    not rooms/any()

Combine documentos onde para `rooms/amenities` todos os quartos, o campo contém "tv" e `rooms/baseRate` é inferior a 100:

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>Limitações

Nem todas as características das expressões de filtro estão disponíveis dentro do corpo de uma expressão lambda. As limitações diferem consoante o tipo de dados do campo de recolha que pretende filtrar. A tabela que se segue resume as limitações.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Para mais detalhes sobre estas limitações, bem como exemplos, consulte filtros de recolha de [resolução de problemas em Pesquisa Cognitiva Azure](search-query-troubleshoot-collection-filters.md). Para obter informações mais aprofundadas sobre o porquê destas limitações existirem, consulte filtros de [recolha De compreensão em Pesquisa Cognitiva Azure](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
