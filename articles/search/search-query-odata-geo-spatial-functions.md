---
title: Referência de funções geoespaciais de OData - Azure Search
description: OData funções geoespaciais, geo.distance e geo.intersects, nas consultas de pesquisa do Azure.
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
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079798"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>Funções geoespaciais de OData no Azure Search - `geo.distance` e `geo.intersects`

O Azure Search oferece suporte a consultas geoespaciais [expressões de filtro de OData](query-odata-filter-orderby-syntax.md) através do `geo.distance` e `geo.intersects` funções. O `geo.distance` função retorna a distância em quilômetros entre dois pontos, sendo um um campo ou variável de alcance e outro que está a ser uma constante passados como parte do filtro. O `geo.intersects` função devolve `true` se um determinado ponto estiver dentro de um polígono determinado, em que o ponto é uma variável de campo ou um intervalo e o polígono é especificado como constante passada como parte do filtro.

O `geo.distance` função também pode ser utilizada no [ **$orderby** parâmetro](search-query-odata-orderby.md) para ordenar os resultados da pesquisa por distância de um determinado ponto. A sintaxe para `geo.distance` no **$orderby** é o mesmo que está a ser **$filter**. Ao usar `geo.distance` no **$orderby**, o campo aos quais se aplica tem de ser do tipo `Edm.GeographyPoint` e também tem de ser **ordenável**.

## <a name="syntax"></a>Sintaxe

A seguinte EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática da `geo.distance` e `geo.intersects` funções, bem como os valores geoespaciais, na qual operar:

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

Um diagrama da sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama da sintaxe OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para a EBNF completa.

### <a name="geodistance"></a>Geo.Distance

O `geo.distance` função aceita dois parâmetros do tipo `Edm.GeographyPoint` e devolve um `Edm.Double` valor que é a distância entre eles em quilômetros. Isto é diferente de outros serviços que suportam operações geoespaciais de OData, o que normalmente é retornado distâncias em metros.

Um dos parâmetros para `geo.distance` tem de ser uma constante de ponto de geografia e a outro deve ser um caminho de campo (ou uma variável de alcance no caso de um filtro de iterar sobre um campo do tipo `Collection(Edm.GeographyPoint)`). Não importa a ordem desses parâmetros.

A constante de ponto de geografia é o formato `geography'POINT(<longitude> <latitude>)'`, onde a longitude e latitude são constantes numéricas.

> [!NOTE]
> Ao usar `geo.distance` num filtro, tem de comparar a distância devolvida pela função com uma utilização constante `lt`, `le`, `gt`, ou `ge`. Os operadores `eq` e `ne` não são suportadas ao comparar distâncias. Por exemplo, este é um uso correto dos `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>geo.intersects

O `geo.intersects` função usa uma variável do tipo `Edm.GeographyPoint` e uma constante `Edm.GeographyPolygon` e devolve um `Edm.Boolean`  --  `true` se o ponto estiver dentro dos limites do polígono, `false` caso contrário.

O polígono é uma superfície bidimensional armazenada como uma seqüência de pontos definindo um anel delimitadora (consulte a [exemplos](#examples) abaixo). O polígono tem de ser fechado, que significa que o primeiro e último ponto conjuntos têm de ser iguais. [Pontos de um polígono tem de ser por ordem para a esquerda](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Consultas geoespaciais e polígonos que abrange dos meridianos 180th

Para muitas consultas geoespaciais bibliotecas formular uma consulta que inclui dos meridianos 180th (perto dateline) é continua fora dos limites ou requer uma solução, por exemplo, dividindo o polígono em dois, um em ambos os lados dos meridianos.

No Azure Search, consultas geoespaciais que incluem a 180 graus de longitude funcionará como esperado se a forma de consulta é retangular e suas coordenadas alinham-se com um esquema de grelha ao longo de longitude e latitude (por exemplo, `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). Caso contrário, para as formas não retangulares ou unaligned, considere a abordagem de polígono de divisão.  

### <a name="geo-spatial-functions-and-null"></a>Funções geoespaciais e `null`

Como todos os outros campos não coleção no Azure Search, os campos do tipo `Edm.GeographyPoint` pode conter `null` valores. Quando o Azure Search avalia `geo.intersects` de um campo que está `null`, o resultado será sempre `false`. O comportamento de `geo.distance` neste caso, depende do contexto:

- Em filtros, `geo.distance` de um `null` campo resulta em `null`. Isso significa que o documento não corresponderão porque `null` em comparação com qualquer não nulo de valor avalia `false`.
- Ao classificar resultados usando **$orderby**, `geo.distance` de um `null` campo resulta na distância máxima que possível. Documentos com um campo para ordenar mais baixo que todos os outros quando a direção de ordenação `asc` é utilizada (predefinição) e maior do que todos os outros utilizadores quando a direção é `desc`.

## <a name="examples"></a>Exemplos

### <a name="filter-examples"></a>Exemplos de filtro

Localizar todos os hotéis dentro de 10 quilómetros de distância de um ponto de referência especificado (em que a localização é um campo do tipo `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Localizar todos os hotéis dentro de uma determinado da janela viewport descrita como um polígono (em que a localização é um campo do tipo `Edm.GeographyPoint`). Tenha em atenção que o polígono é fechado (os conjuntos de primeiro e último ponto devem ser o mesmo) e [os pontos devem ser listados na ordem para a esquerda](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Exemplos de ordem

Ordenar hotéis em ordem decrescente, pela `rating`, em seguida, ascendente por distância das coordenadas de determinado:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Ordenar hotéis em ordem decrescente pela `search.score` e `rating`e, em seguida, em ordem por distância crescente das coordenadas de determinado, para que entre dois hotéis com classificações idênticas, aquele mais próxima é listada em primeiro lugar:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passos Seguintes  

- [Filtros no Azure Search](search-filters.md)
- [Descrição geral de linguagem de expressão OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Procurar nos documentos &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
