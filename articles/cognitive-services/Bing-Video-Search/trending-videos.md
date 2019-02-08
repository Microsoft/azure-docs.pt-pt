---
title: Pesquisar na web para vídeos populares usando a API de pesquisa de vídeos do Bing
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar a API de pesquisa de vídeos do Bing para pesquisar a web para vídeos populares.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 2aa3c0cd9112f2de9ccda818e74b0b2c377133bf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869369"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Obtenha vídeos populares com a API de pesquisa de vídeos do Bing 

A API de pesquisa de vídeos do Bing permite-lhe encontrar vídeos populares da atualidade em toda a web e, em diferentes categorias. 

## <a name="get-request"></a>O pedido

Para aproveitar a API de pesquisa de vídeos do Bing vídeos populares da atualidade, envie o pedido GET seguinte:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Suporte do mercado

Os seguintes mercados suportam vídeos populares.  
 
-   EN-AU (inglês, Austrália)  
-   en-CA (inglês, Canadá)  
-   en-GB (em inglês, Grã-Bretanha)  
-   EN-ID (inglês, Indonésia)  
-   EN-IE (inglês, Irlanda)  
-   EN-Índia (inglês, Índia)  
-   EN-NZ (inglês, Nova Zelândia)  
-   EN-PH (inglês, Filipinas)  
-   EN-SG (inglês, Singapura)  
-   EN-US (inglês, Estados Unidos)  
-   EN-WW (inglês, Worldwide agregar código)  
-   ZA en (inglês, África do Sul)  
-   zh-CN (Chinese, China)

## <a name="example-json-response"></a>Resposta JSON de exemplo  

O exemplo seguinte mostra uma resposta de API que contém vídeos populares, que estão listados por categoria e subcategoria. A resposta também contém vídeos de faixa, que são os mais populares vídeos populares e podem vir de uma ou mais categorias.  

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
[Obtenha informações de vídeo](video-insights.md)