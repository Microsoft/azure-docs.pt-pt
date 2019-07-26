---
title: Como paginar os vídeos disponíveis-Pesquisa de Vídeo do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como paginar todos os vídeos retornados pelo API de Pesquisa de Vídeo do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: c3570d8772734595c6707ca8103006867a8eb47a
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500713"
---
# <a name="paging-through-video-search-results"></a>Paginação por meio de resultados da pesquisa de vídeo

O API de Pesquisa de Vídeo do Bing retorna um subconjunto de todos os resultados de pesquisa encontrados para sua consulta. Ao paginar por meio desses resultados com chamadas subsequentes para a API, você pode obtê-los e exibi-los em seu aplicativo.

> [!NOTE]
> A paginação aplica-se somente à pesquisa de vídeos (/Videos/Search) e não a/videos/Details (Video insights) ou a/videos/Trending (vídeos de tendências).

## <a name="total-estimated-matches"></a>Total de correspondências estimadas

Para obter o número estimado de resultados de pesquisa encontrados, use o campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-totalestimatedmatches) na resposta JSON.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Paginando por meio de vídeos

Para paginar os vídeos disponíveis, use os parâmetros de consulta [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) e [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) ao enviar sua solicitação.  
  

|Parâmetro  |Descrição  |
|---------|---------|
|`count`     | Especifica o número de resultados a serem retornados na resposta. O número máximo de resultados que você pode solicitar na resposta é 100. O padrão é 10. O número real entregue pode ser menor que o solicitado.        |
|`offset`     | Especifica o número de resultados a serem ignorados. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` - `count`).          |

Por exemplo, se você quiser exibir 20 artigos por página, defina `count` como 20 e `offset` como 0 para obter a primeira página de resultados. Para cada página subsequente, você incrementaria `offset` por 20 (por exemplo, 20, 40).  
  
O exemplo a seguir solicita 20 vídeos, começando no deslocamento 40.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Se você usar o valor padrão para a [contagem](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count), só precisará especificar o parâmetro `offset` de consulta, como no exemplo a seguir.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Se você paginar os vídeos de 35 por vez, definiria `offset` o parâmetro de consulta como 0 em sua primeira solicitação e, `offset` em seguida, incrementado em 35 em cada solicitação subsequente. No entanto, alguns resultados na próxima resposta podem conter resultados de vídeo duplicados da resposta anterior. Por exemplo, os dois primeiros vídeos em uma resposta podem ser iguais aos dois últimos vídeos da resposta anterior.

Para eliminar resultados duplicados, use o campo [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-nextoffset) do `Videos` objeto.

Por exemplo, se você quiser paginar 30 vídeos por vez, poderá definir `count` como 30 e `offset` 0 em sua primeira solicitação. Em sua próxima solicitação, você definiria o `offset` parâmetro de consulta como `nextOffset` o valor.

> [!NOTE]
> O `TotalEstimatedAnswers` campo é uma estimativa do número total de resultados da pesquisa que você pode recuperar para a consulta atual.  Quando você define `count` e `offset` parâmetros, o `TotalEstimatedAnswers` número pode ser alterado. 
  
## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Obter informações de vídeo](video-insights.md)
