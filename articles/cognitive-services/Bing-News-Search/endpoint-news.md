---
title: Pontos finais de Pesquisa de Notícias do Bing
titlesuffix: Azure Cognitive Services
description: Resumo do ponto de extremidade de API de pesquisa de notícias.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 6ea218da23d65696c76008cb15e183fcc4bbda10
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383233"
---
# <a name="bing-news-search-api-endpoints"></a>Pontos finais de API de pesquisa de notícias do Bing

O **API de pesquisa de notícias** devolve notícias imagens de páginas da Web, artigos, vídeos, e [entidades](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entidades contêm informações de resumo sobre uma pessoa, lugar ou tópico.

## <a name="endpoints"></a>Pontos Finais

Para obter notícias resultados de pesquisa usando a API de pesquisa de notícias do Bing, enviar um `GET` solicitação a um dos seguintes pontos finais. Os cabeçalhos e os parâmetros de URL definem ainda mais especificações.

### <a name="news-items-by-search-query"></a>Itens de notícias por consulta de pesquisa

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Devolve itens de notícias com base numa consulta de pesquisa. Se a consulta de pesquisa estiver vazia, a API irá devolver a artigos de notícias de principais de categorias diferentes. Enviar uma consulta ao url de seu termo de pesquisa de codificação e anexando-o para o`q=""` parâmetro. Para disponibilidade, consulte [países/regiões e mercados suportados](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Itens de notícias superior por categoria

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Devolve os itens de notícias superior por categoria. Pode pedir especificamente o negócio superior, desporto ou artigos de entretenimento usando `category=business`, `category=sports`, ou `category=entertainment`.  O `category` parâmetro só pode ser utilizado com o `/news` URL. Existem alguns requisitos formais para especificar categorias; consulte a `category` no [parâmetro de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) documentação. Enviar uma consulta ao url de seu termo de pesquisa de codificação e anexando-o para o`q=""` parâmetro. Para disponibilidade, consulte [países/regiões e mercados suportados](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Tópicos de notícias em destaque 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Devolve os tópicos de notícias são popularidade atual nas redes sociais. Quando o `/trendingtopics` opção é incluída, pesquisa do Bing ignora vários outros parâmetros, tais como `freshness` e `?q=""`. Para disponibilidade, consulte [países/regiões e mercados suportados](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Passos Seguintes

Para obter detalhes sobre os cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a [pesquisa de notícias do Bing API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) referência.

Para obter informações completas sobre os parâmetros suportados por cada ponto de extremidade, consulte as páginas de referência para cada tipo.
Para obter exemplos de solicitações básicas com a API de pesquisa de notícias, consulte [inícios rápidos de pesquisa de notícias do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
