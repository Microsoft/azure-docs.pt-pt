---
title: O ponto final de pesquisa da API da Entidade Bing
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Entidade Bing tem um ponto final que devolve entidades da Web com base numa consulta. Estes resultados de pesquisa são devolvidos em JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74072659"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entidade Pesquisa ponto final API


A API de Pesquisa de Entidade Bing tem um ponto final que devolve entidades da Web com base numa consulta. Estes resultados de pesquisa são devolvidos em JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Obtenha os resultados da entidade a partir do ponto final

Para obter os resultados da entidade utilizando a **API Bing,** envie um `GET` pedido para o seguinte ponto final. Utilize [cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) e [parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) para personalizar o seu pedido de pesquisa. Os pedidos de pesquisa podem ser enviados usando o `?q=` parâmetro.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O que é a API de Pesquisa de Entidade Bing?](overview.md)

## <a name="see-also"></a>Consulte também 

Para obter mais informações sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, consulte o artigo de referência [de referência v7 da API de pesquisa de entidades Bing.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
