---
title: Pontos finais para a API de Pesquisa de Imagem Bing
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Imagem inclui três pontos finais. O ponto final 1 retorna as imagens da web. O ponto final 2 devolve ImageInsights. O ponto final 3 retorna as imagens de tendência.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74072635"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Pontos finais para a API de Pesquisa de Imagem Bing

A **API de Pesquisa de Imagem**  inclui três pontos finais.  O ponto final 1 retorna as imagens da Web com base numa consulta. Ponto final 2 devolve [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  O ponto final 3 retorna as imagens de tendência.

## <a name="endpoints"></a>Pontos Finais

Para obter resultados de imagem usando a API Bing, envie um pedido para um dos seguintes pontos finais. Utilize os cabeçalhos e os parâmetros URL para definir especificações adicionais.

**Ponto final 1:** Devolve imagens relevantes à consulta de pesquisa do utilizador definida por `?q=""` .
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Ponto final 2:** Devolve insights sobre uma imagem, utilizando `GET` ou `POST` .
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Um pedido GET retorna insights sobre uma imagem, como páginas Web que incluem a imagem. Inclua o parâmetro [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) com um `GET` pedido.

Ou, pode incluir uma imagem binária no corpo de um `POST` pedido e definir o parâmetro dos [módulos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) para `RecognizedEntities` . Isto devolverá uma [introspeção Para](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) usar como parâmetro num `GET` pedido subsequente, que devolve informações sobre pessoas na imagem.  `modules`Prepare-se para obter todas as `All` informações, exceto `RecognizedEntities` nos resultados do sem fazer outra chamada `POST` usando o `insightsToken` .


**Ponto final 3:** Devolve imagens que estão em tendência com base em pedidos de pesquisa feitos por outros. As imagens são separadas em diferentes categorias, por exemplo, com base em pessoas ou eventos notáveis.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Para uma lista de mercados que suportam imagens de tendência, consulte [Trending Images](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Para mais detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [Bing Image Search API v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
## <a name="response-json"></a>Resposta JSON
A resposta a um pedido de pesquisa de imagem inclui resultados como objetos JSON. Por exemplo, a análise dos resultados ver o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) e o [código fonte](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Passos seguintes
As **APIs de Bing suportam** ações de pesquisa que devolvem resultados de acordo com o seu tipo.Todos os pontos finais de pesquisa retornam os resultados como objetos de resposta JSON. Todos os pontos finais suportam consultas que retornem uma linguagem e/ou localização específicas por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros suportados por cada ponto final, consulte as páginas de referência para cada tipo.
Para exemplos de pedidos básicos que utilizam a API de pesquisa de imagem, consulte [o Image Search Quick-starts](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
