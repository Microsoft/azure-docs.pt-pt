---
title: Ponto final de pesquisa web
titleSuffix: Azure Cognitive Services
description: Para obter os resultados da pesquisa na Web, envie um `GET` pedido para o seguinte ponto final. Os cabeçalhos e os parâmetros URL definem especificações adicionais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74111333"
---
# <a name="web-search-endpoint"></a>Ponto final de pesquisa web

A **API de Pesquisa Web** devolve páginas web, notícias, imagens, vídeos e [entidades.](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) As entidades têm informações sumárias sobre uma pessoa, lugar ou tópico.

## <a name="endpoint"></a>Ponto final

Para obter os resultados da pesquisa na Web utilizando a API Bing, envie um `GET` pedido para o seguinte ponto final. Os cabeçalhos e os parâmetros URL definem especificações adicionais.

**Ponto final**: Devolve resultados web relevantes para a consulta de pesquisa do utilizador definida por `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Ponto final: Para mais detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e muito mais, consulte a referência [Bing Web API v7.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)

## <a name="response-json"></a>Resposta JSON

A resposta a um pedido de pesquisa na Web inclui todos os resultados como objetos JSON. A análise do resultado requer procedimentos que manuseiem os elementos de cada tipo. Consulte o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) e [o código fonte,](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) por exemplo.

## <a name="next-steps"></a>Passos seguintes

As **APIs de Bing suportam** ações de pesquisa que devolvem resultados de acordo com o seu tipo.Todos os pontos finais de pesquisa retornam os resultados como objetos de resposta JSON. Todos os pontos finais suportam consultas que devolvem uma língua e localização específicas por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros suportados por cada ponto final, consulte as páginas de referência para cada tipo.
Para, por exemplo, de pedidos básicos que utilizem a API de pesquisa web, consulte [pesquisar o Início Rápido da Web](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
