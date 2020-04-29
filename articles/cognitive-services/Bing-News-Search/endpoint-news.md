---
title: Pontos finais de Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
description: Este artigo fornece um resumo dos pontos finais da Pesquisa de Notícias da API; notícias, notícias de topo, e notícias de tendência.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74111479"
---
# <a name="bing-news-search-api-endpoints"></a>Bing News Search API pontos finais

A API de **Pesquisa de Notícias** devolve artigos de notícias, páginas Web, imagens, vídeos e [entidades.](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) As entidades contêm informações sumárias sobre uma pessoa, um local ou um tópico.

## <a name="endpoints"></a>Pontos Finais

Para obter resultados de pesquisa de notícias `GET` usando a API de Pesquisa de Notícias Bing, envie um pedido para um dos seguintes pontos finais. Os cabeçalhos e os parâmetros url definem especificações adicionais.

### <a name="news-items-by-search-query"></a>Notícias por consulta de pesquisa

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Devolve artigos de notícias com base numa consulta de pesquisa. Se a consulta de pesquisa estiver vazia, a API devolverá artigos de topo de diferentes categorias. Envie uma consulta através de url codificando o`q=""` seu termo de pesquisa e acomodá-lo ao parâmetro. Para disponibilidade, consulte [países/regiões e mercados apoiados.](language-support.md#supported-markets-for-news-search-endpoint)

### <a name="top-news-items-by-category"></a>Principais notícias por categoria

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Devolve os principais itens noticiosos por categoria. Você pode solicitar especificamente os principais artigos `category=sports`de `category=entertainment`negócios, esportes ou entretenimento usando, `category=business`ou . O `category` parâmetro só pode ser `/news` utilizado com o URL. Existem alguns requisitos formais para especificar categorias; consulte `category` na documentação do parâmetro de [consulta.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) Envie uma consulta através de url codificando o`q=""` seu termo de pesquisa e acomodá-lo ao parâmetro. Para disponibilidade, consulte [países/regiões e mercados apoiados.](language-support.md#supported-markets-for-news-endpoint)

### <a name="trending-news-topics"></a>Tópicos de notícias de tendência 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Devolve tópicos noticiosos que estão atualmente a surgir nas redes sociais. Quando `/trendingtopics` a opção está incluída, a pesquisa bing `freshness` ignora vários outros parâmetros, tais como e `?q=""`. Para disponibilidade, consulte [países/regiões e mercados apoiados.](language-support.md#supported-markets-for-news-trending-endpoint)

## <a name="next-steps"></a>Passos seguintes

Para mais detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) do Bing News.

Para obter informações completas sobre os parâmetros suportados por cada ponto final, consulte as páginas de referência para cada tipo.
Por exemplo, de pedidos básicos utilizando a API de pesquisa de Notícias, consulte [Bing News Search Quick-starts](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
