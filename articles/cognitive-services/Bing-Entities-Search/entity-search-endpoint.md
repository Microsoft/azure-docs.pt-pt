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
ms.openlocfilehash: 7fc65b27c3c02d6102210ae277690795d763d91a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338262"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entidade Pesquisa ponto final API

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)


A API de Pesquisa de Entidade Bing tem um ponto final que devolve entidades da Web com base numa consulta. Estes resultados de pesquisa são devolvidos em JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Obtenha os resultados da entidade a partir do ponto final

Para obter os resultados da entidade utilizando a **API Bing,** envie um `GET` pedido para o seguinte ponto final. Utilize [cabeçalhos](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) e [parâmetros de consulta](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) para personalizar o seu pedido de pesquisa. Os pedidos de pesquisa podem ser enviados usando o `?q=` parâmetro.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O que é a API de Pesquisa de Entidade Bing?](overview.md)

## <a name="see-also"></a>Ver também 

Para obter mais informações sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, consulte o artigo de referência [de referência v7 da API de pesquisa de entidades Bing.](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)