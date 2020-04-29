---
title: O que é a API de Pesquisa de Notícias do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API bing news search para pesquisar na web para as manchetes atuais em todas as categorias, incluindo manchetes e tópicos de tendência.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: e0e99c7f677173c64afad3109b2f4accd7cb3cb9
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75448451"
---
# <a name="what-is-the-bing-news-search-api"></a>O que é a API de Pesquisa de Notícias do Bing?

A API de Pesquisa de Notícias Bing facilita a integração das capacidades cognitivas de pesquisa de notícias do Bing nas suas aplicações. A API proporciona uma experiência semelhante à [Bing News,](https://www.bing.com/news)permitindo-lhe enviar consultas de pesquisa e receber artigos de notícias relevantes.

Esteja ciente de que a API de Pesquisa de Notícias bing fornece apenas resultados de pesquisa de notícias. Utilize a API de [Pesquisa web bing,](../bing-web-search/search-the-web.md) [API](../bing-video-search/search-the-web.md) de pesquisa de vídeo e [API](../bing-image-search/overview.md) de pesquisa de imagem para outros tipos de conteúdo web.

## <a name="bing-news-search-api-features"></a>Funcionalidades da API de Pesquisa de Notícias bing

Enquanto a API de Pesquisa de Notícias bing encontra e devolve principalmente artigos de notícias relevantes, fornece várias funcionalidades para a recuperação inteligente e focada de notícias na web.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugerindo e usando termos de pesquisa](concepts/search-for-news.md#suggest-and-use-search-terms)     | Melhore a sua experiência de pesquisa utilizando a [API Bing Autosuggest](../bing-autosuggest/get-suggested-search-terms.md) para apresentar os termos de pesquisa sugeridos à medida que são dactilografados.         |
|[Receber notícias gerais](concepts/search-for-news.md#get-general-news)     | Encontre novidades enviando uma consulta de pesquisa para a API de Pesquisa de Notícias bing, e recebendo de volta uma lista de artigos de notícias relevantes.           |
|[As principais notícias de hoje](concepts/search-for-news.md#get-todays-top-news)      | Obtenha as principais notícias do dia, em todas as categorias.       |
|[Notícias por categoria](concepts/search-for-news.md)     | Procure notícias em categorias específicas.        | 
|[Notícias de manchete](concepts/search-for-news.md)     | Procure manchetes de topo em todas as categorias.         |

## <a name="workflow"></a>Fluxo de trabalho

O Bing News Search API é um serviço web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar a JSON. Pode utilizar o serviço através da API REST ou o SDK.

1. Crie uma conta de API dos Serviços Cognitivos com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição Azure, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api)

2. Envie um pedido para a API com uma consulta de pesquisa válida.

3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos seguintes

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) para a API de Pesquisa de Notícias bing. Esta demonstração mostra como pode personalizar rapidamente uma consulta de pesquisa e encontrar novidades na web.

Para começar rapidamente com o seu primeiro pedido de API, tente um arranque rápido para a [Rest API](quickstart.md) ou um dos [SDKs](sdk.md).

## <a name="see-also"></a>Consulte também

* A secção de referência [Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) contém definições e informações sobre os pontos finais, cabeçalhos, respostas API e parâmetros de consulta que pode utilizar para solicitar resultados de pesquisa baseados em imagem.
* Os [Requisitos de apresentação e utilização do Bing](./useanddisplayrequirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.
* Visite a página do centro da [Bing Search API](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.