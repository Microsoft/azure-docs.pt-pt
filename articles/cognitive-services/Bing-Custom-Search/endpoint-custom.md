---
title: Ponto final de Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade API de Pesquisa Personalizada do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e3b544d8d655b653383cc999de233e2163433d85
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405068"
---
# <a name="custom-search"></a>Pesquisa Personalizada
Saiba como a Pesquisa Personalizada do Bing lhe permite criar experiências de pesquisa personalizada dos tópicos que mais lhe interessam. Os seus utilizadores veem resultados de pesquisa personalizados de acordo com os conteúdos que lhes interessam, em vez de terem de percorrer resultados de pesquisa que podem apresentar conteúdos irrelevantes.

## <a name="custom-search-endpoint"></a>Ponto de extremidade de pesquisa personalizado
Para obter resultados usando o API de pesquisa personalizada do Bing, envie uma `GET` solicitação para o ponto de extremidade a seguir. Use os cabeçalhos e os parâmetros de URL para definir outras especificações.

Extremidade Retorna sugestões de pesquisa como resultados JSON que são relevantes para a entrada do usuário definida `?q=""`pelo.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Para obter exemplos que descrevem como configurar fontes de pesquisa personalizadas, consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência do [API de pesquisa personalizada do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) .

## <a name="custom-search-response-json"></a>JSON de resposta de pesquisa personalizada
Uma solicitação de pesquisa personalizada retorna resultados como objetos JSON, consulte [objetos de resposta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Personalizar Sugestão Automática
A API de sugestão automática personalizada permite enviar um termo de consulta de pesquisa parcial para o Bing e obter uma lista de consultas sugeridas que você pode configurar. Com a sugestão automática personalizada, você adiciona sugestões retornadas pela API e, opcionalmente, especifica se as sugestões geradas pelo Bing devem ser incluídas.

## <a name="custom-autosuggest-endpoint"></a>Ponto de extremidade de sugestão automática personalizado
Para solicitar sugestões de consulta personalizada, envie uma solicitação GET para:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Para obter informações sobre como definir sugestões personalizadas, consulte [definir sugestões de pesquisa personalizadas](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Pesquisa de Imagem personalizado
A API de Pesquisa de Imagem personalizada permite enviar uma consulta de pesquisa para o Bing e obter uma lista de imagens relevantes de sua instância de pesquisa personalizada.

## <a name="custom-image-search-endpoint"></a>Ponto de extremidade Pesquisa de Imagem personalizado
Para solicitar imagens de sua instância de pesquisa personalizada, envie uma solicitação GET para a seguinte URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Para obter informações sobre como configurar uma instância de pesquisa personalizada, consulte [configurar sua experiência de pesquisa personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Passos Seguintes
As APIs do **Bing** dão suporte a ações de pesquisa que retornam resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam resultados como objetos de resposta JSON.  Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros com suporte em cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando o API de Pesquisa Personalizada, consulte [início rápido da pesquisa personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
