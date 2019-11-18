---
title: Referência de função espacial geográfica do OData
titleSuffix: Azure Cognitive Search
description: A sintaxe e a documentação de referência para o uso de funções geoespaciais, geográficas e geográficas OData. interseccionamentos, nas consultas do Azure Pesquisa Cognitiva.
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113170"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Funções geoespaciais OData no Azure Pesquisa Cognitiva-`geo.distance` e `geo.intersects`

O Azure Pesquisa Cognitiva dá suporte a consultas espaciais geográficas em [expressões de filtro OData](query-odata-filter-orderby-syntax.md) por meio das funções `geo.distance` e `geo.intersects`. A função `geo.distance` retorna a distância em quilômetros entre dois pontos, um sendo um campo ou uma variável de intervalo, e um é uma constante passada como parte do filtro. A função `geo.intersects` retorna `true` se um determinado ponto estiver dentro de um determinado polígono, em que o ponto é um campo ou uma variável de intervalo e o polígono é especificado como uma constante passada como parte do filtro.

A função `geo.distance` também pode ser usada no parâmetro [ **$OrderBy** ](search-query-odata-orderby.md) para classificar os resultados da pesquisa por distância de um determinado ponto. A sintaxe para `geo.distance` em **$OrderBy** é a mesma que está em **$Filter**. Ao usar `geo.distance` no **$OrderBy**, o campo ao qual ele se aplica deve ser do tipo `Edm.GeographyPoint` e também deve ser **classificável**.

> [!NOTE]
> Ao usar `geo.distance` no parâmetro **$OrderBy** , o campo que você passa para a função deve conter apenas um único ponto geográfico. Em outras palavras, ele deve ser do tipo `Edm.GeographyPoint` e não `Collection(Edm.GeographyPoint)`. Não é possível classificar em campos de coleção no Azure Pesquisa Cognitiva.

## <a name="syntax"></a>Sintaxe

O EBNF a seguir ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática das funções `geo.distance` e `geo.intersects`, bem como os valores espaciais geográficos nos quais elas operam:

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

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

### <a name="geodistance"></a>distância geográfica

A função `geo.distance` usa dois parâmetros do tipo `Edm.GeographyPoint` e retorna um valor `Edm.Double` que é a distância entre eles em quilômetros. Isso difere de outros serviços que dão suporte a operações geoespaciais OData, que normalmente retornam distâncias em metros.

Um dos parâmetros para `geo.distance` deve ser uma constante de ponto de Geografia e o outro deve ser um caminho de campo (ou uma variável de intervalo no caso de uma iteração de filtro sobre um campo do tipo `Collection(Edm.GeographyPoint)`). A ordem desses parâmetros não importa.

A constante de ponto de Geografia está no formato `geography'POINT(<longitude> <latitude>)'`, em que a longitude e a latitude são constantes numéricas.

> [!NOTE]
> Ao usar `geo.distance` em um filtro, você deve comparar a distância retornada pela função com uma constante usando `lt`, `le`, `gt`ou `ge`. Não há suporte para os operadores `eq` e `ne` ao comparar distâncias. Por exemplo, esse é um uso correto de `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>geo.intersects

A função `geo.intersects` usa uma variável do tipo `Edm.GeographyPoint` e uma constante `Edm.GeographyPolygon` e retorna um `Edm.Boolean` -- `true` se o ponto estiver dentro dos limites do polígono, `false` caso contrário.

O polígono é uma superfície bidimensional armazenada como uma sequência de pontos que define um anel delimitador (consulte os [exemplos](#examples) abaixo). O polígono precisa ser fechado, o que significa que os primeiros e os últimos conjuntos de pontos devem ser iguais. Os [pontos em um polígono devem estar na ordem no sentido anti-horário](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Consultas geoespaciais e polígonos abrangendo o meridiano de 180th

Para muitas bibliotecas de consultas geoespaciais formular uma consulta que inclui o meridiano 180th (próximo ao meridiano) está fora dos limites ou requer uma solução alternativa, como dividir o polígono em dois, um em ambos os lados do meridiano.

No Azure Pesquisa Cognitiva, as consultas geoespaciais que incluem a longitude de 180 graus funcionarão como esperado se a forma de consulta for retangular e suas coordenadas ficarem alinhadas a um layout de grade ao longo da longitude e da latitude (por exemplo, `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). Caso contrário, para formas não retangulares ou não alinhadas, considere a abordagem de polígono dividido.  

### <a name="geo-spatial-functions-and-null"></a>Funções espaciais geográficas e `null`

Como todos os outros campos que não são de coleção no Azure Pesquisa Cognitiva, os campos do tipo `Edm.GeographyPoint` podem conter valores `null`. Quando o Azure Pesquisa Cognitiva avalia `geo.intersects` para um campo `null`, o resultado sempre será `false`. O comportamento de `geo.distance` nesse caso depende do contexto:

- Em filtros, `geo.distance` de um campo de `null` resulta em `null`. Isso significa que o documento não será correspondente porque `null` em comparação com qualquer valor não nulo é avaliado como `false`.
- Ao classificar os resultados usando **$OrderBy**, `geo.distance` de um campo `null` resulta na distância máxima possível. Os documentos com tal campo classificarão menos do que todos os outros quando a direção de classificação `asc` for usada (o padrão) e mais alta do que todas as outras quando a direção for `desc`.

## <a name="examples"></a>Exemplos

### <a name="filter-examples"></a>Exemplos de filtro

Localiza todos os hotéis em 10 quilômetros de um determinado ponto de referência (em que Location é um campo do tipo `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Localiza todos os hotéis em um determinado visor descrito como um polígono (em que Location é um campo do tipo `Edm.GeographyPoint`). Observe que o polígono está fechado (os primeiros e os últimos conjuntos de pontos devem ser iguais) e [os pontos devem estar listados na ordem no sentido anti-horário](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Exemplos de ordenação

Classifique os hotéis decrescentes por `rating`e, em seguida, em ordem crescente por distância das coordenadas fornecidas:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Classifique os hotéis em ordem decrescente por `search.score` e `rating`e, em seguida, em ordem crescente por distância das coordenadas fornecidas para que entre dois hotéis com classificações idênticas, o mais próximo é listado primeiro:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passos seguintes  

- [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;do Azure pesquisa cognitiva API REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
