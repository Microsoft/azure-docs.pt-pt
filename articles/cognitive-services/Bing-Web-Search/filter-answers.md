---
title: Como filtrar os resultados da pesquisa - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Pode filtrar os tipos de respostas que o Bing inclui na resposta (por exemplo, imagens, vídeos e notícias) utilizando o parâmetro de consulta 'answerFilter'.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 6fa022f181e2061c6a7f3e08d1f2f501ddd9cac3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84696716"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrar as respostas que a resposta de pesquisa inclui  

Ao consultar a web, Bing devolve todo o conteúdo relevante que encontra para a pesquisa. Por exemplo, se a consulta de pesquisa for "vela+dinghies", a resposta pode conter as seguintes respostas:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>Parâmetros de consulta

Para filtrar as respostas devolvidas por Bing, utilize os parâmetros de consulta abaixo quando ligar para a API.  

### <a name="responsefilter"></a>Filtro de resposta

Pode filtrar os tipos de respostas que bing inclui na resposta (por exemplo, imagens, vídeos e notícias) utilizando o parâmetro de consulta [answerFilter,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) que é uma lista de respostas delimitada em vírgula. Uma resposta será incluída na resposta se Bing encontrar conteúdo relevante para a resposta. 

Para excluir respostas específicas da resposta, como imagens, prepare um `-` personagem para o tipo de resposta. Por exemplo:

```
&responseFilter=-images,-videos
```

O seguinte mostra como usar `responseFilter` para solicitar imagens, vídeos e notícias de barcos à vela. Quando codificar a cadeia de consulta, as vírgulas mudam para %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Segue-se a resposta à consulta anterior. Como o Bing não encontrou os resultados relevantes do vídeo e das notícias, a resposta não os inclui.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Apesar de Bing não ter retornido os resultados de vídeo e notícias na resposta anterior, não significa que o conteúdo de vídeo e notícias não exista. Significa simplesmente que a página não os incluía. No entanto, [se](./paging-webpages.md) analisar mais resultados, as páginas seguintes provavelmente incluí-los-ão. Além disso, se ligar diretamente para os pontos finais da [API](../bing-video-search/search-the-web.md) de Pesquisa de Vídeo e [de Pesquisa de Notícias,](../bing-news-search/search-the-web.md) a resposta provavelmente conterá resultados.

Você está desencorajado de usar `responseFilter` para obter resultados de uma única API. Se quiser conteúdo de uma única API Bing, ligue diretamente para a API. Por exemplo, para receber apenas imagens, envie um pedido para o ponto final da API de Pesquisa de Imagem, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` ou um dos outros pontos finais de [Imagens.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) Chamar a API única é importante não só por razões de desempenho, mas porque as APIs específicas para o conteúdo oferecem resultados mais ricos. Por exemplo, pode utilizar filtros que não estão disponíveis na API de Pesquisa Web para filtrar os resultados.  

### <a name="site"></a>Site

Para obter resultados de pesquisa de um domínio específico, inclua o `site:` parâmetro de consulta na cadeia de consulta.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Dependendo da consulta, se utilizar o `site:` operador de consulta, existe a possibilidade de a resposta conter conteúdo adulto independentemente da definição [de segurança.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) Deve utilizar `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdo para adultos.

### <a name="freshness"></a>Atualização

Para limitar os resultados da resposta web às páginas web que Bing descobriu durante um período específico, desa fixa o parâmetro de consulta de [frescura](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) para um dos seguintes valores insensíveis a casos:

* `Day` — Devolver páginas que Bing descobriu nas últimas 24 horas
* `Week` — Devolver páginas que Bing descobriu nos últimos 7 dias
* `Month` — Devolver páginas que descobertas nos últimos 30 dias

Também pode definir este parâmetro para um intervalo de datas personalizado no formulário, `YYYY-MM-DD..YYYY-MM-DD` . 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Para limitar os resultados a uma única data, desa fixa o parâmetro de frescura para uma data específica:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Os resultados podem incluir páginas web que ficam fora do período especificado se o número de páginas web que Bing corresponde aos critérios do filtro for inferior ao número de páginas web que solicitou (ou o número padrão que bing devolve).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Limitação do número de respostas na resposta

Bing pode devolver vários tipos de resposta na resposta JSON. Por exemplo, se consultar *vela+dinghies,* Bing pode `webpages` `images` voltar, `videos` e `relatedSearches` .

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Para limitar o número de respostas que Bing retorna às duas melhores respostas (páginas web e imagens), desa um parâmetro de consulta [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) para 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A resposta inclui apenas `webPages` e `images` . .

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Se adicionar o `responseFilter` parâmetro de consulta à consulta anterior e o definir para páginas web e notícias, a resposta contém apenas páginas web porque as notícias não estão classificadas.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Promover respostas que não estão classificadas

Se as respostas mais bem classificadas que Bing retorna para uma consulta são páginas web, imagens, vídeos e relacionadosSearches, a resposta incluiria essas respostas. Se definir [respostasConse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) dois (2), Bing devolve as duas melhores respostas classificadas: páginas web e imagens. Se quiser que bing inclua imagens e vídeos na resposta, especifique o parâmetro de consulta [de promoção](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) e desate-o em imagens e vídeos.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Segue-se a resposta ao pedido acima referido. Bing devolve as duas melhores respostas, páginas web e imagens, e promove vídeos na resposta.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Se definir `promote` as notícias, a resposta não inclui a resposta noticiosa porque não é uma resposta classificada &mdash; que pode promover apenas respostas classificadas.

As respostas que quer promover não contam contra o `answerCount` limite. Por exemplo, se as respostas classificadas forem notícias, imagens e vídeos, e definir `answerCount` para 1 e `promote` para as notícias, a resposta contém notícias e imagens. Ou, se as respostas classificadas forem vídeos, imagens e notícias, a resposta contém vídeos e notícias.

Só pode utilizar `promote` se especificar o `answerCount` parâmetro de consulta.
