---
title: Como filtrar os resultados da pesquisa-API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Você pode filtrar os tipos de respostas que o Bing inclui na resposta (por exemplo, imagens, vídeos e notícias) usando o parâmetro de consulta ' responseFilter '.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111426"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrando as respostas que a resposta de pesquisa inclui  

Quando você consulta a Web, o Bing retorna todo o conteúdo relevante encontrado para a pesquisa. Por exemplo, se a consulta de pesquisa for "velejando + Dinghies", a resposta poderá conter as seguintes respostas:

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

Para filtrar as respostas retornadas pelo Bing, use os parâmetros de consulta abaixo ao chamar a API.  

### <a name="responsefilter"></a>ResponseFilter

Você pode filtrar os tipos de respostas que o Bing inclui na resposta (por exemplo, imagens, vídeos e notícias) usando o parâmetro de consulta [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) , que é uma lista delimitada por vírgulas de respostas. Uma resposta será incluída na resposta se o Bing encontrar o conteúdo relevante para ele. 

Para excluir respostas específicas da resposta, como imagens, preceda um caractere `-` ao tipo de resposta. Por exemplo:

```
&responseFilter=-images,-videos
```

O seguinte mostra como usar `responseFilter` para solicitar imagens, vídeos e notícias de velejando Dinghies. Quando você codifica a cadeia de caracteres de consulta, as vírgulas são alteradas para% 2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Segue-se a resposta à consulta anterior. Como o Bing não encontrou resultados relevantes de vídeos e notícias, a resposta não os inclui.

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

Embora o Bing não retorne os resultados de vídeo e notícias na resposta anterior, isso não significa que o conteúdo de vídeo e de notícias não exista. Isso simplesmente significa que a página não as incluiu. No entanto, se você [paginar](./paging-webpages.md) por mais resultados, as páginas subsequentes provavelmente as incluiriam. Além disso, se você chamar a [API de pesquisa de vídeo](../bing-video-search/search-the-web.md) e pesquisa de notícias pontos de extremidade de [API](../bing-news-search/search-the-web.md) diretamente, a resposta provavelmente conteria resultados.

Você não é incentivado de usar `responseFilter` para obter resultados de uma única API. Se você quiser conteúdo de uma única API do Bing, chame essa API diretamente. Por exemplo, para receber apenas imagens, envie uma solicitação para o ponto de extremidade da API Pesquisa de Imagem, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` ou um dos outros pontos de extremidades de [imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) . Chamar a API única é importante não apenas por motivos de desempenho, mas porque as APIs específicas do conteúdo oferecem resultados mais avançados. Por exemplo, você pode usar filtros que não estão disponíveis para a API de Pesquisa na Web para filtrar os resultados.  

### <a name="site"></a>locais

Para obter os resultados da pesquisa de um domínio específico, inclua o parâmetro de consulta `site:` na cadeia de caracteres de consulta.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Dependendo da consulta, se você usar o operador de consulta `site:`, haverá a chance de que a resposta possa conter conteúdo adulto, independentemente da configuração do [filtro adulto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) . Deve utilizar `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdo para adultos.

### <a name="freshness"></a>Atualização

Para limitar os resultados da resposta da Web a páginas da Webque o Bing descobriu durante um período específico, defina o parâmetro de consulta de [atualização](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) para um dos seguintes valores que não diferenciam maiúsculas de minúsculas:

* `Day` — retornar páginas da Web que o Bing descobriu nas últimas 24 horas
* `Week` — retornar páginas da Web que o Bing descobriu nos últimos 7 dias
* `Month` — retornar páginas da Web que foram descobertas nos últimos 30 dias

Você também pode definir esse parâmetro para um intervalo de datas personalizado no formulário `YYYY-MM-DD..YYYY-MM-DD`. 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Para limitar os resultados a uma única data, defina o parâmetro de atualização para uma data específica:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Os resultados podem incluir páginas da Web que se enquadram fora do período especificado se o número de páginas da Web que o Bing corresponde aos seus critérios de filtro for menor que o número de páginas da Web solicitadas (ou o número padrão que o Bing retorna).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Limitando o número de respostas na resposta

O Bing pode retornar vários tipos de resposta na resposta JSON. Por exemplo, se você consultar *velejando + Dinghies*, o Bing poderá retornar `webpages`, `images`, `videos`e `relatedSearches`.

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

Para limitar o número de respostas que o Bing retorna às duas principais respostas (páginas da Web e imagens), defina o parâmetro de consulta [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) como 2.

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

Se você adicionar o parâmetro de consulta `responseFilter` à consulta anterior e defini-lo como páginas da Web e notícias, a resposta conterá apenas páginas da Web, pois as notícias não são classificadas.

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

## <a name="promoting-answers-that-are-not-ranked"></a>Promovendo respostas que não estão classificadas

Se as respostas mais classificadas que o Bing retornar para uma consulta forem páginas da Web, imagens, vídeos e relatedSearches, a resposta incluirá essas respostas. Se você definir [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) como dois (2), o Bing retornará as duas principais respostas classificadas: páginas da Web e imagens. Se você quiser que o Bing inclua imagens e vídeos na resposta, especifique o parâmetro de consulta [Promote](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) e defina-o como imagens e vídeos.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A seguir está a resposta para a solicitação acima. O Bing retorna as duas principais respostas, páginas da Web e imagens e promove vídeos na resposta.

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

Se você definir `promote` como notícias, a resposta não incluirá a resposta de notícias porque ela não é uma resposta classificada&mdash;você só poderá promover respostas classificadas.

As respostas que você deseja promover não contam com relação ao limite de `answerCount`. Por exemplo, se as respostas classificadas forem notícias, imagens e vídeos, e você definir `answerCount` como 1 e `promote` para notícias, a resposta conterá notícias e imagens. Ou, se as respostas classificadas forem vídeos, imagens e notícias, a resposta conterá vídeos e notícias.

Você poderá usar `promote` apenas se especificar o parâmetro de consulta `answerCount`.
