---
title: Envio de pedidos de pesquisa para a API de pesquisa de vídeos do Bing
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre o envio de consultas de pesquisa para a API de pesquisa de vídeos do Bing.
services: cognitive-services
author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: aahill
ms.openlocfilehash: 4049042dc485725924349708e717ae48e8458ef7
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274647"
---
# <a name="sending-search-requests-to-the-bing-video-search-api"></a>Envio de pedidos de pesquisa para a API de pesquisa de vídeos do Bing

Este artigo descreve os parâmetros e atributos de pedidos enviados para a API de pesquisa de vídeos do Bing, bem como o objeto de resposta JSON retorna.

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Sugerir termos de pesquisa com a API de sugestão automática do Bing

Se disponibilizar uma caixa de pesquisa na qual o utilizador introduz o seu termo de pesquisa, utilize a [API de Sugestão Automática do Bing](../../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API devolve cadeias de consulta sugerida com base em termos de pesquisa parcial à medida que o utilizador escreve.

Depois do usuário insere seu termo de pesquisa, codificação de URL-lo antes de definir o [p](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) parâmetro de consulta. Por exemplo, se o utilizador introduzir *sailing dinghies*, defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

## <a name="sending-a-request"></a>Enviar um pedido

Para obter os resultados da pesquisa de vídeo, enviaria um pedido GET ao seguinte ponto final:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
O pedido tem de utilizar o protocolo HTTPS.

Recomendamos que todos os pedidos tenham origem num servidor. Distribuir a chave como parte de uma aplicação cliente abre mais oportunidades de acesso à mesma a terceiros mal-intencionados. Também fazer chamadas a partir de um servidor fornece um único ponto de atualização para futuras versões da API.

  
O pedido tem de especificar o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query), que contém o termo de pesquisa do utilizador. Embora seja opcional, o pedido deve também especificar o parâmetro de consulta [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#mkt), que identifica o mercado de onde pretende que os resultados provenham. Para obter uma lista opcional de parâmetros de consulta, como `pricing`, veja [Parâmetros de Consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query-parameters). Todos os valores de parâmetro de consulta têm de estar codificados com URL.  
  
O pedido tem de especificar o cabeçalho [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#subscriptionkey). Embora seja opcional, recomenda-se que especifique também os seguintes cabeçalhos:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#location)  

Os cabeçalhos de IP e localização do cliente são importantes para devolver o conteúdo com reconhecimento de local.  

Para obter uma lista de todos os cabeçalhos de pedido e resposta, veja [Cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#headers).

## <a name="example-search-request"></a>Exemplo de solicitação de pesquisa

A seguir, é-lhe mostrado um pedido de pesquisa que inclui todos os parâmetros de consulta e cabeçalhos sugeridos. Se for a primeira vez que está a chamar qualquer uma das APIs do Bing, não inclua o cabeçalho de ID de cliente. Inclua apenas o ID de cliente se tiver chamado anteriormente uma API do Bing e o Bing tiver devolvido um ID de cliente para o utilizador e a combinação de dispositivo. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

## <a name="example-json-response"></a>Resposta JSON de exemplo

O código a seguir mostra a resposta ao pedido anterior. O exemplo também mostra os cabeçalhos de resposta específicos do Bing.

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Passos Seguintes

Experimente a API. Aceda a [Consola de Testes da API da Pesquisa de Vídeos](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8). 

Para obter detalhes sobre os objetos de resposta, veja [Pesquisar Vídeos na Web](../search-the-web.md).

Para obter detalhes de como obter informações sobre um vídeo, como pesquisas relacionadas, veja [Informações de Vídeo](../video-insights.md).  
  
Para obter detalhes sobre os vídeos que são mais populares nas redes sociais, veja [Vídeos Populares](../trending-videos.md).  
