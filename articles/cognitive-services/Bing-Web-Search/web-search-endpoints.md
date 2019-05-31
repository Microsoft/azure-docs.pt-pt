---
title: Ponto final de pesquisa Web
titleSuffix: Azure Cognitive Services
description: Resumo do ponto de final de API de pesquisa do Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: c55e2f8b1685893ecc813c0d5d94e894f66fb186
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390461"
---
# <a name="web-search-endpoint"></a>Ponto final de pesquisa Web

O **API de pesquisa na Web** retorna páginas da Web, notícias, imagens, vídeos, e [entidades](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entidades têm informações de resumo sobre uma pessoa, lugar ou tópico.

## <a name="endpoint"></a>Ponto Final

Para obter os resultados de pesquisa da Web usando a API do Bing, enviar um `GET` pedido para o seguinte ponto de extremidade. Os cabeçalhos e os parâmetros de URL definem ainda mais especificações.

**Ponto final**: Devolve os resultados da Web que são relevantes para a consulta de pesquisa do utilizador definida por `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Ponto final: Para obter mais informações sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, consulte a [API Web do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) referência.

## <a name="response-json"></a>Resposta JSON

A resposta a um pedido de pesquisa Web inclui todos os resultados como objetos JSON. O resultado de análise necessita de procedimentos que lidar com os elementos de cada tipo. Consulte a [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) e [código-fonte](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) para obter exemplos.

## <a name="next-steps"></a>Passos Seguintes

O **Bing** ações de pesquisa devolvem resultados de acordo com seu tipo de suporte de APIs. Todos os pontos finais de pesquisa devolvem resultados como objetos de resposta JSON.  Todos os pontos finais suportam consultas que devolvem um idioma específico e o local, longitude, latitude e radius de pesquisa.

Para obter informações completas sobre os parâmetros suportados por cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas com a API de pesquisa Web, consulte [pesquisar os Web inícios Rápidos](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
