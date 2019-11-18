---
title: Pontos finais de Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
description: Este artigo fornece um resumo dos pontos de extremidade da API de pesquisa de notícias; Notícias, principais notícias e notícias de tendências.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111479"
---
# <a name="bing-news-search-api-endpoints"></a>API de Pesquisa de Notícias do Bing pontos de extremidade

A **API pesquisa de notícias** retorna artigos de notícias, páginas da Web, imagens, vídeos e [entidades](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). As entidades contêm informações resumidas sobre uma pessoa, lugar ou tópico.

## <a name="endpoints"></a>Pontos Finais

Para obter os resultados da pesquisa de notícias usando o API de Pesquisa de Notícias do Bing, envie uma solicitação de `GET` para um dos pontos de extremidade a seguir. Os cabeçalhos e os parâmetros de URL definem outras especificações.

### <a name="news-items-by-search-query"></a>Itens de notícias por consulta de pesquisa

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Retorna itens de notícias com base em uma consulta de pesquisa. Se a consulta de pesquisa estiver vazia, a API retornará os principais artigos de notícias de categorias diferentes. Envie uma consulta por URL codificando seu termo de pesquisa e anexando-o ao parâmetro`q=""`. Para disponibilidade, consulte [países/regiões e mercados com suporte](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Principais itens de notícias por categoria

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Retorna os principais itens de notícias por categoria. Você pode solicitar especificamente os principais artigos de negócios, esportes ou entretenimento usando `category=business`, `category=sports`ou `category=entertainment`.  O parâmetro `category` só pode ser usado com a URL `/news`. Há alguns requisitos formais para especificar categorias; consulte `category` na documentação do [parâmetro de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) . Envie uma consulta por URL codificando seu termo de pesquisa e anexando-o ao parâmetro`q=""`. Para disponibilidade, consulte [países/regiões e mercados com suporte](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Tópicos de notícias de tendências 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Retorna os tópicos de notícias que estão se tendência em redes sociais no momento. Quando a opção de `/trendingtopics` é incluída, a pesquisa do Bing ignora vários outros parâmetros, como `freshness` e `?q=""`. Para disponibilidade, consulte [países/regiões e mercados com suporte](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Passos seguintes

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência a [API de pesquisa de notícias v7 do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) .

Para obter informações completas sobre os parâmetros com suporte em cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando a API de pesquisa de notícias, consulte [pesquisa de notícias do Bing início rápido](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
