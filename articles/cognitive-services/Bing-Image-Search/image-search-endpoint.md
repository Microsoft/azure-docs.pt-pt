---
title: Pontos de extremidade para o API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Imagem inclui três pontos de extremidade. O ponto de extremidade 1 retorna imagens da Web. O ponto de extremidade 2 retorna ImageInsights. O ponto de extremidade 3 retorna imagens de tendências.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072635"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Pontos de extremidade para o API de Pesquisa de Imagem do Bing

A **API de pesquisa de imagem** inclui três pontos de extremidade.  O ponto de extremidade 1 retorna imagens da Web com base em uma consulta. O ponto de extremidade 2 retorna [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  O ponto de extremidade 3 retorna imagens de tendências.

## <a name="endpoints"></a>Pontos Finais

Para obter os resultados da imagem usando a API do Bing, envie uma solicitação para um dos pontos de extremidade a seguir. Use os cabeçalhos e os parâmetros de URL para definir outras especificações.

**Ponto de extremidade 1:** Retorna imagens que são relevantes para a consulta de pesquisa do usuário definida por `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Ponto de extremidade 2:** Retorna informações sobre uma imagem, usando `GET` ou `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Uma solicitação GET retorna informações sobre uma imagem, como páginas da Web que incluem a imagem. Inclua o parâmetro [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) com uma solicitação de `GET`.

Ou, você pode incluir uma imagem binária no corpo de uma `POST` solicitação e definir o parâmetro [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) como `RecognizedEntities`. Isso retornará um [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) para usar como um parâmetro em uma solicitação de `GET` subsequente, que retorna informações sobre as pessoas na imagem.  Defina `modules` como `All` para obter todas as informações, exceto `RecognizedEntities` nos resultados da `POST` sem fazer outra chamada usando o `insightsToken`.


**Ponto de extremidade 3:** Retorna imagens que são tendências com base em solicitações de pesquisa feitas por outros. As imagens são separadas em categorias diferentes, por exemplo, com base em pessoas ou eventos dignos de notável.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Para obter uma lista de mercados que dão suporte a imagens de tendências, consulte [imagens de tendências](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência do [API de pesquisa de imagem do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) .
## <a name="response-json"></a>JSON de resposta
A resposta a uma solicitação de pesquisa de imagem inclui resultados como objetos JSON. Para obter exemplos de como analisar os resultados, consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) e o [código-fonte](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Passos seguintes
As APIs do **Bing** dão suporte a ações de pesquisa que retornam resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam resultados como objetos de resposta JSON.  Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros com suporte em cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando a API de pesquisa de imagem, consulte [pesquisa de imagem início rápido](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
