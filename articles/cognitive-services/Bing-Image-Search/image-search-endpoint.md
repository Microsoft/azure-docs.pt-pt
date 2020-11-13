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
ms.openlocfilehash: 67fec77136f5d593279be2846e63c51b60e16bb4
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593523"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Pontos finais para a API de Pesquisa de Imagem Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A **API de Pesquisa de Imagem**  inclui três pontos finais.  O ponto final 1 retorna as imagens da Web com base numa consulta. Ponto final 2 devolve [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  O ponto final 3 retorna as imagens de tendência.

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
Um pedido GET retorna insights sobre uma imagem, como páginas Web que incluem a imagem. Inclua o parâmetro [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) com um `GET` pedido.

Ou, pode incluir uma imagem binária no corpo de um `POST` pedido e definir o parâmetro dos [módulos](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) para `RecognizedEntities` . Isto devolverá uma [introspeção Para](/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) usar como parâmetro num `GET` pedido subsequente, que devolve informações sobre pessoas na imagem.  `modules`Prepare-se para obter todas as `All` informações, exceto `RecognizedEntities` nos resultados do sem fazer outra chamada `POST` usando o `insightsToken` .


**Ponto final 3:** Devolve imagens que estão em tendência com base em pedidos de pesquisa feitos por outros. As imagens são separadas em diferentes categorias, por exemplo, com base em pessoas ou eventos notáveis.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Para uma lista de mercados que suportam imagens de tendência, consulte [Trending Images](./trending-images.md).

Para mais detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [Bing Image Search API v7.](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
## <a name="response-json"></a>Resposta JSON
A resposta a um pedido de pesquisa de imagem inclui resultados como objetos JSON. Por exemplo, a análise dos resultados ver o [tutorial](./tutorial-bing-image-search-single-page-app.md) e o [código fonte](./tutorial-bing-image-search-single-page-app.md).

## <a name="next-steps"></a>Passos seguintes
As **APIs de Bing suportam** ações de pesquisa que devolvem resultados de acordo com o seu tipo. Todos os pontos finais de pesquisa retornam os resultados como objetos de resposta JSON.  Todos os pontos finais suportam consultas que retornem uma linguagem e/ou localização específicas por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros suportados por cada ponto final, consulte as páginas de referência para cada tipo.
Para exemplos de pedidos básicos que utilizam a API de pesquisa de imagem, consulte [o Image Search Quick-starts](./overview.md).