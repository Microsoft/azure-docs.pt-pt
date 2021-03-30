---
title: Referência de encomenda OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência linguística para utilização de consultas de pesquisa cognitiva em Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/05/2020
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
ms.openlocfilehash: 83ab2c6b97435ace0d2bc508cbf522600391b60b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88926835"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData $orderby sintaxe na pesquisa cognitiva de Azure

 Pode utilizar o [parâmetro OData **$orderby**](query-odata-filter-orderby-syntax.md) para aplicar uma ordem de classificação personalizada para resultados de pesquisa em Azure Cognitive Search. Este artigo descreve a sintaxe de **$orderby** em detalhe. Para obter informações mais gerais sobre como utilizar **$orderby** ao apresentar resultados de pesquisa, consulte [Como trabalhar com os resultados da pesquisa na Azure Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

O parâmetro **$orderby** aceita uma lista separada por vírgula de até 32 **cláusulas de encomenda**. A sintaxe de uma cláusula de encomenda é descrita pelo seguinte formulário EBNF[(Formulário Backus-Naur Alargado):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

Cada cláusula tem critérios de classificação, opcionalmente seguidos por uma direção de tipo `asc` (para subir ou `desc` descer). Se não especificar uma direção, o padrão está a subir. Se houver valores nulos no campo, os valores nulos aparecem primeiro se o tipo for `asc` e durar se o tipo for `desc` .

Os critérios de tipo podem ser o caminho de um `sortable` campo ou uma chamada para as [`geo.distance`](search-query-odata-geo-spatial-functions.md) funções ou para as [`search.score`](search-query-odata-search-score-function.md) funções.

Se vários documentos tiverem os mesmos critérios de tipo e a `search.score` função não for utilizada (por exemplo, se ordenar por um campo numérico `Rating` e três documentos todos tiverem uma classificação de 4), os laços serão quebrados pela pontuação do documento por ordem descendente. Quando as pontuações dos documentos são as mesmas (por exemplo, quando não há consulta de pesquisa de texto completo especificada no pedido), então a encomenda relativa dos documentos vinculados é indeterminada.

Pode especificar vários critérios de classificação. A ordem das expressões determina a ordem final. Por exemplo, para classificar descendo por pontuação, seguido de Rating, a sintaxe seria `$orderby=search.score() desc,Rating desc` .

A sintaxe para `geo.distance` **em $orderby** é a mesma que é em **$filter**. Quando utilizar `geo.distance` em **$orderby,** o campo a que se aplica deve ser do tipo `Edm.GeographyPoint` e também deve ser `sortable` .

A sintaxe para `search.score` **em $orderby** é `search.score()` . A função `search.score` não tem parâmetros.

## <a name="examples"></a>Exemplos

Classificar hotéis ascendentes por taxa base:

```odata-filter-expr
    $orderby=BaseRate asc
```

Classificar hotéis descendo por classificação, em seguida, subindo por taxa base (lembre-se que subir é o padrão):

```odata-filter-expr
    $orderby=Rating desc,BaseRate
```

Classificar hotéis descendo por classificação, em seguida, subindo por distância das coordenadas dadas:

```odata-filter-expr
    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

Ordenar hotéis em ordem descendente por pesquisa.pontuação e classificação, e em seguida, em ordem ascendente por distância das coordenadas dadas. Entre dois hotéis com pontuações e classificações de relevância idênticas, o mais próximo é listado primeiro:

```odata-filter-expr
    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Passos seguintes  

- [Como trabalhar com resultados de pesquisa na Azure Cognitive Search](search-pagination-page-layout.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)