---
title: Use sintaxe de consulta lucene simples
titleSuffix: Azure Cognitive Search
description: Exemplos de consulta que demonstram a sintaxe simples para pesquisa completa de texto, pesquisa de filtros e pesquisa de geo contra um índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694041"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Use a sintaxe de pesquisa "simples" na Pesquisa Cognitiva Azure

Na Pesquisa Cognitiva Azure, a [simples sintaxe de consulta](query-simple-syntax.md) invoca o parser de consulta padrão para pesquisa completa de texto. O analisador é rápido e lida com cenários comuns, incluindo pesquisa completa de texto, pesquisa filtrada e frontal, e pesquisa de prefixos. Este artigo utiliza exemplos para ilustrar o uso simples da sintaxe num pedido [de Documentos de Busca (REST API).](/rest/api/searchservice/search-documents)

> [!NOTE]
> Uma sintaxe de consulta alternativa é [Full Lucene,](query-lucene-syntax.md)suportando estruturas de consulta mais complexas, tais como pesquisa de fuzzy e wildcard. Para obter mais informações e exemplos, consulte [utilizar a sintaxe lucene completa.](search-query-lucene-examples.md)

## <a name="hotels-sample-index"></a>Índice de amostra de hotéis

As seguintes consultas baseiam-se no índice de amostras de hotéis, que pode criar seguindo as instruções neste [arranque rápido.](search-get-started-portal.md)

As consultas de exemplo são articuladas utilizando os pedidos REST API e POST. Pode colar e executá-los no [Carteiro](search-get-started-rest.md) ou no [Código do Estúdio Visual com a extensão de Pesquisa Cognitiva.](search-get-started-vs-code.md)

Os cabeçalhos de pedido devem ter os seguintes valores:

| Chave | Valor |
|-----|-------|
| Content-Type | application/json|
| api-chave  | `<your-search-service-api-key>`, quer a consulta ou a chave de administração |

Os parâmetros URI devem incluir o seu ponto final do serviço de pesquisa com o nome de índice, coleções de docs, comando de pesquisa e versão API, semelhante ao seguinte exemplo:

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

O organismo de pedido deve ser formado como JSON válido:

