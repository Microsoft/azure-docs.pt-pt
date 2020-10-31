---
title: Procure na web vídeos de tendência usando a API de Pesquisa de Vídeo Bing
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API de Pesquisa de Vídeo Bing para pesquisar na web vídeos de tendência.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 8232af6cb409628a1066a044a196777ddc46348f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098972"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Obtenha vídeos de tendência com a API de Pesquisa de Vídeo Bing 

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

A API de Pesquisa de Vídeo Bing permite-lhe encontrar os vídeos de tendência de hoje em toda a web e em diferentes categorias. 

## <a name="get-request"></a>Pedido GET

Para obter os vídeos de tendência de hoje da API de Pesquisa de Vídeo Bing, envie o seguinte pedido GET:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Apoio ao mercado

Os mercados seguintes suportam vídeos de tendência.  
 
-   en-AU (Inglês, Austrália)  
-   en-CA (Inglês, Canadá)  
-   en-GB (Inglês, Grã-Bretanha)  
-   en-ID (Inglês, Indonésia)  
-   en-IE (Inglês, Irlanda)  
-   en-IN (Inglês, Índia)  
-   en-NZ (Inglês, Nova Zelândia)  
-   en-PH (Inglês, Filipinas)  
-   en-SG (inglês, Singapura)  
-   en-US (Inglês, Estados Unidos)  
-   en-WW (inglês, código agregado mundial)  
-   en-ZA (Inglês, África do Sul)  
-   zh-CN (Chinês, China)

## <a name="example-json-response"></a>Exemplo JSON resposta  

O exemplo a seguir mostra uma resposta API que contém vídeos de tendência, que são listados por categoria e subcategoria. A resposta também contém vídeos banner, que são os vídeos de tendência mais populares, e podem vir de uma ou mais categorias.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Obter informações de vídeo](video-insights.md)