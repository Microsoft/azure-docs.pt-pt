---
title: O ponto de extremidade API de Pesquisa de Entidade do Bing
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre o ponto de extremidade API de Pesquisa de Entidade do Bing e envie solicitações para ele.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9d08091d0ea6869d13e294e60454f85a84f672ad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424051"
---
# <a name="bing-entity-search-api-endpoint"></a>Ponto de extremidade API de Pesquisa de Entidade do Bing


O API de Pesquisa de Entidade do Bing tem um ponto de extremidade que retorna entidades da Web com base em uma consulta. Esses resultados da pesquisa são retornados em JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Obter resultados da entidade do ponto de extremidade

Para obter os resultados da entidade usando a **API**do Bing `GET` , envie uma solicitação para o ponto de extremidade a seguir. Use [cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) e [parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) para personalizar sua solicitação de pesquisa. As solicitações de pesquisa podem ser enviadas `?q=` usando o parâmetro.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [O que é o API de Pesquisa de Entidade do Bing?](overview.md)

## <a name="see-also"></a>Ver também 

Para obter mais informações sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, consulte o artigo de referência do [API de pesquisa de entidade do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
