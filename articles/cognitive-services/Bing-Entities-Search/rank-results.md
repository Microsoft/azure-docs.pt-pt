---
title: Usando a classificação de mensagens em fila para exibir respostas - pesquisa de entidades do Bing
titlesuffix: Azure Cognitive Services
description: Mostra como utilizar a classificação de mensagens em fila para apresentar as respostas que retorna a API de pesquisa de entidades do Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: aahi
ms.openlocfilehash: f0cae32acf2db62a5d3c060ea944b1131252beda
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195924"
---
# <a name="using-ranking-to-display-results"></a>Usando a classificação de mensagens em fila para exibir os resultados  

Cada resposta de pesquisa de entidades inclui um [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) responder, semelhante à de uma resposta de pesquisa Web Bing, que especifica como tem de apresentar os resultados da pesquisa. A resposta de classificação agrupa os resultados em Polo, principal e o conteúdo da barra lateral. O resultado de Polo é o resultado mais importante ou proeminente e deverá ser apresentado pela primeira vez. Se não exibir os resultados restantes tradicional no principal e o formato de barra lateral, tem de fornecer a visibilidade mais elevada do que o conteúdo da barra lateral do conteúdo principal. 
  
Em cada grupo, o [itens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) matriz identifica a ordem em que o conteúdo tem de aparecer no. Cada item fornece duas formas de identificar o resultado numa resposta.  
  
-   `answerType` e `resultIndex` — o `answerType` campo identifica a resposta (entidade ou local) e `resultIndex` identifica um resultado na resposta (por exemplo, uma entidade). O índice é baseado em zero.  
  
-   `value` — O `value` campo contém um ID que corresponde ao ID de uma resposta ou um resultado na resposta. A resposta ou os resultados contêm o ID, mas não ambos.  
  
Com o ID requer que coincide com o ID de classificação com o ID de uma resposta ou um dos seus resultados. Se um objeto de resposta inclui uma `id` campo, exibir os resultados de todos os a resposta em conjunto. Por exemplo, se o `Entities` objeto inclui o `id` campo, apresentar todos os artigos de entidades em conjunto. Se o `Entities` objeto não inclui o `id` campo, em seguida, cada entidade contém um `id` campo e a resposta de classificação mistura as entidades com os resultados de locais.  
  
Utilizar o `answerType` e `resultIndex` é um processo de dois passos. Primeiro, use `answerType` para identificar a resposta que contém os resultados para apresentar. Em seguida, utilizar `resultIndex` ao índice resultados dessa resposta para obter o resultado para apresentar. (A `answerType` valor é o nome do campo no [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objeto.) Se deverá para exibir resultados de todos os a resposta em conjunto, a resposta de classificação item não inclui o `resultIndex` campo.

## <a name="ranking-response-example"></a>Exemplo de resposta de classificação

O seguinte mostra um exemplo [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Com base na resposta classificação, a barra lateral seria exibir os resultados de dois entidade relacionados com Jimi Hendrix.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial de pesquisa de entidades do Bing](tutorial-bing-entities-search-single-page-app.md)
