---
title: Como paginar através de resultados de pesquisa do Bing notícias
titlesuffix: Azure Cognitive Services
description: Saiba como paginar os artigos de notícias, que retorna a API de pesquisa de notícias do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 1eab92dcc9c1890e82f9999e26e54378a3687c6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66390487"
---
# <a name="how-to-page-through-news-search-results"></a>Como paginar através de resultados de pesquisa de notícias

Quando chama a API de pesquisa de notícias, o Bing devolve uma lista de resultados que são relevantes para a sua consulta. Para obter o número total de resultados disponíveis, acessar o objeto de resposta [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-totalmatches) campo.  
  
A exemplo a seguir mostra o `totalEstimatedMatches` campo que inclui uma resposta de notícias.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Para a página por meio dos artigos disponíveis, utilize o [contagem](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#count) e [deslocamento](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#offset) parâmetros de consulta.  
 

|Parâmetro  |Descrição  |
|---------|---------|
|`count`     | Especifica o número de resultados a devolver na resposta. O número máximo de resultados que pode solicitar na resposta é 100. A predefinição é 10. O número real entregue pode ser menor do que o pedido.        |
|`offset`     | Especifica o número de resultados a ignorar. O `offset` é baseado em zero e deve ser inferior a (`totalEstimatedMatches` - `count`).          |

Por exemplo, se deseja exibir 20 artigos por página, definiria `count` para 20 e `offset` como 0 para obter a primeira página de resultados. Para cada página subsequente, teria de incrementar `offset` por 20 (por exemplo, 20, 40).  
  
O exemplo seguinte solicita 20 artigos de notícias, começando no desvio 40.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Se a predefinição `count` valor funciona para a sua implementação, especifique apenas o `offset` parâmetro de consulta, conforme mostrado no exemplo a seguir:  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Paginação aplica-se apenas a pesquisa de notícias (/ notícias/search) e não para tópicos em destaque (/ notícias/trendingtopics) ou categorias de notícias (/ notícias).

> [!NOTE]
> O `TotalEstimatedAnswers` campo é uma estimativa do número total de resultados de pesquisa, pode recuperar para a consulta atual.  Se definir `count` e `offset` parâmetros, o `TotalEstimatedAnswers` número pode ser alterada. 
