---
title: Ponto final de Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Crie experiências de pesquisa personalizadas para tópicos sobre os quais você se preocupa. Os usuários veem os resultados da pesquisa adaptados ao conteúdo que eles se preocupam.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 03ec22caedd4e317b9e1fe781dc3d983febc7a6d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072790"
---
# <a name="custom-search"></a>Pesquisa Personalizada
Saiba como a Pesquisa Personalizada do Bing lhe permite criar experiências de pesquisa personalizada dos tópicos que mais lhe interessam. Os seus utilizadores veem resultados de pesquisa personalizados de acordo com os conteúdos que lhes interessam, em vez de terem de percorrer resultados de pesquisa que podem apresentar conteúdos irrelevantes.

## <a name="custom-search-endpoint"></a>Ponto de extremidade de pesquisa personalizado
Para obter resultados usando o API de Pesquisa Personalizada do Bing, envie uma solicitação de `GET` para o ponto de extremidade a seguir. Use os cabeçalhos e os parâmetros de URL para definir outras especificações.

Ponto de extremidade: retorna sugestões de pesquisa como resultados JSON que são relevantes para a entrada do usuário definida por `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Para obter exemplos que descrevem como configurar fontes de pesquisa personalizadas, consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência do [API de pesquisa personalizada do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) .

## <a name="custom-search-response-json"></a>JSON de resposta de pesquisa personalizada
Uma solicitação de pesquisa personalizada retorna resultados como objetos JSON, consulte [objetos de resposta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Sugestão automática personalizada
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

## <a name="next-steps"></a>Passos seguintes
As APIs do **Bing** dão suporte a ações de pesquisa que retornam resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam resultados como objetos de resposta JSON.  Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros com suporte em cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando o API de Pesquisa Personalizada, consulte [início rápido da pesquisa personalizada](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
