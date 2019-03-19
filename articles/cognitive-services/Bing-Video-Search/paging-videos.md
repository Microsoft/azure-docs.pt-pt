---
title: Como paginar através de vídeos disponíveis - pesquisa de vídeos do Bing
titlesuffix: Azure Cognitive Services
description: Aprenda a página por meio de todos os vídeos devolvidos pela API de pesquisa de vídeos do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 0af36fa68b2d801eed52e6f081b040fb56929c91
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101317"
---
# <a name="paging-through-video-search-results"></a>Paginar através de resultados de pesquisa de vídeos

A API de pesquisa de vídeos do Bing devolve um subconjunto de todos os resultados de pesquisa encontrados na sua consulta. Ao paginação através destes resultados com as chamadas subseqüentes para a API, pode obter e exibi-las em seu aplicativo.

> [!NOTE]
> Paginação aplica-se apenas a pesquisa de vídeos (/ vídeos/search) e não para informações de vídeo (/ vídeos/detalhes) ou vídeos populares (/ vídeos/tendências).

## <a name="total-estimated-matches"></a>Total de correspondências estimadas

Para obter o número de resultados da pesquisa encontrada, utilize o [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) campo na resposta JSON.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Paginar através de vídeos

Para a página por meio dos vídeos disponíveis, utilize o [contagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) e [deslocamento](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) parâmetros de consulta ao enviar o pedido.  
  

|Parâmetro  |Descrição  |
|---------|---------|
|`count`     | Especifica o número de resultados a devolver na resposta. O número máximo de resultados que pode solicitar na resposta é 100. A predefinição é 10. O número real entregue pode ser menor do que o pedido.        |
|`offset`     | Especifica o número de resultados a ignorar. O `offset` é baseado em zero e deve ser inferior a (`totalEstimatedMatches` - `count`).          |

Por exemplo, se deseja exibir 20 artigos por página, definiria `count` para 20 e `offset` como 0 para obter a primeira página de resultados. Para cada página subsequente, teria de incrementar `offset` por 20 (por exemplo, 20, 40).  
  
O exemplo seguinte solicita 20 vídeos, começando no desvio 40.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Se utilizar o valor predefinido para o [contagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count), apenas tem de especificar o `offset` parâmetro, como no exemplo seguinte de consulta.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Se a página por meio de 35 vídeos ao mesmo tempo, definiria a `offset` parâmetro como 0 em sua primeira solicitação de consulta e, em seguida, incrementar `offset` por 35 em cada pedido subsequente. No entanto, alguns resultados na seguinte resposta podem conter duplicados resultados de vídeo da resposta anterior. Por exemplo, os dois primeiros vídeos numa resposta podem ser o mesmo que os dois últimos vídeos da resposta anterior.

Para eliminar duplicados resultados, utilize o [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) campo o `Videos` objeto.

Por exemplo, se quiser página 30 vídeos ao mesmo tempo, pode definir `count` de 30 e `offset` para 0 na sua primeira solicitação. No seu pedido seguinte, definiria a `offset` consultar o parâmetro para o `nextOffset` valor.

> [!NOTE]
> O `TotalEstimatedAnswers` campo é uma estimativa do número total de resultados de pesquisa, pode recuperar para a consulta atual.  Se definir `count` e `offset` parâmetros, o `TotalEstimatedAnswers` número pode ser alterada. 
  
## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Obtenha informações de vídeo](video-insights.md)
