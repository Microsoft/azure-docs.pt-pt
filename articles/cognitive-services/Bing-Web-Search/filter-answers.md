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
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: a89d73b63680415aa8e516926b8e1d6c59ffbbad
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626008"
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

## <a name="query-parameters"></a>Parâmetros de consulta

Para filtrar as respostas devolvidas pela Bing, utilize o abaixo parâmetros de consulta ao chamar a API.  

### <a name="responsefilter"></a>ResponseFilter

Pode filtrar os tipos de respostas Bing inclui na resposta (por exemplo imagens, vídeos e notícias), utilizando o [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) parâmetro de consulta, o que é uma lista delimitada por vírgulas de respostas. Uma resposta será incluída na resposta se Bing localiza conteúdo relevante para o mesmo. 

Para excluir respostas específicas da resposta, como imagens, preceder um `-` caráter para o tipo de resposta. Por exemplo:

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

### <a name="site"></a>Site

Para obter os resultados da pesquisa de um domínio específico, inclua o `site:` parâmetro na cadeia de consulta de consulta.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Dependendo da consulta, se utilizar o `site:` operador de consulta, existe a possibilidade de que a resposta pode conter o conteúdo para adultos, independentemente do [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) definição. Deve utilizar `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdo para adultos.

### <a name="freshness"></a>Atualização

Para limitar os resultados de resposta da web para páginas Web que Bing descoberto durante um período específico, defina o [atualização](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) parâmetro para um dos seguintes valores de maiúsculas e minúsculas de consulta:

* `Day` — Páginas da Web retorno que Bing detetado nas últimas 24 horas
* `Week` — Páginas da Web retorno que Bing detetado nos últimos 7 dias
* `Month` — Páginas da Web retorno detetados dos últimos 30 dias

Também pode definir este parâmetro para um intervalo de datas personalizadas no formulário, `YYYY-MM-DD..YYYY-MM-DD`. 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Para limitar os resultados para um único data, defina o parâmetro de atualização como uma data específica:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Os resultados podem incluir páginas da Web que estão fora do período especificado, se o número de páginas da Web que corresponda ao Bing para os critérios do filtro é inferior ao número de páginas da Web que pediu (ou o número predefinido que o Bing devolve).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Limitar o número de respostas na resposta

Bing pode devolver vários tipos de resposta a resposta JSON. Por exemplo, se consultar *sailing + dinghies*, o Bing pode devolver `webpages`, `images`, `videos`, e `relatedSearches`.

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
