---
title: O ponto de extremidade API de Pesquisa de Entidade do Bing
titleSuffix: Azure Cognitive Services
description: O API de Pesquisa de Entidade do Bing tem um ponto de extremidade que retorna entidades da Web com base em uma consulta. Esses resultados da pesquisa são retornados em JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072659"
---
# <a name="bing-entity-search-api-endpoint"></a>Ponto de extremidade API de Pesquisa de Entidade do Bing


O API de Pesquisa de Entidade do Bing tem um ponto de extremidade que retorna entidades da Web com base em uma consulta. Esses resultados da pesquisa são retornados em JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Obter resultados da entidade do ponto de extremidade

Para obter os resultados da entidade usando a **API do Bing**, envie uma solicitação de `GET` para o ponto de extremidade a seguir. Use [cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) e [parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) para personalizar sua solicitação de pesquisa. As solicitações de pesquisa podem ser enviadas usando o parâmetro `?q=`.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O que é o API de Pesquisa de Entidade do Bing?](overview.md)

## <a name="see-also"></a>Consulte também 

Para obter mais informações sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, consulte o artigo de referência do [API de pesquisa de entidade do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
