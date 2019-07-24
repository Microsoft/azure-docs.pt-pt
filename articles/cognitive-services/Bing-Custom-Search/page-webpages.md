---
title: Página por meio de páginas da Web disponíveis-Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Mostra como paginar todas as páginas da Web que Pesquisa Personalizada do Bing pode retornar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 13b4cef624c636b8935897338badf3349f27c7f5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405034"
---
# <a name="paging-webpages"></a>Paginando páginas da Web 

Quando você chama o API de Pesquisa Personalizada, o Bing retorna uma lista de resultados. A lista é um subconjunto do número total de resultados que podem ser relevantes para a consulta. Para obter o número total estimado de resultados disponíveis, acesse o campo [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#totalestimatedmatches) do objeto de resposta.  
  
O exemplo a seguir mostra `totalEstimatedMatches` o campo que uma resposta da Web inclui.  
  
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
  
Para percorrer as páginas da Web disponíveis, use os parâmetros de consulta [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#count) e [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#offset) .  
  
O `count` parâmetro especifica o número de resultados a serem retornados na resposta. O número máximo de resultados que você pode solicitar na resposta é 50. O padrão é 10. O número real entregue pode ser menor que o solicitado.

O `offset` parâmetro especifica o número de resultados a serem ignorados. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` - `count`).  
  
Se você quiser exibir 15 páginas da Web por página, defina `count` como 15 e `offset` como 0 para obter a primeira página de resultados. Para cada página subsequente, você incrementaria `offset` 15 (por exemplo, 15, 30).  
  
A seguir é mostrado um exemplo que solicita 15 páginas da Web começando no deslocamento 45.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Se o valor `count` padrão funcionar para sua implementação, você só precisará especificar o `offset` parâmetro de consulta.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> O `TotalEstimatedAnswers` campo é uma estimativa do número total de resultados da pesquisa que você pode recuperar para a consulta atual.  Quando você define `count` e `offset` parâmetros, o `TotalEstimatedAnswers` número pode ser alterado. 

