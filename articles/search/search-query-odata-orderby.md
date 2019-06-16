---
title: OData por ordem referência - Azure Search
description: Referência de linguagem de OData para a sintaxe de ordem nas consultas de pesquisa do Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 1ced35dc73e6d596fbeda32590ab0b69df396c5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079759"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Sintaxe de OData $orderby no Azure Search

 Pode utilizar o [OData **$orderby** parâmetro](query-odata-filter-orderby-syntax.md) para aplicar uma ordem de classificação personalizada para os resultados da pesquisa no Azure Search. Este artigo descreve a sintaxe de **$orderby** detalhadamente. Para obter mais informações sobre como utilizar **$orderby** ao apresentar os resultados da pesquisa, veja [como trabalhar com a pesquisa resulta no Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxe

O **$orderby** parâmetro aceita uma lista separada por vírgulas de até 32 **por ordem cláusulas**. A sintaxe de uma cláusula order by é descrita pela EBNF seguinte ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

Cada cláusula tem critérios de ordenação, seguidos opcionalmente uma direção de ordenação (`asc` para ascendente ou `desc` para descendente). Se não especificar uma direção, a predefinição é ascendente. Os critérios de classificação podem ser o caminho de um `sortable` campo ou uma chamada para ao [ `geo.distance` ](search-query-odata-geo-spatial-functions.md) ou o [ `search.score` ](search-query-odata-search-score-function.md) funções.

Se vários documentos têm os mesmos critérios de ordenação e a `search.score` a função não é utilizada (por exemplo, se ordenar por um numérico `Rating` campo e de três documentos para todos os tem uma classificação de 4), empates irão ser corrompidas pela pontuação de documentos por ordem descendente. Quando as pontuações de documento são as mesmas (por exemplo, quando há qualquer consulta de pesquisa em texto completo especificada no pedido), em seguida, a ordenação relativa dos documentos empatados é indeterminada.

É possível especificar vários critérios de ordenação. A ordem das expressões determina a ordem de classificação final. Por exemplo, para classificar a ordem decrescente, pela pontuação, seguida de classificação, a sintaxe seria `$orderby=search.score() desc,Rating desc`.

A sintaxe para `geo.distance` no **$orderby** é o mesmo que está a ser **$filter**. Ao usar `geo.distance` no **$orderby**, o campo aos quais se aplica tem de ser do tipo `Edm.GeographyPoint` e também tem de ser `sortable`.

A sintaxe para `search.score` no **$orderby** é `search.score()`. A função `search.score` não tem nenhum parâmetro.

## <a name="examples"></a>Exemplos

Hotéis de ordenação ascendente por taxa base:

    $orderby=BaseRate asc

Ordenar hotéis em ordem decrescente, pela classificação, em seguida, ascendente por taxa base (Lembre-se de que ascendente é a predefinição):

    $orderby=Rating desc,BaseRate

Ordenar hotéis em ordem decrescente, pela classificação, em seguida, ascendente por distância das coordenadas de determinado:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Ordenar hotéis em ordem decrescente pela search.score e classificação e, em seguida, ascendentemente pela distância das coordenadas de determinado. Entre dois hotéis com pontuações de relevância idênticos e classificações, aquele mais próxima é listada em primeiro lugar:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passos Seguintes  

- [Como trabalhar com a pesquisa resulta no Azure Search](search-pagination-page-layout.md)
- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
