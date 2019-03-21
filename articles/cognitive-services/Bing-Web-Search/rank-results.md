---
title: Como utilizar as classificações para apresentar os resultados da pesquisa - API de pesquisa Web Bing
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a classificação de mensagens em fila para exibir os resultados da API de pesquisa Web do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 9a49c4af474d7f0618bf0cff1a093e5cbb62fe2d
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295611"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Como utilizar a classificação de mensagens em fila para apresentar os resultados da API de pesquisa Web Bing  

Cada resposta de pesquisa inclui um [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) resposta, que especifica a forma como deve exibir os resultados da pesquisa. A resposta de classificação agrupa os resultados por mainline conteúdo e conteúdo da barra lateral para a página de resultados de uma pesquisa tradicional. Se não exibir os resultados num tradicional principal e o formato de barra lateral, tem de fornecer a visibilidade mais elevada do que o conteúdo da barra lateral do conteúdo principal.  

Em cada grupo (mainline ou a barra lateral), o [itens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) matriz identifica a ordem em que o conteúdo tem de aparecer no. Cada item fornece duas formas seguintes para identificar o resultado numa resposta.  

-   `answerType` e `resultIndex` — o `answerType` campo identifica a resposta (por exemplo, a página Web ou a notícias) e `resultIndex` identifica um resultado na resposta (por exemplo, um artigo de notícias). O índice é baseado em zero.  

-   `value` — O `value` campo contém um ID que corresponde ao ID de uma resposta ou um resultado na resposta. A resposta ou os resultados contêm o ID, mas não ambos.  

Com o ID é mais fácil de utilizar porque só tem de corresponder ao ID de classificação com o ID de uma resposta ou um dos seus resultados. Se um objeto de resposta inclui uma `id` campo, exibir os resultados de todos os a resposta em conjunto. Por exemplo, se o `News` objeto inclui o `id` campo, apresentar todos os artigos de notícias em conjunto. Se o `News` objeto não inclui o `id` campo, em seguida, cada artigo de notícias contém um `id` campo e a resposta de classificação mistura os artigos de notícias com os resultados de outras respostas.  

Utilizar o `answerType` e `resultIndex` é um pouco mais complicado. Utilizar `answerType` para identificar a resposta que contém os resultados para apresentar. Em seguida, utilize `resultIndex` ao índice através de resultados a resposta para obter o resultado para apresentar. (A `answerType` valor é o nome do campo no [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objeto.) Se deverá para exibir resultados de todos os a resposta em conjunto, a resposta de classificação item não inclui o `resultIndex` campo.  

## <a name="ranking-response-example"></a>Exemplo de resposta de classificação

O seguinte mostra um exemplo [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse). Uma vez que a resposta Web não inclui um `id` campo, seria exibida todas as páginas Web individualmente com base na classificação (cada página da Web inclui um `id` campo). E porque as imagens, vídeos e respostas de pesquisas relacionadas incluem a `id` campo, poderia exibir os resultados de cada um desses respostas em conjunto com base na classificação.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

Com base na resposta classificação, a principal seria exibida os seguintes resultados de pesquisa:  

-   O primeiro resultado de página Web
-   Todas as imagens  
-   Os resultados da segunda e terceira página da Web  
-   Todos os vídeos  
-   Os resultados de página Web de 4, 5 e a 6ª  

E a barra lateral seria exibida os seguintes resultados de pesquisa:  

-   Todas as pesquisas relacionadas  


## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre como promover unranked resultados, consulte [promover as respostas não estão ordenadas](./filter-answers.md#promoting-answers-that-are-not-ranked).

Para obter informações sobre como limitar o número de respostas de classificações na resposta, consulte [limitar o número de respostas na resposta](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Para um exemplo c# que utiliza a classificação de mensagens em fila para apresentar resultados, consulte [tutorial de classificação do c#](./csharp-ranking-tutorial.md).
