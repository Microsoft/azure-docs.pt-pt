---
title: Pesquise vídeos de tendências na Web usando o API de Pesquisa de Vídeo do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como usar o API de Pesquisa de Vídeo do Bing para pesquisar vídeos de tendências na Web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: ea6f79bf6c305ecc07b3e684ede15ad439039e7e
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500618"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Obtenha vídeos de tendências com o API de Pesquisa de Vídeo do Bing 

O API de Pesquisa de Vídeo do Bing permite que você encontre vídeos de tendência de hoje em toda a Web e em categorias diferentes. 

## <a name="get-request"></a>OBTER solicitação

Para obter vídeos de tendência de hoje da API de Pesquisa de Vídeo do Bing, envie a seguinte solicitação GET:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Suporte ao mercado

Os seguintes mercados dão suporte a vídeos de tendência.  
 
-   en-AU (Inglês, Austrália)  
-   en-CA (Inglês, Canadá)  
-   en-GB (Inglês, Grã-Bretanha)  
-   EN-ID (Inglês, Indonésia)  
-   EN-IE (Inglês, Irlanda)  
-   en-IN (Inglês, Índia)  
-   en-NZ (Inglês, Nova Zelândia)  
-   EN-PH (Inglês, Filipinas)  
-   EN-SG (Inglês, Cingapura)  
-   en-US (Inglês, Estados Unidos)  
-   EN-WW (Inglês, código de agregação mundial)  
-   EN-ZA (Inglês, África do Sul)  
-   ZH-CN (chinês, China)

## <a name="example-json-response"></a>Exemplo de resposta JSON  

O exemplo a seguir mostra uma resposta de API que contém vídeos de tendência, que são listados por categoria e subcategoria. A resposta também contém vídeos de faixa, que são os vídeos mais populares de tendências e podem vir de uma ou mais categorias.  

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Obter informações de vídeo](video-insights.md)