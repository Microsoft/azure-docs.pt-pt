---
title: Referência de encomenda OData
titleSuffix: Azure Cognitive Search
description: Documentação de sintaxe e referência linguística para o uso de order-by em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113158"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData $orderby sintaxe na Pesquisa Cognitiva azure

 Pode utilizar o [parâmetro **oData $orderby** ](query-odata-filter-orderby-syntax.md) para aplicar uma ordem de classificação personalizada para resultados de pesquisa em Pesquisa Cognitiva Azure. Este artigo descreve detalhadamente a sintaxe de **$orderby.** Para obter informações mais gerais sobre como usar **$orderby** ao apresentar resultados de pesquisa, consulte Como trabalhar com os resultados da [pesquisa em Pesquisa Cognitiva Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxe

O parâmetro **$orderby** aceita uma lista separada de vírpostas até 32 **cláusulas de encomenda.** A sintaxe de uma cláusula de encomenda é descrita pela seguinte EBNF ([Formulário De backus-Naur estendido):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Cada cláusula tem critérios de classificação,`asc` opcionalmente seguidos por uma direção de classificação (ascendente ou `desc` descendente). Se não especificar uma direção, o padrão está a subir. Os critérios de classificação podem `sortable` ser o caminho [`geo.distance`](search-query-odata-geo-spatial-functions.md) de [`search.score`](search-query-odata-search-score-function.md) um campo ou uma chamada para as funções ou para as funções.

Se vários documentos tiverem os `search.score` mesmos critérios de tipo e a função não for utilizada (por exemplo, se classificar por um campo numérico `Rating` e três documentos tiverem uma classificação de 4), os laços serão quebrados pela pontuação do documento em ordem descendente. Quando as pontuações dos documentos são as mesmas (por exemplo, quando não há consulta de pesquisa de texto completo especificada no pedido), então a ordem relativa dos documentos vinculados é indeterminada.

Pode especificar critérios de vários tipos. A ordem de expressão determina a ordem de classificação final. Por exemplo, para classificar descendo pela pontuação, seguido `$orderby=search.score() desc,Rating desc`de Rating, a sintaxe seria .

A sintaxe para `geo.distance` **$orderby** é a mesma que é em **$filter**. Quando `geo.distance` se utiliza em **$orderby,** o campo a `Edm.GeographyPoint` que se `sortable`aplica deve ser de tipo e deve ser.

A sintaxe `search.score` para `search.score()` **$orderby** é. A `search.score` função não tem parâmetros.

## <a name="examples"></a>Exemplos

Ordenar hotéis ascendentes por taxa base:

    $orderby=BaseRate asc

Ordenar hotéis descendo por classificação, em seguida, ascendendo por taxa base (lembre-se que ascendente é o padrão):

    $orderby=Rating desc,BaseRate

Ordenar hotéis descendo por classificação, em seguida, ascendendo à distância das coordenadas dadas:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Ordenar hotéis em ordem descendente por search.score e rating, e em seguida, em ordem ascendente por distância das coordenadas dadas. Entre dois hotéis com pontuações de relevância idênticas e classificações, o mais próximo está listado primeiro:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passos seguintes  

- [Como trabalhar com os resultados da pesquisa em Pesquisa Cognitiva Azure](search-pagination-page-layout.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
