---
title: Como paginar através de resultados de pesquisa - API de pesquisa Web Bing
titleSuffix: Azure Cognitive Services
description: Saiba como paginar através de resultados de pesquisa da API de pesquisa Web do Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: erhopf
ms.openlocfilehash: 4aa3509fcdfc6fd1bccb4db95a8c916a42ce23d2
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125277"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>Como paginar através de resultados da API de pesquisa Web do Bing

Quando chama a API de pesquisa Web, o Bing devolve uma lista de resultados. A lista é um subconjunto do número total de resultados que podem ser relevantes para a consulta. Para obter o número total de resultados disponíveis, acessar o objeto de resposta [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#totalestimatedmatches) campo.  

A exemplo a seguir mostra o `totalEstimatedMatches` campo que inclui uma resposta de Web.  

```
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

Para a página por meio de páginas da Web disponíveis, utilize o [contagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) e [deslocamento](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset) parâmetros de consulta.  

O `count` parâmetro especifica o número de resultados a devolver na resposta. O número máximo de resultados que pode solicitar na resposta é 50. A predefinição é 10. O número real entregue pode ser menor do que o pedido.

O `offset` parâmetro especifica o número de resultados a ignorar. O `offset` é baseado em zero e deve ser inferior a (`totalEstimatedMatches` - `count`).  

Se deseja exibir 15 páginas da Web por página, definiria `count` 15 e `offset` como 0 para obter a primeira página de resultados. Para cada página subsequente, teria de incrementar `offset` por 15 (por exemplo, 15, 30).  

O exemplo seguinte solicita começando no desvio 45 15 páginas da Web.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Se a predefinição `count` valor funciona para a sua implementação, só precisa de especificar o `offset` parâmetro de consulta.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

A API de pesquisa Web devolve resultados que incluem páginas da Web e podem incluir imagens, vídeos e notícias. Quando a página resultados da pesquisa, são de paginação da [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) resposta e não as outras respostas, como imagens ou de notícias. Por exemplo, se definir `count` a 50, recebe 50 resultados de página Web, mas a resposta pode incluir resultados para as outras respostas também. Por exemplo, a resposta pode incluir a 15 imagens e 4 artigos de notícias. Também é possível que os resultados podem incluir notícias na primeira página, mas não na segunda página, ou vice versa.   

Se especificar a `responseFilter` parâmetro de consulta e não incluem páginas da Web na lista de filtro, não utilize o `count` e `offset` parâmetros.  
