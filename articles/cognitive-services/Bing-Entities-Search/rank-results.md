---
title: Usando classificação para exibir respostas-Pesquisa de Entidade do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como usar a classificação para exibir as respostas que o API de Pesquisa de Entidade do Bing retorna.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423913"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Usando a classificação para exibir os resultados da pesquisa de entidade  

Cada resposta de pesquisa de entidade inclui uma resposta [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) que especifica como você deve exibir os resultados da pesquisa retornados pelo API de pesquisa de entidade do Bing. Os resultados da classificação agrupam o conteúdo do pólo, da principal e da barra lateral. O resultado do pólo é o resultado mais importante ou proeminente e deve ser exibido primeiro. Se você não exibir os resultados restantes em um formato principal e de barra lateral tradicional, deverá fornecer o conteúdo principal maior visibilidade do que o conteúdo da barra lateral. 
  
Dentro de cada grupo, a matriz de [itens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identifica a ordem em que o conteúdo deve aparecer. Cada item fornece duas maneiras de identificar o resultado em uma resposta.  
 

|Campo | Descrição  |
|---------|---------|
|`answerType` e `resultIndex` | `answerType`Identifica a resposta (entidade ou local) e `resultIndex` identifica um resultado dentro dessa resposta (por exemplo, uma entidade). O índice começa em 0.|
|`value`    | `value`Contém uma ID que corresponde à ID de uma resposta ou a um resultado na resposta. A resposta ou os resultados contêm a ID, mas não ambos. |
  
Usar o `answerType` e `resultIndex` o é um processo de duas etapas. Primeiro, use `answerType` para identificar a resposta que contém os resultados a serem exibidos. Em seguida `resultIndex` , use para indexar os resultados dessa resposta para obter o resultado a ser exibido. (O `answerType` valor é o nome do campo no objeto [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) .) Se você tiver que exibir todos os resultados da resposta juntos, o item de resposta de classificação não incluirá o `resultIndex` campo.

O uso da ID exige que você corresponda à ID de classificação com a ID de uma resposta ou de um de seus resultados. Se um objeto de resposta incluir `id` um campo, exiba todos os resultados da resposta juntos. Por exemplo, se o `Entities` objeto incluir o `id` campo, exiba todos os artigos de entidades juntos. Se o `Entities` objeto não incluir o `id` campo, cada entidade conterá um `id` campo e a resposta de classificação mistura as entidades com os resultados dos locais.  
  
## <a name="ranking-response-example"></a>Exemplo de resposta de classificação

Veja a seguir um exemplo de [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
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

Com base nessa resposta de classificação, a barra lateral exibiria os dois resultados da entidade relacionados ao Jimi Hendrix.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](tutorial-bing-entities-search-single-page-app.md)
