---
title: Enviando e usando consultas e respostas da API de pesquisa de negócios local do Bing
titleSuffix: Azure Cognitive Services
description: Use este artigo para aprender a enviar e usar consultas de pesquisa com a API de pesquisa de negócios local do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 1938bb0464eee458e7320cba4bf713a67c298d27
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882873"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Enviando e usando consultas e respostas da API de pesquisa de negócios local do Bing

Você pode obter resultados locais da API de pesquisa de negócios local do Bing enviando uma consulta de pesquisa para seu ponto de `Ocp-Apim-Subscription-Key` extremidade e incluindo o cabeçalho, que é necessário. Juntamente com os [cabeçalhos](local-search-reference.md#headers) e [parâmetros](local-search-reference.md#query-parameters)disponíveis, as pesquisas podem ser personalizadas especificando [limites](specify-geographic-search.md) geográficos para a área a ser pesquisada e as [categorias](local-search-query-response.md) de lugares retornadas.

## <a name="creating-a-request"></a>Criando uma solicitação

Para enviar uma solicitação para a API de pesquisa de negócios local do Bing, acrescente um termo `q=` de pesquisa ao parâmetro antes de adicioná-lo ao ponto de `Ocp-Apim-Subscription-Key` extremidade da API e ao incluir o cabeçalho. Por exemplo:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

A sintaxe da URL de solicitação completa é mostrada abaixo. Consulte os [guias de início rápido](quickstarts/local-quickstart.md)da API de pesquisa de negócios local do [](local-search-reference.md#headers) Bing e [](local-search-reference.md#query-parameters) conteúdo de referência para obter mais informações sobre como enviar solicitações. 

Para obter informações sobre categorias de pesquisa local, consulte [categorias de pesquisa para a API de pesquisa de negócios local do Bing](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Usando respostas

As respostas JSON da API de pesquisa comercial local do Bing `SearchResponse` contêm um objeto. A API retornará resultados de pesquisa relevantes no `places` campo. Se nenhum resultado for encontrado, o `places` campo não será incluído na resposta.

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

### <a name="search-result-attributes"></a>Atributos de resultado da pesquisa

Os resultados JSON retornados pela API incluem os seguintes atributos:

* _type
* endereço
* entityPresentationInfo
* graficamente
* id
* name
* routeablePoint
* Ligue
* url

Para obter informações gerais sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência a [API de pesquisa local do Bing v7](local-search-reference.md) .

> [!NOTE]
> Você, ou um terceiro em seu nome, não pode usar, reter, armazenar, colocar em cache, compartilhar ou distribuir quaisquer dados da API de pesquisa local com a finalidade de testar, desenvolver, treinar, distribuir ou disponibilizar qualquer serviço ou recurso que não seja da Microsoft. 


## <a name="example-json-response"></a>Exemplo de resposta JSON

A resposta JSON a seguir inclui os resultados da pesquisa especificados `?q=restaurant+in+Bellevue`pela consulta.

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
- [Início rápido de pesquisa comercial local](quickstarts/local-quickstart.md)
- [Início rápido do Java da pesquisa de negócios local](quickstarts/local-search-java-quickstart.md)
- [Início rápido do nó de pesquisa comercial local](quickstarts/local-search-node-quickstart.md)
- [Início rápido do Python de pesquisa comercial local](quickstarts/local-search-python-quickstart.md)
