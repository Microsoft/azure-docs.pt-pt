---
title: Pontos finais para a API de Pesquisa de Imagem Bing
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Imagem inclui três pontos finais. Endpoint 1 devolve imagens da web. Endpoint 2 devolve ImageInsights. Endpoint 3 retorna imagens de tendência.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74072635"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Pontos finais para a API de Pesquisa de Imagem Bing

A **API de Pesquisa de Imagem** inclui três pontos finais.  Endpoint 1 devolve imagens da Web com base numa consulta. Endpoint 2 devolve [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Endpoint 3 retorna imagens de tendência.

## <a name="endpoints"></a>Pontos Finais

Para obter resultados de imagem utilizando a API bing, envie um pedido para um dos seguintes pontos finais. Utilize os cabeçalhos e os parâmetros url para definir especificações adicionais.

**Ponto final 1:** Devolve imagens relevantes para a consulta de `?q=""`pesquisa do utilizador definida por .
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Ponto final 2:** Devolve insights sobre uma `GET` imagem, usando ou `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Um pedido get devolve insights sobre uma imagem, como páginas Web que incluem a imagem. Inclua os [insightsParamToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) com um `GET` pedido.

Ou, pode incluir uma imagem binária `POST` no corpo de um pedido `RecognizedEntities`e definir o parâmetro dos [módulos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) para . Isto devolverá um [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) para usar como `GET` parâmetro num pedido subsequente, que devolve informações sobre pessoas na imagem.  Prepare-se `modules` `All` para obter `RecognizedEntities` todas as informações, exceto nos resultados do `POST` sem fazer outra chamada usando o `insightsToken`.


**Ponto final 3:** Devolve imagens que estão em tendência com base em pedidos de pesquisa feitos por outros. As imagens são separadas em diferentes categorias, por exemplo, com base em pessoas ou eventos notáveis.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Para obter uma lista de mercados que suportam imagens de tendência, consulte [Imagens de Tendência](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Para mais detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [Bing Image Search API v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
## <a name="response-json"></a>Resposta JSON
A resposta a um pedido de pesquisa de imagem inclui resultados como objetos JSON. Por exemplo, a análise dos resultados consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) e o [código fonte](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Passos seguintes
As APIs **bing** suportam ações de pesquisa que devolvem resultados de acordo com o seu tipo.Todos os pontos finais de pesquisa retornam os resultados como objetos de resposta JSON. Todos os pontos finais suportam consultas que devolvem uma linguagem e/ou localização específicas por longitude, latitude e raio de busca.

Para obter informações completas sobre os parâmetros suportados por cada ponto final, consulte as páginas de referência para cada tipo.
Por exemplo, de pedidos básicos utilizando a API de pesquisa de imagem, consulte Image [Search Quick-starts](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
