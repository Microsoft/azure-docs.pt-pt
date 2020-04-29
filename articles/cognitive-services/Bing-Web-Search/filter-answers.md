---
title: Como filtrar os resultados da pesquisa - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Pode filtrar os tipos de respostas que bing inclui na resposta (por exemplo imagens, vídeos e notícias) utilizando o parâmetro de consulta 'responseFilter'.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220272"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrar as respostas que a resposta de pesquisa inclui  

Quando consulta a web, bing devolve todos os conteúdos relevantes que encontra para a pesquisa. Por exemplo, se a consulta de pesquisa for "vela+dinghies", a resposta pode conter as seguintes respostas:

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

Para filtrar as respostas devolvidas por Bing, use os parâmetros de consulta abaixo ao ligar para a API.  

### <a name="responsefilter"></a>Filtro de Resposta

Pode filtrar os tipos de respostas que bing inclui na resposta (por exemplo imagens, vídeos e notícias) utilizando [o](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) parâmetro de consulta filter resposta, que é uma lista de respostas delimitada em vírvia. Uma resposta será incluída na resposta se Bing encontrar conteúdo relevante para o mesmo. 

Para excluir respostas específicas da resposta, `-` como imagens, prepare um personagem para o tipo de resposta. Por exemplo:

```
&responseFilter=-images,-videos
```

O seguinte mostra `responseFilter` como usar para solicitar imagens, vídeos e notícias de botes de vela. Quando codifica a corda de consulta, as vírgulas mudam para %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Segue-se a resposta à consulta anterior. Como bing não encontrou resultados relevantes de vídeo e notícias, a resposta não os inclui.

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

Apesar de Bing não ter retornado os resultados de vídeo e notícias na resposta anterior, isso não significa que os conteúdos de vídeo e notícias não existam. Significa simplesmente que a página não os incluiu. No entanto, se [você página](./paging-webpages.md) através de mais resultados, as páginas subsequentes provavelmente incluem-nas. Além disso, se ligar diretamente para os pontos finais da [API](../bing-video-search/search-the-web.md) de Pesquisa de Vídeo e de Pesquisa de [Notícias,](../bing-news-search/search-the-web.md) a resposta provavelmente conterá resultados.

Está desencorajado de `responseFilter` usar para obter resultados de uma única API. Se quiser conteúdo de uma única API bing, ligue diretamente para a API. Por exemplo, para receber apenas imagens, envie um `https://api.cognitive.microsoft.com/bing/v7.0/images/search` pedido para o ponto final da Image Search API, ou um dos outros pontos finais das [Imagens.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) Chamar a API única é importante não só por razões de desempenho, mas porque as APIs específicas do conteúdo oferecem resultados mais ricos. Por exemplo, pode utilizar filtros que não estejam disponíveis para a API de pesquisa web para filtrar os resultados.  

### <a name="site"></a>Site

Para obter resultados de pesquisa `site:` de um domínio específico, inclua o parâmetro de consulta na corda de consulta.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Dependendo da consulta, se utilizar `site:` o operador de consulta, existe a possibilidade de a resposta conter conteúdo adulto, independentemente da definição [de segurançaSearch.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) Deve utilizar `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdo para adultos.

### <a name="freshness"></a>Atualização

Para limitar os resultados da resposta web às páginas web que bing descobriu durante um período específico, defina o parâmetro de consulta de [frescura](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) para um dos seguintes valores insensíveis em casos:

* `Day`— Devolver páginas web que Bing descobriu nas últimas 24 horas
* `Week`— Devolver páginas web que Bing descobriu nos últimos 7 dias
* `Month`— Devolver páginas web que foram descobertas nos últimos 30 dias

Também pode definir este parâmetro para uma gama `YYYY-MM-DD..YYYY-MM-DD`de datas personalizada sintetizando no formulário, . 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Para limitar os resultados a uma única data, defina o parâmetro de frescura para uma data específica:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Os resultados podem incluir páginas web que se enquadram fora do período especificado se o número de páginas web que bing corresponde aos critérios do filtro for inferior ao número de páginas web que solicitou (ou o número predefinido que bing devolve).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Limitando o número de respostas na resposta

Bing pode devolver vários tipos de resposta na resposta JSON. Por exemplo, se você consultar *vela+dinghies* `images`, `videos`Bing `relatedSearches`pode voltar `webpages`, , e .

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

Para limitar o número de respostas que Bing retorna às duas principais respostas (páginas web e imagens), defina o parâmetro de consulta [do Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) para 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A resposta `webPages` inclui `images`apenas e .

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

Se adicionar `responseFilter` o parâmetro de consulta à consulta anterior e defini-lo em páginas web e notícias, a resposta contém apenas páginas web porque as notícias não estão classificadas.

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

Se as respostas mais bem classificadas que bing retorna para uma consulta são páginas web, imagens, vídeos e relacionadosPesquisas, a resposta incluiria essas respostas. Se definir [as respostasCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) a dois (2), Bing devolve as duas respostas mais bem classificadas: páginas web e imagens. Se quiser que o Bing inclua imagens e vídeos na resposta, especifique o parâmetro de consulta [de promoção](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) e defina-o para imagens e vídeos.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Segue-se a resposta ao pedido acima referido. Bing devolve as duas principais respostas, páginas web e imagens, e promove vídeos na resposta.

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

Se você `promote` começar a noticias, a resposta não inclui a resposta&mdash;noticiosa porque não é uma resposta classificada você pode promover apenas respostas classificadas.

As respostas que quer promover não `answerCount` contam contra o limite. Por exemplo, se as respostas classificadas forem notícias, imagens e vídeos, e se definir `answerCount` para 1 e `promote` para notícias, a resposta contém notícias e imagens. Ou, se as respostas classificadas forem vídeos, imagens e notícias, a resposta contém vídeos e notícias.

Só poderá `promote` utilizar se `answerCount` especificar o parâmetro de consulta.
