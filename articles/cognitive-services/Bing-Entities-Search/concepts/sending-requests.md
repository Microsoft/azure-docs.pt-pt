---
title: Envio de pedidos de pesquisa para a API de pesquisa de entidades do Bing
titleSuffix: Azure cognitive Services
description: Saiba como enviar pedidos de pesquisa para a API de pesquisa de entidades do Bing
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9058a97b6d13d0b4af421d4d612b56fb9c5c1298
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757471"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>Envio de pedidos de pesquisa para a API de pesquisa de entidades do Bing

A API de Pesquisa de Entidades do Bing envia uma consulta de pesquisa ao Bing e obtém resultados que incluem entidades e locais. Os resultados de locais incluem restaurantes, hotéis ou outras empresas locais. Nos locais, a consulta pode especificar o nome da empresa local ou pedir uma lista (por exemplo, restaurantes perto de mim). Os resultados de entidades incluem pessoas, locais ou coisas. Os locais neste contexto são atrações turísticas, estados, países, etc. 

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="the-endpoint"></a>O ponto final

Para obter os resultados da pesquisa de entidades e locais, envie um pedido GET ao seguinte ponto final:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Pedidos têm de utilizar o protocolo HTTPS.

Recomendamos que todos os pedidos tenham origem num servidor. Distribuir a chave como parte de uma aplicação cliente abre mais oportunidades de acesso à mesma a terceiros mal-intencionados. Além disso, fazer chamadas a partir de um servidor fornece um único ponto de atualização para as futuras versões da API.

## <a name="specifying-query-parameters-and-headers"></a>Especificar parâmetros e cabeçalhos de consulta

O pedido tem de especificar o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query), que contém o termo de pesquisa do utilizador. O pedido também tem especificar o parâmetro de consulta [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt), que identifica o mercado de onde pretende que os resultados provenham. Para obter uma lista opcional de parâmetros de consulta, veja [Parâmetros de Consulta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters). Codifique com URL todos os termos de consulta.  
  
O pedido tem de especificar o cabeçalho [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey). Embora seja opcional, recomenda-se que especifique também os seguintes cabeçalhos:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

Os cabeçalhos de IP e localização do cliente são importantes para devolver o conteúdo com reconhecimento de local.  

Para obter uma lista de todos os cabeçalhos de pedido e resposta, veja [Cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>O pedido

A seguir, é-lhe mostrado um pedido de entidades que inclui todos os parâmetros e cabeçalhos de consulta sugeridos. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Se for a primeira vez que está a chamar qualquer uma das APIs do Bing, não inclua o cabeçalho de ID de cliente. Inclua apenas o ID de cliente se tiver chamado anteriormente uma API do Bing e o Bing tiver devolvido um ID de cliente para o utilizador e a combinação de dispositivo.

## <a name="the-response"></a>A resposta

O código a seguir mostra a resposta ao pedido anterior. O exemplo também mostra os cabeçalhos de resposta específicos do Bing. Para obter informações sobre o objeto de resposta, veja [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse).

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>Passos Seguintes

* [Pesquisa de entidades com a API de entidades do Bing](search-for-entities.md)
* [Requisitos de apresentação e utilização de API do Bing](../use-display-requirements.md)