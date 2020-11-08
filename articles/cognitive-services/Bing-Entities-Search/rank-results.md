---
title: Utilização do ranking para exibir respostas - Pesquisa de Entidade Bing
titleSuffix: Azure Cognitive Services
description: Saiba como usar o ranking para mostrar as respostas que a API de pesquisa de entidade bing retorna.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b63c2e53f34ac86f8ddf5ad300c2465ee2d9f032
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365640"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Utilização do ranking para exibir resultados de pesquisa de entidades  

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Cada resposta de pesquisa de entidade inclui uma resposta [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) que especifica como deve exibir os resultados de pesquisa devolvidos pela API de Pesquisa de Entidade Bing. Os grupos de resposta do ranking resultam em conteúdo sonoro, mainline e sidebar. O resultado do poste é o resultado mais importante ou proeminente e deve ser exibido primeiro. Se não apresentar os resultados restantes num formato tradicional de mainline e barra lateral, deve fornecer ao conteúdo principal uma maior visibilidade do que o conteúdo da barra lateral. 
  
Dentro de cada grupo, a matriz [de Itens](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identifica a ordem em que o conteúdo deve aparecer. Cada item fornece duas formas de identificar o resultado dentro de uma resposta.  
 

|Campo | Descrição  |
|---------|---------|
|`answerType` e `resultIndex` | `answerType` identifica a resposta (entidade ou local) e `resultIndex` identifica um resultado dentro dessa resposta (por exemplo, uma entidade). O índice começa em 0.|
|`value`    | `value` Contém um ID que corresponde ao ID de uma resposta ou de um resultado dentro da resposta. Ou a resposta ou os resultados contêm o ID, mas não ambos. |
  
Usar o `answerType` e é um processo em `resultIndex` duas etapas. Primeiro, utilize `answerType` para identificar a resposta que contém os resultados a exibir. Em seguida, use `resultIndex` para indexar os resultados dessa resposta para obter o resultado a visualizar. (O `answerType` valor é o nome do campo no objeto [SearchResponse.)](/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) Se é suposto mostrar todos os resultados da resposta em conjunto, o item de resposta do ranking não inclui o `resultIndex` campo.

A utilização do ID requer que combine o ID do ranking com o ID de uma resposta ou um dos seus resultados. Se um objeto de resposta incluir um `id` campo, apresente todos os resultados da resposta em conjunto. Por exemplo, se o `Entities` objeto incluir o `id` campo, exiba todos os artigos das entidades em conjunto. Se o `Entities` objeto não incluir o `id` campo, cada entidade contém um campo `id` e a resposta do ranking mistura as entidades com os resultados do Places.  
  
## <a name="ranking-response-example"></a>Exemplo de resposta de ranking

O seguinte mostra um exemplo [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
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

Com base nesta resposta do ranking, a barra lateral mostraria os resultados das duas entidades relacionadas com Jimi Hendrix.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](tutorial-bing-entities-search-single-page-app.md)