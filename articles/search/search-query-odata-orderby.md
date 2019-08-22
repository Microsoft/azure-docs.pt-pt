---
title: Referência de order-by OData-Azure Search
description: Referência de linguagem OData para sintaxe de order-by em consultas Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 8ee44549931100a1affa5e2bb9e5cda904c05ed1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647544"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Sintaxe de $orderby OData no Azure Search

 Você pode usar o [parâmetro **$OrderBy** OData](query-odata-filter-orderby-syntax.md) para aplicar uma ordem de classificação personalizada para os resultados da pesquisa em Azure Search. Este artigo descreve a sintaxe de **$OrderBy** em detalhes. Para obter mais informações gerais sobre como usar **$OrderBy** ao apresentar resultados da pesquisa, consulte [como trabalhar com resultados da pesquisa em Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxe

O parâmetro **$OrderBy** aceita uma lista separada por vírgulas de até 32 **cláusulas**de ordenação. A sintaxe de uma cláusula order by é descrita pelo seguinte EBNF ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md) para o EBNF completo.

Cada cláusula tem critérios de classificação, opcionalmente seguido por uma direção de`asc` classificação (para crescente `desc` ou para decrescente). Se você não especificar uma direção, o padrão será ascendente. Os critérios de classificação podem ser o caminho de um `sortable` campo ou uma chamada para as [`geo.distance`](search-query-odata-geo-spatial-functions.md) [`search.score`](search-query-odata-search-score-function.md) funções ou.

Se vários documentos tiverem os mesmos critérios de classificação e `search.score` a função não for usada (por exemplo, se você classificar por `Rating` um campo numérico e três documentos todos tiverem uma classificação de 4), as ligações serão quebradas pela Pontuação do documento em ordem decrescente. Quando as pontuações de documento são as mesmas (por exemplo, quando não há nenhuma consulta de pesquisa de texto completo especificada na solicitação), a ordem relativa dos documentos vinculados é indeterminada.

Você pode especificar vários critérios de classificação. A ordem das expressões determina a ordem de classificação final. Por exemplo, para classificar em ordem decrescente por Pontuação, seguido por classificação, a `$orderby=search.score() desc,Rating desc`sintaxe seria.

A sintaxe de `geo.distance` em **$OrderBy** é a mesma que está em **$Filter**. Ao usar `geo.distance` o no **$OrderBy**, o campo ao qual ele se aplica deve ser `Edm.GeographyPoint` do tipo e também deve `sortable`ser.

A sintaxe para `search.score` no **$OrderBy** é `search.score()`. A função `search.score` não assume nenhum parâmetro.

## <a name="examples"></a>Exemplos

Classificar hotéis em ordem crescente por taxa base:

    $orderby=BaseRate asc

Classifique os hotéis decrescentes por classificação e, em seguida, em ordem crescente por taxa base (Lembre-se de que Ascending é o padrão):

    $orderby=Rating desc,BaseRate

Classifique os hotéis decrescentes por classificação e, em seguida, em ordem crescente por distância das coordenadas fornecidas:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Classifique os hotéis em ordem decrescente por pesquisa. Pontuação e classificação e, em seguida, em ordem crescente por distância das coordenadas fornecidas. Entre dois hotéis com pontuações e classificações de relevância idênticas, o mais próximo é listado primeiro:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passos Seguintes  

- [Como trabalhar com resultados de pesquisa em Azure Search](search-pagination-page-layout.md)
- [Visão geral da linguagem de expressão OData para Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Azure Search](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
