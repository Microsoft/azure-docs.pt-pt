---
title: Pontos finais de Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade da API de pesquisa de notícias.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 82e37e8fa47b467e7c2fe98f801482675809a266
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423776"
---
# <a name="bing-news-search-api-endpoints"></a>API de Pesquisa de Notícias do Bing pontos de extremidade

A **API pesquisa de notícias** retorna artigos de notícias, páginas da Web, imagens, vídeos e [entidades](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). As entidades contêm informações resumidas sobre uma pessoa, lugar ou tópico.

## <a name="endpoints"></a>Pontos Finais

Para obter os resultados da pesquisa de notícias usando o API de pesquisa de notícias do Bing `GET` , envie uma solicitação para um dos pontos de extremidade a seguir. Os cabeçalhos e os parâmetros de URL definem outras especificações.

### <a name="news-items-by-search-query"></a>Itens de notícias por consulta de pesquisa

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Retorna itens de notícias com base em uma consulta de pesquisa. Se a consulta de pesquisa estiver vazia, a API retornará os principais artigos de notícias de categorias diferentes. Envie uma consulta por URL codificando seu termo de pesquisa e anexando-`q=""` o ao parâmetro. Para disponibilidade, consulte [países/regiões e mercados com suporte](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Principais itens de notícias por categoria

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Retorna os principais itens de notícias por categoria. Você pode solicitar especificamente os principais artigos de negócios, esportes ou entretenimento usando `category=business`, `category=sports`ou `category=entertainment`.  O `category` parâmetro só pode ser usado com a `/news` URL. Há alguns requisitos formais para especificar categorias; consulte na documentação do [parâmetro de consulta.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) `category` Envie uma consulta por URL codificando seu termo de pesquisa e anexando-`q=""` o ao parâmetro. Para disponibilidade, consulte [países/regiões e mercados com suporte](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Tópicos de notícias de tendências 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Retorna os tópicos de notícias que estão se tendência em redes sociais no momento. Quando a `/trendingtopics` opção é incluída, a pesquisa do Bing ignora vários outros parâmetros, `freshness` como e `?q=""`. Para disponibilidade, consulte [países/regiões e mercados com suporte](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Passos Seguintes

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência a [API de pesquisa de notícias v7 do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) .

Para obter informações completas sobre os parâmetros com suporte em cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas usando a API de pesquisa de notícias, consulte [pesquisa de notícias do Bing início rápido](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
