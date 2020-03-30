---
title: Referência da função geoespacial OData
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113170"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>OData funções geo-espaciais em `geo.distance` Pesquisa Cognitiva Azure - e`geo.intersects`

A Pesquisa Cognitiva Azure suporta consultas geo-espaciais nas `geo.distance` `geo.intersects` expressões de [filtro OData](query-odata-filter-orderby-syntax.md) através das funções e funções. A `geo.distance` função devolve a distância em quilómetros entre dois pontos, sendo um sendo uma variável de campo ou de alcance, e sendo um constante passado como parte do filtro. A `geo.intersects` função retorna `true` se um determinado ponto estiver dentro de um determinado polígono, onde o ponto é uma variável de campo ou de alcance e o polígono é especificado como uma constante passada como parte do filtro.

A `geo.distance` função também pode ser utilizada no parâmetro [ **$orderby** ](search-query-odata-orderby.md) para classificar os resultados de pesquisa à distância de um determinado ponto. A sintaxe para `geo.distance` **$orderby** é a mesma que é em **$filter**. Quando `geo.distance` se utiliza em **$orderby,** o campo a `Edm.GeographyPoint` que se aplica deve ser de tipo e deve também ser **classificado.**

> [!NOTE]
> Quando `geo.distance` utilizar o parâmetro **$orderby,** o campo que passa para a função deve conter apenas um ponto geo-ponto. Por outras palavras, `Edm.GeographyPoint` deve `Collection(Edm.GeographyPoint)`ser de tipo e não. Não é possível classificar em campos de recolha em Azure Cognitive Search.

## <a name="syntax"></a>Sintaxe

O seguinte EBNF ([Formulário Backus-Naur Estendido)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)define a gramática das `geo.distance` funções e funções, `geo.intersects` bem como os valores geo-espaciais em que operam:

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
> [Diagrama de sintaxe OData para pesquisa cognitiva azure](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Consulte a [referência sintaxe de expressão OData para a Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

### <a name="geodistance"></a>geo.distância

A `geo.distance` função requer dois `Edm.GeographyPoint` parâmetros `Edm.Double` de tipo e devolve um valor que é a distância entre eles em quilómetros. Isto difere de outros serviços que suportam operações geoespaciais OData, que normalmente devolvem distâncias em metros.

Um dos parâmetros `geo.distance` a ser uma constante de ponto de geografia, e o outro deve ser um percurso de campo `Collection(Edm.GeographyPoint)`(ou uma variável de alcance no caso de uma iteração de filtro sobre um campo de tipo). A ordem destes parâmetros não importa.

A constante do ponto de `geography'POINT(<longitude> <latitude>)'`geografia é da forma, onde a longitude e a latitude são constantes numéricas.

> [!NOTE]
> Quando `geo.distance` utilizar um filtro, deve comparar a distância devolvida `lt`pela `le` `gt`função `ge`com uma utilização constante, ou . Os `eq` operadores e `ne` não são suportados na comparação das distâncias. Por exemplo, trata-se `geo.distance`de `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`uma utilização correta de: .

### <a name="geointersects"></a>geo.intersects

A `geo.intersects` função requer `Edm.GeographyPoint` uma variável de tipo e uma constante `Edm.GeographyPolygon` e devolve um `Edm.Boolean`  --  `true` se o ponto estiver dentro dos limites do polígono, `false` caso contrário.

O polígono é uma superfície bidimensional armazenada como uma sequência de pontos que definem um anel de limitador (ver os [exemplos](#examples) abaixo). O polígono tem de ser fechado, o que significa que os conjuntos de primeiro e último ponto devem ser os mesmos. [Os pontos num polígono devem estar em ordem anti-horário](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Consultas geo-espaciais e polígonos abrangendo o 180º meridiano

Para muitas bibliotecas de consulta geo-espacial que formulam uma consulta que inclui o 180º meridiano (perto da linha de data) ou está fora dos limites ou requer uma salieste, como dividir o polígono em dois, um em ambos os lados do meridiano.

Na Pesquisa Cognitiva Azure, consultas geo-espaciais que incluem longitude de 180 graus funcionarão como esperado se a forma de consulta for retangular e as suas coordenadas alinharem-se com um layout da grelha ao longo da longitude e latitude (por exemplo, `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). Caso contrário, para formas não retangulares ou desalinhadas, considere a abordagem do polígono dividido.  

### <a name="geo-spatial-functions-and-null"></a>Funções geo-espaciais e`null`

Como todos os outros campos não-coletores `Edm.GeographyPoint` em `null` Azure Cognitive Search, os campos de tipo podem conter valores. Quando a Pesquisa Cognitiva `geo.intersects` Azure avaliar `null`para um campo `false`que é, o resultado será sempre . O comportamento `geo.distance` deste caso depende do contexto:

- Nos filtros, `geo.distance` `null` de um `null`campo resulta em . Isto significa que o `null` documento não corresponderá porque, em `false`comparação com qualquer valor não nulo avaliado a .
- Ao classificar os resultados `null` utilizando **$orderby**, `geo.distance` de um campo resulta na distância máxima possível. Os documentos com tal campo classificar-se-ão mais baixo sem qualquer outro quando a direção `asc` de classificação for utilizada (o padrão), e superior a todos os outros quando a direção é `desc`.

## <a name="examples"></a>Exemplos

### <a name="filter-examples"></a>Exemplos de filtro

Encontre todos os hotéis num raio de 10 km `Edm.GeographyPoint`de um determinado ponto de referência (onde a localização é um campo de tipo):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Encontre todos os hotéis dentro de um determinado viewport descrito `Edm.GeographyPoint`como um polígono (onde a localização é um campo de tipo). Note que o polígono está fechado (os conjuntos de primeiro e último ponto devem ser os mesmos) e [os pontos devem ser enumerados no sentido contrário ao dos ponteiros do relógio](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Exemplos de encomenda

Ordenar hotéis `rating`descendo, em seguida, ascendendo à distância das coordenadas dadas:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Ordenar hotéis `search.score` em `rating`ordem descendente por e, em seguida, em ordem ascendente por distância das coordenadas dadas para que entre dois hotéis com classificações idênticas, o mais próximo é listado primeiro:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Passos seguintes  

- [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
- [Visão geral da linguagem de expressão OData para pesquisa cognitiva azure](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva Azure](search-query-odata-syntax-reference.md)
- [Documentos de pesquisa &#40;pesquisa cognitiva azure REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
