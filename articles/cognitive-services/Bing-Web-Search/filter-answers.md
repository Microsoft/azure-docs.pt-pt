---
title: Como filtrar os resultados da pesquisa - API de pesquisa Web Bing
titleSuffix: Azure Cognitive Services
description: Saiba como filtrar e exibir os resultados da API de pesquisa Web do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 8d8fd03d9c3d912788e9893377bbab3efac86f8a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383838"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>As respostas que inclui a resposta da pesquisa de filtragem  

Quando consultar a web, o Bing devolve todos os conteúdos relevantes para a pesquisa encontra. Por exemplo, se a consulta de pesquisa é "dinghies sailing +", a resposta pode conter as respostas seguintes:

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
Pode filtrar os tipos de conteúdo, receberá (para imagens de exemplo, vídeos e notícias), utilizando o [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) parâmetro de consulta. Se o Bing localiza conteúdo relevante para as respostas especificadas, será devolvido. O filtro de resposta é uma lista delimitada por vírgulas de respostas. 

Para excluir determinados tipos de conteúdo, como imagens, de resposta, pode adicionar um `-` caráter para o início do `responseFilter` valor. Pode separar tipos excluídos com uma vírgula (`,`). Por exemplo:

```
&responseFilter=-images,-videos
```


O seguinte mostra como utilizar `responseFilter` pedido imagens, vídeos e notícias de velejar dinghies. Ao codificar a cadeia de consulta, vírgulas alterar para %2 C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Segue-se a resposta à consulta anterior. Uma vez que não encontrou o Bing vídeos relevantes e os resultados de notícias, a resposta não incluí-los.

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

Embora o Bing não devolveu resultados de vídeo e as notícias na resposta anterior, não significa que não existe conteúdo de vídeo e notícias. Simplesmente significa que a página não incluí-los. No entanto, se [página](./paging-webpages.md) através de resultados de mais, as páginas subsequentes seriam provavelmente incluí-los. Além disso, se chamar o [API de pesquisa de vídeos](../bing-video-search/search-the-web.md) e [API de pesquisa de notícias](../bing-news-search/search-the-web.md) pontos de extremidade diretamente, a resposta provavelmente iria conter os resultados.

Não deve utilizar `responseFilter` para obter resultados de uma única API. Se pretender que o conteúdo a partir de uma única API do Bing, chame essa API diretamente. Por exemplo, para receber apenas imagens, enviar um pedido para o ponto de final de API de pesquisa de imagens `https://api.cognitive.microsoft.com/bing/v7.0/images/search` ou um do outro [imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) pontos de extremidade. Chamar a API única é importante não só por motivos de desempenho, mas porque as APIs de específico de conteúdo oferecem resultados mais sofisticados. Por exemplo, pode utilizar filtros que não estão disponíveis para a API de pesquisa na Web para filtrar os resultados.  

Para obter os resultados da pesquisa de um domínio específico, inclua o `site:` operador de consulta na cadeia de consulta.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Dependendo da consulta, se utilizar o `site:` operador de consulta, existe a possibilidade de que a resposta pode conter o conteúdo para adultos, independentemente do [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) definição. Deve utilizar `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdo para adultos.

## <a name="limiting-the-number-of-answers-in-the-response"></a>Limitar o número de respostas na resposta

Bing inclui respostas na resposta com base na classificação. Por exemplo, se consultar *sailing + dinghies*, o Bing devolve `webpages`, `images`, `videos`, e `relatedSearches`.

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

Para limitar o número de respostas que o Bing devolve para as respostas de duas principais (páginas da Web e imagens), conjunto de [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) parâmetro para 2 de consulta.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A resposta inclui apenas `webPages` e `images`.

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

Se adicionar o `responseFilter` parâmetro para a consulta anterior e o conjunto de páginas da Web e notícias, a resposta contém páginas Web apenas uma vez que não são consideradas o notícias de consulta.

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

## <a name="promoting-answers-that-are-not-ranked"></a>Promover as respostas não estão ordenadas

Se a parte superior com a classificação de respostas Bing devolve para uma consulta em páginas da Web, imagens, vídeos e relatedSearches, a resposta inclui as respostas. Se definir [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) para dois (2), do Bing devolve as respostas de classificações duas principais: páginas da Web e imagens. Se pretender que o Bing para incluir imagens e vídeos na resposta, especifique a [promover](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) parâmetro de consulta e defina-o como imagens e vídeos.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Segue-se a resposta ao pedido acima. Bing devolve as principais duas respostas, imagens e páginas da Web e promove a vídeos para a resposta.

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

Se definir `promote` para notícias, a resposta não inclui a resposta de notícias, porque não é uma resposta classificada&mdash;pode promover somente com a classificação de respostas.

As respostas que pretende promover não contam para o `answerCount` limite. Por exemplo, se as respostas classificadas são notícias, imagens e vídeos e defina `answerCount` para 1 e `promote` para notícias, a resposta contém notícias e imagens. Em alternativa, se as respostas classificadas vídeos, imagens e notícias, a resposta contém vídeos e notícias.

Pode usar `promote` apenas se especificou o `answerCount` parâmetro de consulta.
