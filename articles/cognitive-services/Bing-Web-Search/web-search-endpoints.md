---
title: Ponto final de pesquisa web
titleSuffix: Azure Cognitive Services
description: Para obter resultados de `GET` pesquisa na Web, envie um pedido para o seguinte ponto final. Os cabeçalhos e os parâmetros url definem especificações adicionais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111333"
---
# <a name="web-search-endpoint"></a>Ponto final de pesquisa web

A **Web Search API** retorna páginas Web, notícias, imagens, vídeos e [entidades.](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) As entidades têm informação sumária sobre uma pessoa, um local ou um tópico.

## <a name="endpoint"></a>Ponto Final

Para obter resultados de pesquisa na `GET` Web utilizando a API Bing, envie um pedido para o seguinte ponto final. Os cabeçalhos e os parâmetros url definem especificações adicionais.

**Ponto final**: Devoluções resultados web relevantes para `?q=""`a consulta de pesquisa do utilizador definida por .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Ponto final: Para mais detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, consulte a referência [Bing Web API v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)

## <a name="response-json"></a>Resposta JSON

A resposta a um pedido de pesquisa web inclui todos os resultados como objetos JSON. Analisar o resultado requer procedimentos que manuseiem os elementos de cada tipo. Consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) e o [código de origem,](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) por exemplo.

## <a name="next-steps"></a>Passos seguintes

As APIs **bing** suportam ações de pesquisa que devolvem resultados de acordo com o seu tipo.Todos os pontos finais de pesquisa retornam os resultados como objetos de resposta JSON. Todos os pontos finais suportam consultas que devolvem uma linguagem e localização específicas por longitude, latitude e raio de busca.

Para obter informações completas sobre os parâmetros suportados por cada ponto final, consulte as páginas de referência para cada tipo.
Por exemplo, em pedidos básicos utilizando a API de pesquisa web, consulte [Search the Web Quick-starts](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
