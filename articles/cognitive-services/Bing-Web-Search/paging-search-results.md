---
title: Como página através dos resultados da pesquisa - Bing Search APIs
titleSuffix: Azure Cognitive Services
description: Saiba como página através dos resultados de pesquisa das APIs de pesquisa de Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: 670460759a9495de735da35ae9f3d8388e59e0e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350624"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Como página através dos resultados das APIs de Pesquisa de Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Quando envia uma chamada para as APIs de Pesquisa de Bing Web, Personalizadas, Imagem, Notícias ou Pesquisa de Vídeo, bing devolve um subconjunto do número total de resultados que podem ser relevantes para a consulta. Para obter o número total estimado de resultados disponíveis, aceda ao campo do objeto de `totalEstimatedMatches` resposta. 

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

## <a name="paging-through-search-results"></a>Paging através dos resultados da pesquisa

Para páginar nos resultados disponíveis, utilize os `count` parâmetros e `offset` os parâmetros de consulta ao enviar o seu pedido.  

> [!NOTE]
>
> * A paging com as APIs de Bing Video, Image e News aplica-se apenas a pesquisas de vídeo geral `/video/search` (, notícias `/news/search` ( ) e imagem `/image/search` . Não é suportado o paging através de tópicos e categorias de tendência.  
> * O `TotalEstimatedMatches` campo é uma estimativa do número total de resultados de pesquisa para a consulta atual. Quando definir os `count` parâmetros e `offset` parâmetros, esta estimativa pode mudar.

| Parâmetro | Descrição                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Especifica o número de resultados a devolver na resposta. Note que o valor predefinido de `count` , e o número máximo de resultados que pode solicitar varia de acordo com a API. Pode encontrar estes valores na documentação de referência nos [próximos passos.](#next-steps) |
| `offset`  | Especifica o número de resultados a saltar. O `offset` é baseado em zero e deve ser inferior a ( `totalEstimatedMatches`  -  `count` ).                                           |

Como exemplo, se quiser apresentar 15 resultados por página, definiria `count` para 15 e `offset` 0 para obter a primeira página de resultados. Para cada chamada de API subsequente, aumentaria `offset` em 15. O exemplo seguinte solicita 15 páginas web a partir do offset 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Se utilizar o valor predefinido, `count` só precisa de especificar o parâmetro de `offset` consulta nas suas chamadas API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Ao utilizar as APIs de Imagem de Bing e vídeo, pode utilizar o `nextOffset` valor para evitar duplicar os resultados de pesquisa. Obtenha o valor dos `Images` objetos ou `Videos` respostas e use-o nos seus pedidos com o `offset` parâmetro.  

> [!NOTE]
> A API de Pesquisa Web Bing retorna os resultados de pesquisa que podem incluir páginas web, imagens, vídeos e notícias. Quando pesquisa através dos resultados de pesquisa da API de Pesquisa web de Bing, está a chamar [apenas WebPages](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage), e não outros tipos de resposta, como imagens ou notícias. Os resultados da pesquisa em `WebPage` objetos podem incluir resultados que aparecem também em outros tipos de resposta.
>
> Se utilizar o `responseFilter` parâmetro de consulta sem especificar quaisquer valores de filtro, não utilize os `count` parâmetros e `offset` parâmetros. 

## <a name="next-steps"></a>Passos seguintes

* [O que são as APIs de Pesquisa web Bing?](bing-api-comparison.md)
* [Referência da API de Pesquisa na Web do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing Custom Search API v7 referência](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing News Search Referência V7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing Video Search API v7 referência](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Bing Imagem Pesquisa referência API v7 referência](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)