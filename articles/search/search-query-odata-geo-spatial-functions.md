---
title: Referência da função geo-espacial OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para a utilização de funções geo-espaciais OData, geo.distance e geo.intersects, em consultas de Pesquisa Cognitiva Azure.
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
ms.openlocfilehash: 376cece922ca424ec78011224852b1fa5499da16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88934842"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>OData funções geo-espaciais em Azure Cognitive Search - `geo.distance` e `geo.intersects`

A Azure Cognitive Search suporta consultas geo-espaciais em [expressões de filtro OData](query-odata-filter-orderby-syntax.md) através das `geo.distance` funções e `geo.intersects` funções. A `geo.distance` função devolve a distância em quilómetros entre dois pontos, sendo um um campo ou variável de alcance, e um sendo uma constante passada como parte do filtro. A `geo.intersects` função retorna `true` se um determinado ponto estiver dentro de um determinado polígono, onde o ponto é uma variável de campo ou alcance e o polígono é especificado como uma constante passada como parte do filtro.

A `geo.distance` função também pode ser usada no parâmetro [ **$orderby**](search-query-odata-orderby.md) para classificar os resultados de pesquisa por distância de um dado ponto. A sintaxe para `geo.distance` **em $orderby** é a mesma que é em **$filter**. Quando utilizar `geo.distance` em **$orderby,** o campo a que se aplica deve ser do tipo `Edm.GeographyPoint` e deve também ser **homologável**.

> [!NOTE]
> Ao utilizar `geo.distance` no parâmetro **$orderby,** o campo que passa para a função deve conter apenas um único ponto geoponto. Por outras palavras, deve ser do tipo `Edm.GeographyPoint` e `Collection(Edm.GeographyPoint)` não. Não é possível ordenar em campos de recolha em Azure Cognitive Search.

## <a name="syntax"></a>Syntax

O seguinte formulário EBNF ([Formulário Backus-Naur Alargado)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática das `geo.distance` funções e `geo.intersects` funções, bem como os valores geo-espaciais em que operam:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Está também disponível um diagrama de sintaxe interativo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para pesquisa cognitiva de Azure](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Consulte [a referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para o EBNF completo.

### <a name="geodistance"></a>geo.distância

A `geo.distance` função requer dois parâmetros de tipo `Edm.GeographyPoint` e devolve um valor que é a distância entre eles em `Edm.Double` quilómetros. Isto difere de outros serviços que suportam operações geo-espaciais OData, que normalmente regressam distâncias em metros.

Um dos parâmetros `geo.distance` deve ser uma constante de ponto de geografia, e o outro deve ser um caminho de campo (ou uma variável de alcance no caso de um filtro iterando sobre um campo de `Collection(Edm.GeographyPoint)` tipo). A ordem destes parâmetros não importa.

A constante do ponto geografo é da `geography'POINT(<longitude> <latitude>)'` forma, onde a longitude e a latitude são constantes numéricas.

> [!NOTE]
> Ao utilizar `geo.distance` num filtro, deve comparar a distância devolvida pela função com uma utilização `lt` `le` `gt` constante, ou `ge` . Os operadores `eq` e `ne` não são apoiados quando comparam distâncias. Por exemplo, esta é uma utilização correta `geo.distance` de: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5` .

### <a name="geointersects"></a>geo.intersecta

A `geo.intersects` função toma uma variável de tipo `Edm.GeographyPoint` e uma constante e devolve um se o ponto estiver dentro dos `Edm.GeographyPolygon` `Edm.Boolean`  --  `true` limites do polígono, `false` caso contrário.

O polígono é uma superfície bidimensional armazenada como uma sequência de pontos que definem um anel de delimitação (ver os [exemplos](#examples) abaixo). O polígono tem de ser fechado, o que significa que os conjuntos de primeiro e último ponto devem ser os mesmos. [Os pontos num polígono devem estar em ordem anti-horário](/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Consultas geo-espaciais e polígonos abrangendo o 180º meridiano

Para muitas bibliotecas de consultas geo-espaciais que formulam uma consulta que inclui o 180º meridiano (perto da linha de data) ou está fora dos limites ou requer uma solução alternativa, como dividir o polígono em dois, um em ambos os lados do meridiano.

Em Azure Cognitive Search, as consultas geo-espaciais que incluem longitude de 180 graus funcionarão como esperado se a forma de consulta for retangular e as suas coordenadas se alinharem com um layout de grelha ao longo da longitude e latitude (por exemplo, `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'` ). Caso contrário, para formas não retangulares ou desalinhadas, considere a abordagem do polígono dividido.  

### <a name="geo-spatial-functions-and-null"></a>Funções geo-espaciais e `null`

Como todos os outros campos de não-recolha na Pesquisa Cognitiva Azure, os campos de tipo `Edm.GeographyPoint` podem conter `null` valores. Quando a Azure Cognitive Search avaliar `geo.intersects` para um campo que é , o resultado será sempre `null` `false` . O comportamento `geo.distance` deste caso depende do contexto:

- Em filtros, `geo.distance` de um campo resulta em `null` `null` . Isto significa que o documento não corresponderá porque `null` em comparação com qualquer valor não nulo avalia a `false` .
- Ao classificar os resultados utilizando **$orderby,** `geo.distance` de um campo resulta na distância máxima `null` possível. Os documentos com este campo classificam-se mais baixo do que todos os outros quando a direção de tipo `asc` é utilizada (o padrão), e mais alta do que todas as outras quando a direção é `desc` .

## <a name="examples"></a>Exemplos

### <a name="filter-examples"></a>Exemplos de filtro

Encontre todos os hotéis a menos de 10 km de um determinado ponto de referência (onde a localização é um campo de `Edm.GeographyPoint` tipo):

```odata-filter-expr
    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

Encontre todos os hotéis dentro de um determinado viewport descrito como um polígono (onde a localização é um campo de `Edm.GeographyPoint` tipo). Note que o polígono está fechado (os conjuntos de primeiro e último ponto devem ser os [mesmos) e os pontos devem ser listados por ordem anti-horário](/rest/api/searchservice/supported-data-types#Anchor_1).

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

### <a name="order-by-examples"></a>Exemplos de encomenda

Classificar hotéis `rating` descendo, em seguida, subindo por distância das coordenadas:

```odata-filter-expr
    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

Ordenar hotéis em ordem descendente por `search.score` `rating` e, em seguida, em ordem ascendente por ordem ascendente das coordenadas dadas para que entre dois hotéis com classificações idênticas, o mais próximo é listado primeiro:

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva do Azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para pesquisa cognitiva de Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)