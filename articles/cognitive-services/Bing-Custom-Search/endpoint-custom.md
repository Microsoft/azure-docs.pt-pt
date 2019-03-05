---
title: Ponto final de Pesquisa Personalizada do Bing
titlesuffix: Azure Cognitive Services
description: Resumo do ponto de final de API de pesquisa personalizada do Bing.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: v-gedod
ms.openlocfilehash: d01f863f0c42754e2dcc36f6cab1bed1f3dc3b6c
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337809"
---
# <a name="custom-search"></a>Pesquisa Personalizada
Saiba como a Pesquisa Personalizada do Bing lhe permite criar experiências de pesquisa personalizada dos tópicos que mais lhe interessam. Os seus utilizadores veem resultados de pesquisa personalizados de acordo com os conteúdos que lhes interessam, em vez de terem de percorrer resultados de pesquisa que podem apresentar conteúdos irrelevantes.

## <a name="custom-search-endpoint"></a>Ponto final de pesquisa personalizada
Para obter resultados usando a API de pesquisa personalizada do Bing, enviar um `GET` pedido para o seguinte ponto de extremidade. Utilize os cabeçalhos e os parâmetros de URL para definir ainda mais especificações.

Ponto final: Sugestões de pesquisa devolve resultados JSON que são relevantes para a entrada do usuário definida por `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Para obter exemplos que descrevem como configurar origens de pesquisa personalizada, consulte a [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Para obter detalhes sobre os cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a [API de pesquisa personalizada do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) referência.

## <a name="custom-search-response-json"></a>Pesquisa personalizada resposta JSON
Um pedido de pesquisa personalizada devolve resultados como objetos JSON, veja [objetos de resposta](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Personalizar Sugestão Automática
API de sugestão automática personalizada permite-lhe enviar um termo de consulta de pesquisa parcial para o Bing e obter uma lista de consultas sugeridas, que pode configurar. Com a sugestão automática personalizada, adicione sugestões devolvidos pela API e, opcionalmente, especifique se inclui sugestões gerados pelo Bing.

## <a name="custom-autosuggest-endpoint"></a>Ponto final de sugestão automática personalizada
Para pedir as sugestões de consulta personalizada, envie um pedido GET para:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Para obter informações sobre como definir sugestões personalizadas, consulte [definem as sugestões de pesquisa personalizada](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Pesquisa de imagens personalizadas
A API de pesquisa de imagens personalizada permite-lhe enviar uma consulta de pesquisa para o Bing e obter uma lista de imagens relevantes de sua instância de pesquisa personalizada.

## <a name="custom-image-search-endpoint"></a>Ponto final de pesquisa de imagem personalizada
Para solicitar imagens de sua instância de pesquisa personalizada, envie um pedido GET para o seguinte URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Para obter informações sobre como configurar uma instância de pesquisa personalizada, consulte [configurar a sua experiência de pesquisa personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Passos Seguintes
O **Bing** ações de pesquisa devolvem resultados de acordo com seu tipo de suporte de APIs. Todos os pontos finais de pesquisa devolvem resultados como objetos de resposta JSON.  Todos os pontos finais suportam consultas que devolvem um idioma específico e/ou localizações, longitude, latitude e radius de pesquisa.

Para obter informações completas sobre os parâmetros suportados por cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas com a API de pesquisa personalizada, consulte [inícios rápidos pesquisa personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
