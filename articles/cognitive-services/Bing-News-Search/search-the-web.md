---
title: O que é a API de Pesquisa de Notícias do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API de Pesquisa de Notícias do Bing para pesquisar manchetes atuais na Web entre categorias, incluindo tópicos de manchetes e tendências.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 06/19/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 577ce53b4667928d7eb5a870f57ff7180caaf6f5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423655"
---
# <a name="what-is-the-bing-news-search-api"></a>O que é a API de Pesquisa de Notícias do Bing?

O API de Pesquisa de Notícias do Bing facilita a integração dos recursos de pesquisa de notícias cognitivas do Bing em seus aplicativos. A API fornece uma experiência semelhante ao [Bing News](https://www.bing.com/news), permitindo que você envie consultas de pesquisa e receba artigos de notícias relevantes.

Lembre-se de que a API de Pesquisa de Notícias do Bing fornece apenas os resultados da pesquisa de notícias. Use o [API de pesquisa na Web do Bing](../bing-web-search/search-the-web.md), a [API do pesquisa de vídeo](../bing-video-search/search-the-web.md) e a API do [pesquisa de imagem](../bing-image-search/overview.md) para outros tipos de conteúdo da Web.

## <a name="bing-news-search-api-features"></a>Recursos de API de Pesquisa de Notícias do Bing

Embora o API de Pesquisa de Notícias do Bing encontre e retorne artigos de notícias relevantes, ele fornece vários recursos para recuperação de notícias inteligente e concentrada na Web.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugerindo e usando termos de pesquisa](concepts/search-for-news.md#suggest-and-use-search-terms)     | Melhore sua experiência de pesquisa usando o [API de sugestão automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para exibir os termos de pesquisa sugeridos à medida que eles são digitados.         |
|[Obtenha notícias gerais](concepts/search-for-news.md#get-general-news)     | Encontre notícias enviando uma consulta de pesquisa para a API de Pesquisa de Notícias do Bing e voltando uma lista de artigos de notícias relevantes.           |
|[Notícias principais de hoje](concepts/search-for-news.md#get-todays-top-news)      | Obtenha as principais histórias de notícias para o dia, em todas as categorias.       |
|[Notícias por categoria](concepts/search-for-news.md)     | Pesquise notícias em categorias específicas.        | 
|[Notícias de manchete](concepts/search-for-news.md)     | Pesquisar manchetes principais em todas as categorias.         |

## <a name="workflow"></a>Fluxo de trabalho

O API de Pesquisa de Notícias do Bing é um serviço Web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer solicitações HTTP e analisar o JSON. Você pode usar o serviço usando a API REST ou o SDK.

1. Crie uma conta de API de serviços cognitivas com acesso ao APIs de Pesquisa do Bing. Se você não tiver uma assinatura do Azure, poderá [criar uma conta gratuitamente](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api).

2. Envie uma solicitação para a API, com uma consulta de pesquisa válida.

3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos Seguintes

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) para o API de pesquisa de notícias do Bing. Esta demonstração mostra como você pode personalizar rapidamente uma consulta de pesquisa e encontrar notícias na Web.

Para começar rapidamente com sua primeira solicitação de API, experimente um guia de início rápido para a [API REST](quickstart.md) ou um dos [SDKs](sdk.md).

## <a name="see-also"></a>Consulte também

* A seção de referência do [API de pesquisa de notícias do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) contém definições e informações sobre os pontos de extremidade, cabeçalhos, respostas de API e parâmetros de consulta que você pode usar para solicitar resultados de pesquisa baseados em imagem.

* Os [Requisitos de apresentação e utilização do Bing](./useanddisplayrequirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.
