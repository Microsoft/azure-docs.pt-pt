---
title: Como utilizar rankings para exibir resultados de pesquisa - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Aprenda a usar o ranking para exibir os resultados da pesquisa a partir da API de pesquisa web bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 677f6089f649aae720a6303a7e1512e3c7ebeca7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "66390126"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Como usar o ranking para exibir os resultados da API de Pesquisa web do Bing  

Cada resposta de pesquisa inclui uma resposta [rankingResponse,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) que especifica como deve apresentar os resultados da pesquisa. Os grupos de resposta ao ranking resultam pelo conteúdo da linha principal e pelo conteúdo da barra lateral para uma página de resultados de pesquisa tradicional. Se não apresentar os resultados num formato tradicional de linha principal e barra lateral, deve fornecer ao conteúdo principal uma maior visibilidade do que o conteúdo da barra lateral.  

Dentro de cada grupo (linha principal ou barra lateral), a matriz [de itens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) identifica a ordem em que o conteúdo deve aparecer. Cada item fornece as seguintes duas formas de identificar o resultado dentro de uma resposta.  

-   `answerType`e `resultIndex` — `answerType` O campo identifica a resposta (por exemplo, página web ou Notícias) e `resultIndex` identifica um resultado dentro da resposta (por exemplo, um artigo de notícias). O índice é zero baseado.  

-   `value`— `value` O campo contém um ID que corresponde ao ID de uma resposta ou um resultado dentro da resposta. Ou a resposta ou os resultados contêm a identificação, mas não ambas.  

A utilização do ID é mais simples de utilizar porque só precisa de combinar o ID do ranking com o ID de uma resposta ou um dos seus resultados. Se um objeto `id` de resposta incluir um campo, mostre todos os resultados da resposta em conjunto. Por exemplo, `News` se o `id` objeto incluir o campo, exiba todos os artigos de notícias juntos. Se `News` o objeto não `id` incluir o campo, `id` então cada artigo de notícias contém um campo e a resposta do ranking mistura os artigos de notícias com os resultados de outras respostas.  

Usar `answerType` o `resultIndex` e é um pouco mais complicado. Usa `answerType` para identificar a resposta que contém os resultados a apresentar. Em seguida, `resultIndex` usa para indexar através dos resultados da resposta para obter o resultado para exibir. (O `answerType` valor é o nome do campo no objeto [SearchResponse.)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) Se é suposto mostrares todos os resultados da resposta em conjunto, `resultIndex` o item de resposta do ranking não inclui o campo.  

## <a name="ranking-response-example"></a>Exemplo de resposta de ranking

O seguinte mostra um exemplo [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Como a resposta web `id` não inclui um campo, exibiria todas as páginas web `id` individualmente com base no ranking (cada página web inclui um campo). E como as imagens, vídeos e respostas relacionadas incluem o `id` campo, exibimos os resultados de cada uma dessas respostas em conjunto com base no ranking.

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

Com base nesta resposta de ranking, o mainline apresentaria os seguintes resultados de pesquisa:  

-   O primeiro resultado da página web
-   Todas as imagens  
-   Os resultados da segunda e terceira página web  
-   Todos os vídeos  
-   Os resultados da 4ª, 5ª e 6ª página web  

E a barra lateral mostraria os seguintes resultados de pesquisa:  

-   Todas as pesquisas relacionadas  


## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre a promoção de resultados não classificados, consulte A Promoção de [respostas que não estejam classificadas.](./filter-answers.md#promoting-answers-that-are-not-ranked)

Para obter informações sobre a limitação do número de respostas classificadas na resposta, consulte [limitar o número de respostas na resposta](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Para um exemplo C# que usa o ranking para exibir resultados, consulte [o tutorial do ranking C#](./csharp-ranking-tutorial.md).
