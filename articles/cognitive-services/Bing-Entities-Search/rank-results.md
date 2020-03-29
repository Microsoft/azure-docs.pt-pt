---
title: Usando o ranking para mostrar respostas - Bing Entity Search
titleSuffix: Azure Cognitive Services
description: Saiba como usar o ranking para mostrar as respostas que a API de Pesquisa de Entidades Bing devolve.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68423913"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Usando o ranking para exibir resultados de pesquisa de entidades  

Cada resposta de pesquisa de cada entidade inclui uma resposta [rankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) que especifica como deve apresentar os resultados de pesquisa devolvidos pela API de Pesquisa de Entidades Bing. Os grupos de resposta ao ranking resultam em conteúdo de vara, mainline e barra lateral. O resultado do polo é o resultado mais importante ou proeminente e deve ser exibido primeiro. Se não apresentar os resultados restantes num formato tradicional de linha principal e barra lateral, deve fornecer ao conteúdo principal uma maior visibilidade do que o conteúdo da barra lateral. 
  
Dentro de cada grupo, a matriz [de itens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identifica a ordem em que o conteúdo deve aparecer. Cada item fornece duas formas de identificar o resultado dentro de uma resposta.  
 

|Campo | Descrição  |
|---------|---------|
|`answerType` e `resultIndex` | `answerType`identifica a resposta (entidade ou `resultIndex` lugar) e identifica um resultado dentro dessa resposta (por exemplo, uma entidade). O índice começa em 0.|
|`value`    | `value`Contém um ID que corresponda à identificação de uma resposta ou um resultado dentro da resposta. Ou a resposta ou os resultados contêm a identificação, mas não ambas. |
  
Usar `answerType` o `resultIndex` e é um processo em duas etapas. Em primeiro `answerType` lugar, utilize para identificar a resposta que contém os resultados a apresentar. Em `resultIndex` seguida, use para indexar os resultados dessa resposta para obter o resultado para exibir. (O `answerType` valor é o nome do campo no objeto [SearchResponse.)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) Se é suposto mostrares todos os resultados da resposta em conjunto, `resultIndex` o item de resposta do ranking não inclui o campo.

A utilização do ID requer que combine o ID do ranking com a identificação de uma resposta ou um dos seus resultados. Se um objeto `id` de resposta incluir um campo, mostre todos os resultados da resposta em conjunto. Por exemplo, `Entities` se o `id` objeto incluir o campo, exiba todos os artigos das entidades em conjunto. Se `Entities` o objeto não `id` incluir o campo, `id` então cada entidade contém um campo e a resposta de classificação mistura as entidades com os resultados de Lugares.  
  
## <a name="ranking-response-example"></a>Exemplo de resposta de ranking

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

Com base nesta resposta de ranking, a barra lateral mostraria os resultados das duas entidades relacionadas com Jimi Hendrix.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](tutorial-bing-entities-search-single-page-app.md)
