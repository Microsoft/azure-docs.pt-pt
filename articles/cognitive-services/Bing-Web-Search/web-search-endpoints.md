---
title: Ponto de extremidade de pesquisa na Web
titleSuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade da API de pesquisa da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: be622c5636c253c48bec4d67fba58319262c2603
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883569"
---
# <a name="web-search-endpoint"></a>Ponto de extremidade Pesquisa na Web

A **API pesquisa na Web** retorna páginas da Web, notícias, imagens, vídeos e [entidades](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). As entidades têm informações resumidas sobre uma pessoa, lugar ou tópico.

## <a name="endpoint"></a>Ponto Final

Para obter os resultados da pesquisa na Web usando a API do `GET` Bing, envie uma solicitação para o ponto de extremidade a seguir. Os cabeçalhos e os parâmetros de URL definem outras especificações.

**Ponto de extremidade**: Retorna resultados da Web que são relevantes para a consulta de pesquisa do usuário `?q=""`definida por.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Extremidade Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, consulte a referência de [v7 da API Web do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) .

## <a name="response-json"></a>JSON de resposta

A resposta a uma solicitação de pesquisa na Web inclui todos os resultados como objetos JSON. A análise do resultado requer procedimentos que manipulem os elementos de cada tipo. Consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) e o [código-fonte](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) para obter exemplos.

## <a name="next-steps"></a>Passos Seguintes

As APIs do **Bing** dão suporte a ações de pesquisa que retornam resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam resultados como objetos de resposta JSON.  Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e local específicos por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros com suporte em cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando a API de pesquisa da Web, consulte [Pesquisar o início rápido da Web](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
