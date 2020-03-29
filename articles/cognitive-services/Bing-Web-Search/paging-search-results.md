---
title: Como página através de resultados de pesquisa - Bing Search APIs
titleSuffix: Azure Cognitive Services
description: Saiba como página rume através dos resultados da pesquisa a partir das APIs de Pesquisa de Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481728"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Como página através dos resultados das APIs de Pesquisa de Bing

Ao enviar uma chamada para as APIs de Pesquisa de Vídeo, Bing, bing devolve um subconjunto do número total de resultados que podem ser relevantes para a consulta. Para obter o número total estimado de resultados `totalEstimatedMatches` disponíveis, aceda ao campo do objeto de resposta. 

Por exemplo: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Paging através de resultados de pesquisa

Para analisar os resultados `count` disponíveis, utilize os parâmetros e `offset` consulta ao enviar o seu pedido.  

> [!NOTE]
>
> * A paging com as APIs de Vídeo, Imagem e`/video/search`Notícias`/news/search`aplica-se`/image/search`apenas a vídeos gerais ( ), notícias ( ) e pesquisas de imagem . A paging através de tópicos e categorias de tendência não é suportada.  
> * O `TotalEstimatedMatches` campo é uma estimativa do número total de resultados de pesquisa para a consulta atual. Quando definir `count` os `offset` parâmetros e parâmetros, esta estimativa pode mudar.

| Parâmetro | Descrição                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Especifica o número de resultados a devolver na resposta. Note que o `count`valor predefinido de , e o número máximo de resultados que pode solicitar varia por API. Pode encontrar estes valores na documentação de referência em [etapas seguintes](#next-steps). |
| `offset`  | Especifica o número de resultados a saltar. O `offset` é baseado em zero e`totalEstimatedMatches` - `count`deve ser inferior ().                                           |

Como exemplo, se quiser apresentar 15 resultados por `count` página, `offset` definirá para 15 e 0 para obter a primeira página dos resultados. Para cada chamada API subsequente, aumentaria `offset` por 15. O exemplo seguinte solicita 15 páginas web a partir de 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Se utilizar o `count` valor predefinido, apenas precisa especificar o parâmetro de `offset` consulta nas suas chamadas API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Ao utilizar as APIs de Imagem bing `nextOffset` e vídeo, pode utilizar o valor para evitar resultados de pesquisa duplicados. Obtenha o valor `Images` `Videos` dos objetos ou resposta e use-o nos seus pedidos com o `offset` parâmetro.  

> [!NOTE]
> O Bing Web Search API devolve resultados de pesquisa que podem incluir páginas web, imagens, vídeos e novidades. Quando página através dos resultados da pesquisa a partir da API de pesquisa web bing, você está paging apenas [WebPages](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage), e não outros tipos de resposta, como imagens ou notícias. Os resultados da pesquisa em `WebPage` objetos podem incluir resultados que aparecem em outros tipos de resposta também.
>
> Se utilizar `responseFilter` o parâmetro de consulta sem especificar quaisquer `count` valores de filtro, não utilize os parâmetros e os `offset` parâmetros. 

## <a name="next-steps"></a>Passos seguintes

* [O que são as APIs de Pesquisa web bing?](bing-api-comparison.md)
* [Referência da API de Pesquisa na Web do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Referência API v7 de pesquisa personalizada bing custom](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Referência Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Referência bing video search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Referência Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
