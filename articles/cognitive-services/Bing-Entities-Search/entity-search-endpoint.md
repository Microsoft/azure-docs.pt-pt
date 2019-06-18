---
title: O ponto final de API de pesquisa de entidades do Bing
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre o ponto final de API de pesquisa de entidades do Bing e enviar pedidos para o mesmo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 43bca65810d09c87f7f473b3bbac71ca6a7f9bc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389000"
---
# <a name="bing-entity-search-api-endpoint"></a>Ponto final de API de pesquisa de entidades do Bing


A API de pesquisa de entidades do Bing tem um ponto de extremidade que retorne entidades a partir da Web com base numa consulta. Esses resultados de pesquisa são devolvidos em JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Obter resultados de entidade a partir do ponto final

Para obter os resultados utilizando o **API do Bing**, enviar um `GET` pedido para o seguinte ponto de extremidade. Uso [cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) e [parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) para personalizar o seu pedido de pesquisa. Pedidos de pesquisa podem ser enviados com o `?q=` parâmetro.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [O que é a API de pesquisa de entidades do Bing?](overview.md)

## <a name="see-also"></a>Consulte também 

Para obter mais informações sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, consulte a [API de pesquisa de entidades do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) artigo de referência.
