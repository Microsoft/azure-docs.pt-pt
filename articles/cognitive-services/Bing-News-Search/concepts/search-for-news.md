---
title: Pesquisa de notícias com a API de pesquisa de notícias do Bing
titlesuffix: Azure Cognitive Services
description: Saiba como enviar consultas de pesquisa de notícias gerais, tópicos em destaque e manchetes.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 01/11/2019
ms.author: scottwhi
ms.openlocfilehash: 612a3961d901f53147ab2f3cfeea20f9c11d96b7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087861"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>Pesquisa de notícias com a API de pesquisa de notícias do Bing

A API de pesquisa de imagens do Bing torna mais fácil de integrar as capacidades de pesquisa do Bing notícias cognitivos aos seus aplicativos.

Embora a API de pesquisa de notícias do Bing principalmente localiza e devolve os artigos de notícias relevantes, fornece vários recursos para obtenção de notícias inteligentes e focada na web.

## <a name="suggest-and-use-search-terms"></a>Sugerir e utilizar os termos de pesquisa

Se disponibilizar uma caixa de pesquisa na qual o utilizador introduz o seu termo de pesquisa, utilize a [API de Sugestão Automática do Bing](../../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API devolve cadeias de consulta sugerida com base em termos de pesquisa parcial à medida que o utilizador escreve.

Depois de o utilizador introduzir o seu termo de pesquisa, o URL codifica-o antes de definir o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query). Por exemplo, se o utilizador introduzir *sailing dinghies*, defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

## <a name="get-general-news"></a>Receba notícias gerais

Para obter artigos gerais relacionados com o termo de pesquisa do utilizador a partir da Web, envie o seguinte pedido GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Se for a primeira vez que está a chamar qualquer uma das APIs do Bing, não inclua o cabeçalho de ID de cliente. Inclua apenas o ID de cliente se tiver chamado anteriormente uma API do Bing e o Bing tiver devolvido um ID de cliente para o utilizador e a combinação de dispositivo.

Para obter notícias de um domínio específico, utilize o operador de consulta [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Segue-se a resposta à consulta anterior. Como parte do [requisitos de utilização e a exibição](../useanddisplayrequirements.md) para a pesquisa do Bing APIs, deverá exibir cada artigo de notícias na ordem disposta na resposta. Se o artigo tem em cluster artigos, deverá indicar que os artigos relacionados existem e exibi-las mediante pedido.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

A resposta [news](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#news) lista os artigos que o Bing considera relevantes para a consulta. O campo `totalEstimatedMatches` contém uma estimativa do número de artigos disponíveis para visualização. Para obter informações sobre a paginação nos artigos, veja [Paginação em Notícias](../paging-news.md).

Cada [artigo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#newsarticle) na lista inclui o nome, a descrição e o URL do artigo no site do anfitrião. Se o artigo contiver uma imagem, o objeto inclui uma miniatura da imagem. Utilize `name` e `url` para criar uma hiperligação que direcione o utilizador para o artigo no site do anfitrião. Se o artigo incluir uma imagem, torne também a imagem clicável com `url`. Certifique-se de que utiliza `provider` para atribuir o artigo.

Se o Bing conseguir determinar a categoria do artigo, este incluirá o campo `category`.

## <a name="get-todays-top-news"></a>Obtenha notícias de principais de hoje

Para obter os principais artigos do dia, faria o mesmo pedido que para obter notícias gerais, mas manteria `q` não definido.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

A resposta para obter as principais notícias é quase a mesma que para obter notícias gerais. No entanto, a resposta `news` não inclui o campo `totalEstimatedMatches` porque existe um número definido de resultados. O número de artigos principais pode variar consoante o ciclo de notícias. Certifique-se de que utiliza `provider` para atribuir o artigo.

## <a name="get-news-by-category"></a>Receba notícias por categoria

Para obter artigos por categoria, como os principais artigos de desporto ou entretenimento, envie o seguinte pedido GET ao Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Utilize o parâmetro de consulta [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) para especificar a categoria de artigos a obter. Para obter uma lista de possíveis categorias de notícias que pode especificar, veja [Categorias de Notícias por Mercado](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-categories-by-market).

A resposta para obter notícias por categoria é quase a mesma que para obter notícias gerais. No entanto, os artigos são todos da categoria especificada.

## <a name="get-headline-news"></a>Obter manchetes

Para pedir títulos de notícias e obter artigos de todas as categorias, envie o seguinte pedido ao Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Não inclua o parâmetro de consulta [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category).

A resposta para obter títulos de notícias é quase a mesma que para obter as principais notícias do dia. Se o artigo for um título, o campo `headline` será definido como **true**.

Por predefinição, a resposta inclui até 12 títulos de artigos. Para alterar o número de títulos de artigos a devolver, especifique o parâmetro de consulta [headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headlinecount). A resposta também inclui até quatro artigos sem título por categoria de notícias.

A resposta contabiliza os clusters como um artigo. Uma vez que um cluster pode ter vários artigos, a resposta pode incluir mais de 12 títulos de artigos e mais de quatro artigos sem título por categoria.

## <a name="get-trending-news"></a>Obtenha notícias populares

Para obter os tópicos populares nas redes sociais, envie o seguinte pedido GET ao Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> Tópicos Populares está disponível apenas nos mercados en-US e zh-CN.

O seguinte JSON é a resposta ao pedido anterior. Cada artigo popular inclui uma imagem relacionada, um sinalizador de notícia de última hora e um URL para os resultados da pesquisa do Bing para o artigo. Utilize o URL no campo `webSearchUrl` para direcionar o utilizador para a página de resultados da pesquisa do Bing. Em alternativa, utilize o texto da consulta para chamar a [API de Pesquisa na Web](../../bing-web-search/search-the-web.md) para mostrar os resultados.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>Obter notícias relacionadas

Se existirem outros artigos relacionados com uma notícia, esta pode incluir o campo [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle-clusteredarticles). Segue-se um artigo com artigos em cluster.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>Limitar pedidos

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Como paginar através de resultados de pesquisa do Bing notícias](../paging-news.md)
