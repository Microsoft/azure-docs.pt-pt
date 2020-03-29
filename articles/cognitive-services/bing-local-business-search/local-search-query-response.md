---
title: Envio e utilização de consultas e respostas da API - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Use este artigo para aprender a enviar e utilizar consultas de pesquisa com a API de Pesquisa de Negócios Local Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 25bcdb89002fec4f9b67b091996d7bf80bcf21c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74326728"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Envio e utilização de consultas e respostas da API de Pesquisa de Negócios Locais

Pode obter resultados locais da API de Pesquisa de Negócios Local `Ocp-Apim-Subscription-Key` bing, enviando uma consulta de pesquisa para o seu ponto final e incluindo o cabeçalho, que é necessário. Juntamente com [os cabeçalhos](local-search-reference.md#headers) e [parâmetros](local-search-reference.md#query-parameters)disponíveis, as pesquisas podem ser personalizadas especificando [limites geográficos](specify-geographic-search.md) para a área a pesquisar, e as categorias de [lugares devolvidos.](local-search-query-response.md)

## <a name="creating-a-request"></a>Criar um pedido

Para enviar um pedido para a API de Pesquisa De `q=` Negócios Local Bing, anexar um termo `Ocp-Apim-Subscription-Key` de pesquisa ao parâmetro antes de adicioná-lo ao ponto final da API, incluindo o cabeçalho. Por exemplo:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

A sintaxe url de pedido completo é mostrada abaixo. Consulte os [quickstarts](quickstarts/local-quickstart.md)da API de Pesquisa de Negócios Local Bing, e conteúdo de referência para [cabeçalhos](local-search-reference.md#headers) e [parâmetros](local-search-reference.md#query-parameters) para mais informações sobre o envio de pedidos. 

Para obter informações sobre as categorias de pesquisa locais, consulte as categorias de [pesquisa para a API](local-categories.md)local de pesquisa de negócios bing .

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Usando respostas

As respostas da JSON da API `SearchResponse` de Pesquisa de Negócios Local Bing contêm um objeto. A API devolverá os `places` resultados de pesquisa relevantes no campo. se não forem `places` encontrados resultados, o campo não será incluído na resposta.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Atributos de resultados de pesquisa

Os resultados da JSON devolvidos pela API incluem os seguintes atributos:

* _type
* address
* entidadePresentationInfo
* geo
* ID
* nome
* routeablePoint
* telefone
* url

Para obter informações gerais sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [Bing Local Search API v7.](local-search-reference.md)

> [!NOTE]
> Você, ou terceiros em seu nome, não pode usar, reter, armazenar, cache, partilhar ou distribuir quaisquer dados da API local para efeitos de teste, desenvolvimento, formação, distribuição ou disponibilização de qualquer serviço ou funcionalidade não Microsoft. 


## <a name="example-json-response"></a>Exemplo resposta JSON

A seguinte resposta JSON inclui os resultados de pesquisa especificados pela consulta `?q=restaurant+in+Bellevue`.

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Limitar pedidos

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Passos seguintes
- [Pesquisa de negócios locais quickstart](quickstarts/local-quickstart.md)
- [Pesquisa de negócios locais Java quickstart](quickstarts/local-search-java-quickstart.md)
- [Local Business Search Node quickstart](quickstarts/local-search-node-quickstart.md)
- [Pesquisa de negócios local Python quickstart](quickstarts/local-search-python-quickstart.md)