```json
{
    "search": "*",
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "Pesquisa" definida `*` para uma consulta não especificada, equivalente a uma busca nula ou vazia. Não é especialmente útil, mas é a pesquisa mais simples que pode fazer, e mostra todos os campos recuperáveis no índice, com todos os valores.

+ "consultaType" definido para "simples" é o padrão e pode ser omitido, mas é incluído para reforçar ainda mais que os exemplos de consulta neste artigo são expressos na sintaxe simples.

+ É utilizado um conjunto "selecionado" para uma lista de campos delimitada por vírgulas para a composição dos resultados de pesquisa, incluindo apenas os campos que são úteis no contexto dos resultados da pesquisa.

+ "contagem" devolve o número de documentos correspondentes aos critérios de pesquisa. Numa cadeia de pesquisa vazia, a contagem será de todos os documentos do índice (50 no caso de hotéis-índice de amostra).

## <a name="example-1-full-text-search"></a>Exemplo 1: Pesquisa completa por texto

A pesquisa por texto completo pode ser gratuita ou não mediar qualquer número de termos autónomos ou frases com ou sem operadores booleanos. 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

A pesquisa de palavras-chave que é composta por termos ou frases importantes tendem a funcionar melhor. Os campos de cordas são submetidos a análise de texto durante a indexação e consulta, deixando cair palavras não essenciais como "o", "e", "it". Para ver como uma cadeia de consulta é tokenizada no índice, passe a cadeia numa chamada [de Texto de Análise](/rest/api/searchservice/test-analyzer) para o índice.

O parâmetro "searchMode" controla a precisão e a recuperação. Se quiser uma maior recordação, utilize o valor "qualquer" padrão, que devolve um resultado se alguma parte da cadeia de consulta for igualada. Se favorecer a precisão, onde todas as partes da corda devem ser correspondidas, altere o SearchMode para "all". Experimente a consulta acima de ambas as formas para ver como o SearchMode muda o resultado.

A resposta para a consulta "pool spa +aeroporto" deve ser semelhante ao seguinte exemplo, aparado para a brevidade.

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

Reparem na pontuação de pesquisa na resposta. Esta é a pontuação relevante da partida. Por predefinição, um serviço de pesquisa devolverá os 50 melhores jogos com base nesta pontuação.

Pontuações uniformes de "1.0" ocorrem quando não há classificação, seja porque a pesquisa não foi completamente pesquisa de texto, ou porque não foi fornecido nenhum critério. Por exemplo, numa pesquisa vazia (search=), `*` as linhas voltam por ordem arbitrária. Quando incluir critérios reais, verá as pontuações de pesquisa evoluir em valores significativos.

## <a name="example-2-look-up-by-id"></a>Exemplo 2: Procure por ID

Quando devolve os resultados da pesquisa numa consulta, um próximo passo lógico é fornecer uma página de detalhes que inclua mais campos do documento. Este exemplo mostra-lhe como devolver um único documento utilizando [o Documento de Procura,](/rest/api/searchservice/lookup-document) passando no documento ID.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

Todos os documentos têm um identificador único. Se estiver a utilizar o portal, selecione o índice do separador **Indexes** e, em seguida, olhe para as definições de campo para determinar qual o campo que é a chave. Utilizando o REST, a chamada [Get Index](/rest/api/searchservice/get-index) devolve a definição de índice no organismo de resposta.

A resposta para a consulta acima consiste no documento cuja chave é 41. Qualquer campo marcado como "recuperável" na definição de índice pode ser devolvido em resultados de pesquisa e renderizado na sua app.

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>Exemplo 3: Filtro no texto

[A sintaxe do filtro](search-query-odata-filter.md) é uma expressão OData que pode utilizar por si só ou com "pesquisa". Usado em conjunto, o "filtro" é aplicado primeiro a todo o índice e, em seguida, a pesquisa é realizada nos resultados do filtro. Por conseguinte, os filtros podem ser uma técnica útil para melhorar o desempenho de consultas, uma vez que reduzem o conjunto de documentos necessários à consulta de pesquisa para processamento.

Os filtros podem ser definidos em qualquer campo marcado como "filtrado" na definição de índice. Para hotéis-índice de amostra, os campos filtrados incluem categoria, Tags, ParkingIncluded, Rating e a maioria dos campos address.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

A resposta para a consulta acima é apenas para os hotéis categorizados como "Report and Spa", que incluem os termos "arte" ou "tours". Neste caso, há apenas uma correspondência.

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>Exemplo 4: Funções de filtragem

As expressões de filtro podem incluir [funções de "search.ismatch" e "search.ismatchscoring",](search-query-odata-full-text-search-functions.md)permitindo-lhe construir uma consulta de pesquisa dentro do filtro. Esta expressão de filtro usa um wildcard *gratuitamente* para selecionar comodidades, incluindo wi-fi gratuito, estacionamento gratuito, etc.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

Resposta para os jogos de consulta acima em 19 hotéis que oferecem comodidades gratuitas. Note que a pontuação de pesquisa é um uniforme "1.0" ao longo dos resultados. Isto porque a expressão de pesquisa é nula ou vazia, resultando em correspondências de filtro verbatim, mas nenhuma pesquisa completa de texto. As pontuações de relevância são devolvidas apenas na pesquisa completa por texto. Se estiver a utilizar filtros sem "pesquisar", certifique-se de que dispõe de campos de classificação suficientes para que possa controlar o ranking de pesquisa.

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>Exemplo 5: Filtros de alcance

A filtragem de gama é suportada através de expressões de filtros para qualquer tipo de dado. Os exemplos a seguir ilustram gamas numéricas e de cordas. Os tipos de dados são importantes nos filtros de alcance e funcionam melhor quando os dados numéricos estão em campos numéricos e dados de cordas em campos de cordas. Os dados numéricos nos campos de cordas não são adequados para intervalos porque as cordas numéricas não são comparáveis.

A seguinte consulta é uma gama numérica. No índice de amostras de hotéis, o único campo numérico filtrado é o Rating.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

A resposta a esta consulta deve ser semelhante ao exemplo seguinte, aparado para a brevidade.

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

A próxima consulta é um filtro de alcance sobre um campo de cordas (Address/StateProvince):

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

A resposta a esta consulta deve ser semelhante ao exemplo abaixo, aparado para a brevidade. Neste exemplo, não é possível classificar pela StateProvince porque o campo não é atribuído como "ordenado" na definição de índice.

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>Exemplo 6: Geo-pesquisa

O índice de amostra de hotéis inclui um campo geo_location com coordenadas de latitude e longitude. Este exemplo utiliza a [função geo.distância](search-query-odata-geo-spatial-functions.md#examples) que filtra em documentos dentro da circunferência de um ponto de partida, para uma distância arbitrária (em quilómetros) que fornece. Pode ajustar o último valor da consulta (10) para reduzir ou ampliar a área de superfície da consulta.

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

A resposta a esta consulta devolve a todos os hotéis a uma distância de 10 km das coordenadas fornecidas:

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>Exemplo 7: Booleans com searchMode

A sintaxe simples suporta os operadores booleanos sob a forma de caracteres ( `+, -, |` ) para suportar e, OR, e NÃO consultar a lógica. A procura booleana comporta-se como seria de esperar, com algumas exceções notáveis. 

Em exemplos anteriores, o parâmetro "searchMode" foi introduzido como um mecanismo para influenciar a precisão e a recuperação, com a recuperação "searchMode=any" (um documento que satisfaz qualquer um dos critérios é considerado um fósforo), e "searchMode=all" favorecendo a precisão (todos os critérios devem ser correspondidos num documento). 

No contexto de uma pesquisa booleana, o padrão "searchMode=any" pode ser confuso se estiver a empilhar uma consulta com vários operadores e a obter resultados mais amplos em vez de resultados mais estreitos. Isto é particularmente verdade com NÃO, onde os resultados incluem todos os documentos "não contendo" um termo ou frase específico.

O exemplo a seguir fornece uma ilustração. Executando a seguinte consulta com searchMode (qualquer), são devolvidos 42 documentos: os que contêm o termo "restaurante", além de todos os documentos que não têm a frase "ar condicionado". 

Note que não há espaço entre o operador booleano `-` e a frase "ar condicionado".

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

A alteração para "searchMode=all" impõe um efeito cumulativo em critérios e devolve um conjunto de resultados menor (7 fósforos) constituído por documentos que contenham o termo "restaurante", menos os que contêm a frase "ar condicionado".

A resposta a esta consulta seria agora semelhante ao exemplo seguinte, aparado para a brevidade.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>Exemplo 8: Resultados da paging

Em exemplos anteriores, aprendeu sobre parâmetros que afetam a composição dos resultados de pesquisa, incluindo "selecionar" que determina quais os campos que estão em resultado, ordenar encomendas e como incluir uma contagem de todos os jogos. Este exemplo é uma continuação da composição do resultado da pesquisa sob a forma de parâmetros de paging que permitem-lhe emotar o número de resultados que aparecem em qualquer página. 

Por predefinição, um serviço de pesquisa devolve os 50 melhores jogos. Para controlar o número de partidas em cada página, utilize "top" para definir o tamanho do lote e, em seguida, use "skip" para recolher os lotes subsequentes.

O exemplo a seguir utiliza um filtro e uma ordem de classificação no campo de classificação (a classificação é simultaneamente filtravel e ordenada) porque é mais fácil ver os efeitos da paging nos resultados classificados. Numa consulta regular de pesquisa completa, os jogos de topo são classificados e pageed por " @search.score " "

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

A consulta encontra 21 documentos correspondentes, mas como especificou "top", a resposta retorna apenas os cinco primeiros jogos, com classificações a partir de 4.9, e terminando em 4.7 com "Lady of the Lake B & B". 

Para obter os próximos 5, salte o primeiro lote:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

A resposta para o segundo lote salta os primeiros cinco jogos, regressando aos cinco seguintes, começando com "Pull'r Inn Motel". Para continuar com lotes adicionais, manteria "top" a 5 e, em seguida, incrementaria "skip" por 5 em cada novo pedido (skip=5, skip=10, skip=15, e assim por diante).

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>Passos seguintes

Agora que tem alguma prática com a sintaxe de consulta básica, tente especificar consultas em código. Os seguintes links explicam como configurar consultas de pesquisa usando os Azure SDKs.

+ [Consultar o seu índice utilizando o .NET SDK](search-get-started-dotnet.md)
+ [Consultar o seu índice usando o Python SDK](search-get-started-python.md)
+ [Consultar o seu índice utilizando o JavaScript SDK](search-get-started-javascript.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos seguintes links:

+ [Exemplos de consulta de sintaxe lucene para a construção de consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa em texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](query-simple-syntax.md)
+ [Sintaxe de consulta Lucene completa](query-lucene-syntax.md)
+ [Sintaxe de filtro](search-query-odata-filter.md)