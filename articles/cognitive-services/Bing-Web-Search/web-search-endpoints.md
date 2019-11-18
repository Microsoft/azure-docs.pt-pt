---
title: Ponto de extremidade de pesquisa na Web
titleSuffix: Azure Cognitive Services
description: Para obter resultados da pesquisa na Web, envie uma solicitação de `GET` para o ponto de extremidade a seguir. Os cabeçalhos e os parâmetros de URL definem outras especificações.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111333"
---
# <a name="web-search-endpoint"></a>Ponto de extremidade Pesquisa na Web

A **API pesquisa na Web** retorna páginas da Web, notícias, imagens, vídeos e [entidades](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). As entidades têm informações resumidas sobre uma pessoa, lugar ou tópico.

## <a name="endpoint"></a>Ponto Final

Para obter resultados da pesquisa na Web usando a API do Bing, envie uma solicitação de `GET` para o ponto de extremidade a seguir. Os cabeçalhos e os parâmetros de URL definem outras especificações.

**Ponto de extremidade**: retorna resultados da Web que são relevantes para a consulta de pesquisa do usuário definida por `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Ponto de extremidade: para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, consulte a referência do [Bing Web API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) .

## <a name="response-json"></a>JSON de resposta

A resposta a uma solicitação de pesquisa na Web inclui todos os resultados como objetos JSON. A análise do resultado requer procedimentos que manipulem os elementos de cada tipo. Consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) e o [código-fonte](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) para obter exemplos.

## <a name="next-steps"></a>Passos seguintes

As APIs do **Bing** dão suporte a ações de pesquisa que retornam resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam resultados como objetos de resposta JSON.  Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e local específicos por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros com suporte em cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando a API de pesquisa da Web, consulte [Pesquisar o início rápido da Web](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
