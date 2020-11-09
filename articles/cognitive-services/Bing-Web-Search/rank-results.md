---
title: Como utilizar rankings para exibir resultados de pesquisa - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Saiba como usar o ranking para exibir os resultados da pesquisa a partir da API de Pesquisa web de Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 20501d0993cc4566a79d6e916d801911606bea35
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380454"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Como utilizar o ranking para exibir os resultados da API de pesquisa web Bing  

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Cada resposta de pesquisa inclui uma resposta [RankingResponse,](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) que especifica como deve apresentar os resultados da pesquisa. Os grupos de resposta do ranking resultam pelo conteúdo da barra principal e do conteúdo da barra lateral para uma página tradicional de resultados de pesquisa. Se não apresentar os resultados num formato tradicional de mainline e barra lateral, deve fornecer ao conteúdo principal uma maior visibilidade do que o conteúdo da barra lateral.  

Dentro de cada grupo (linha principal ou barra lateral), a matriz [de Itens](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) identifica a ordem em que o conteúdo deve aparecer. Cada item fornece as seguintes duas formas de identificar o resultado dentro de uma resposta.  

-   `answerType` e `resultIndex` — O campo identifica a resposta `answerType` (por exemplo, Página Web ou `resultIndex` Notícias) e identifica um resultado dentro da resposta (por exemplo, um artigo de notícias). O índice é baseado em zero.  

-   `value` — O `value` campo contém um ID que corresponde ao ID de uma resposta ou de um resultado dentro da resposta. Ou a resposta ou os resultados contêm o ID, mas não ambos.  

A utilização do ID é mais simples de usar porque só precisa de combinar o ID do ranking com o ID de uma resposta ou um dos seus resultados. Se um objeto de resposta incluir um `id` campo, apresente todos os resultados da resposta em conjunto. Por exemplo, se o `News` objeto incluir o `id` campo, apresente todos os artigos de notícias em conjunto. Se o `News` objeto não incluir o `id` campo, cada artigo de notícias contém um `id` campo e a resposta do ranking mistura os artigos de notícias com os resultados de outras respostas.  

Usar o `answerType` e é um pouco mais `resultIndex` complicado. `answerType`Utiliza-se para identificar a resposta que contém os resultados a exibir. Em seguida, `resultIndex` usa-se para indexar os resultados da resposta para obter o resultado a exibir. (O `answerType` valor é o nome do campo no objeto [SearchResponse.)](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) Se é suposto mostrar todos os resultados da resposta em conjunto, o item de resposta do ranking não inclui o `resultIndex` campo.  

## <a name="ranking-response-example"></a>Exemplo de resposta de ranking

O seguinte mostra um exemplo [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Como a resposta Web não inclui um `id` campo, exibiria todas as páginas web individualmente com base no ranking (cada página web inclui um `id` campo). E como as imagens, vídeos e pesquisas relacionadas incluem o `id` campo, você exibia os resultados de cada uma dessas respostas em conjunto com base no ranking.

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

Com base nesta resposta de ranking, a linha principal mostraria os seguintes resultados de pesquisa:  

-   O resultado da primeira página web
-   Todas as imagens  
-   Os resultados da segunda e terceira página  
-   Todos os vídeos  
-   Os resultados da página web da 4ª, 5ª e 6ª  

E a barra lateral mostraria os seguintes resultados de pesquisa:  

-   Todas as pesquisas relacionadas  


## <a name="next-steps"></a>Próximos passos

Para obter informações sobre a promoção de resultados desclassificados, consulte [Promover respostas que não estejam classificadas.](./filter-answers.md#promoting-answers-that-are-not-ranked)

Para obter informações sobre a limitação do número de respostas classificadas na resposta, consulte [limitar o número de respostas na resposta](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Para um exemplo C# que utilize o ranking para exibir resultados, consulte [o tutorial do ranking C#](./csharp-ranking-tutorial.md).