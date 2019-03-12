---
title: Enviar e utilizar consultas de API de pesquisa do Bing Local comerciais e as respostas | Documentos da Microsoft
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para saber como enviar e utilizar consultas de pesquisa com a API de pesquisa de negócios locais do Bing.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh; v-gedod
ms.openlocfilehash: 79219de775be96f35bd11ac85640efcc4a04a93b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779798"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Enviar e utilizar consultas de API de pesquisa do Bing Local comerciais e respostas

Pode obter resultados locais a partir da API de pesquisa de negócios Local do Bing ao enviar uma consulta de pesquisa para o seu ponto de extremidade e incluir o `Ocp-Apim-Subscription-Key` cabeçalho, que é necessário. Juntamente com disponíveis [cabeçalhos](local-search-reference.md#headers) e [parâmetros](local-search-reference.md#query-parameters), pesquisas podem ser personalizadas, especificando [fronteiras geográficas](specify-geographic-search.md) para a área a ser pesquisada e a [categorias](local-search-query-response.md) lugares devolvido.

## <a name="creating-a-request"></a>Criar um pedido

Para enviar um pedido para a API de pesquisa de negócios locais do Bing, acrescente um termo de pesquisa para o `q=` parâmetro antes de adicioná-lo para o ponto final de API e incluindo o `Ocp-Apim-Subscription-Key` cabeçalho. Por exemplo:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

A sintaxe de URL do pedido completo é mostrada abaixo. Ver a API de pesquisa de negócios locais do Bing [inícios Rápidos](quickstarts/local-quickstart.md)e conteúdo de referência [cabeçalhos](local-search-reference.md#headers) e [parâmetros](local-search-reference.md#query-parameters) para obter mais informações sobre como enviar pedidos. 

Para obter informações sobre as categorias de pesquisa local, consulte [categorias de pesquisa para a API de pesquisa de negócios locais do Bing](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Usando as respostas

Respostas JSON da API de pesquisa de negócios locais do Bing contêm um `SearchResponse` objeto. A API irá devolver resultados de pesquisa relevantes no `places` campo. não se for encontrado nenhum resultado, o `places` campo não será incluído na resposta.

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

### <a name="search-result-attributes"></a>Atributos de resultado de pesquisa

Os resultados JSON devolvidos pela API incluem os seguintes atributos:

* _type
* endereço
* entityPresentationInfo
* geo
* ID
* nome
* routeablePoint
* Telefone
* url

Para obter informações gerais sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a [Local API de pesquisa do Bing v7](local-search-reference.md) referência.

> [!NOTE]
> , Ou uma aplicação de terceiros em seu nome, poderá não utilizar, manter, armazenar, colocar em cache, partilhar, ou distribuir quaisquer dados da API de pesquisa Local para fins de teste, desenvolvimento, treinamento, distribuir ou ao disponibilizá-se de que qualquer serviço de terceiros ou de recursos. 


## <a name="example-json-response"></a>Resposta JSON de exemplo

A resposta JSON seguinte inclui os resultados de pesquisa especificados pela consulta `?q=restaurant+in+Bellevue`.

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


## <a name="next-steps"></a>Passos Seguintes
- [Guia de introdução de pesquisa de negócios local](quickstarts/local-quickstart.md)
- [Início rápido de Java de pesquisa de negócios local](quickstarts/local-search-java-quickstart.md)
- [Guia de introdução de nó de pesquisa de negócios local](quickstarts/local-search-node-quickstart.md)
- [Guia de introdução de Python de pesquisa de negócios local](quickstarts/local-search-python-quickstart.md)
