---
title: Como percorrer os resultados da pesquisa-APIs de Pesquisa do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como paginar os resultados da pesquisa do APIs de Pesquisa do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: aahi
ms.openlocfilehash: e2149c7db20939a739380dc2df0e21b6a62ed916
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955362"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Como paginar os resultados da APIs de Pesquisa do Bing

Quando você envia uma chamada para as APIs Web, personalizada, imagem, notícias ou Pesquisa de Vídeo do Bing, o Bing retorna um subconjunto do número total de resultados que podem ser relevantes para a consulta. Para obter o número total estimado de resultados disponíveis, acesse o campo `totalEstimatedMatches` do objeto de resposta. 

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

## <a name="paging-through-search-results"></a>Paginação por meio de resultados da pesquisa

Para paginar os resultados disponíveis, use os parâmetros de consulta `count` e `offset` ao enviar sua solicitação.  

> [!NOTE]
>
> * A paginação com as APIs de vídeo, imagem e notícias do Bing aplica-se somente às pesquisas gerais de vídeo (`/video/search`), notícias (`/news/search`) e imagem (`/image/search`). Não há suporte para paginação por meio de tópicos e categorias de tendências.  
> * O campo `TotalEstimatedMatches` é uma estimativa do número total de resultados da pesquisa para a consulta atual. Quando você define os parâmetros `count` e `offset`, essa estimativa pode ser alterada.

| Parâmetro | Descrição                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Especifica o número de resultados a serem retornados na resposta. Observe que o valor padrão de `count` e o número máximo de resultados que você pode solicitar variam de acordo com a API. Você pode encontrar esses valores na documentação de referência em [próximas etapas](#next-steps). |
| `offset`  | Especifica o número de resultados a serem ignorados. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` @ no__t-2 @ no__t-3).                                           |

Por exemplo, se você quiser exibir 15 resultados por página, defina `count` como 15 e `offset` como 0 para obter a primeira página de resultados. Para cada chamada de API subsequente, você incrementaria `offset` por 15. O exemplo a seguir solicita 15 páginas da Web começando no deslocamento 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Se você usar o valor padrão `count`, só precisará especificar o parâmetro de consulta `offset` em suas chamadas à API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> O API de Pesquisa na Web do Bing retorna resultados da pesquisa que podem incluir páginas da Web, imagens, vídeos e notícias. Ao percorrer os resultados da pesquisa da API de Pesquisa na Web do Bing, você está paginando apenas as [páginas da Web](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)e não outros tipos de resposta, como imagens ou notícias. Os resultados da pesquisa em objetos `WebPage` podem incluir resultados que também apareçam em outros tipos de resposta.
>
> Se você usar o parâmetro de consulta `responseFilter` sem especificar nenhum valor de filtro, não use os parâmetros `count` e `offset`. 

## <a name="next-steps"></a>Passos seguintes

* [Quais são as APIs de Pesquisa na Web do Bing?](bing-api-comparison.md)
* [Referência da API de Pesquisa na Web do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-referenc)
* [Referência do API de Pesquisa Personalizada do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Referência do API de Pesquisa de Notícias do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Referência do API de Pesquisa de Vídeo do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Referência do API de Pesquisa de Imagem do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)