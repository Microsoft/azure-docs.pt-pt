---
title: O que é a API de Pesquisa de Notícias do Bing?
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar a API de pesquisa de notícias do Bing para pesquisar a web para manchetes atuais em categorias, incluindo manchetes e tendências de tópicos.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 06/19/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: d4d8c35869fbfc13220aba037a97aadd3cea01c2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272708"
---
# <a name="what-is-the-bing-news-search-api"></a>O que é a API de Pesquisa de Notícias do Bing?

A API de pesquisa de notícias do Bing torna mais fácil de integrar as capacidades de pesquisa do Bing notícias cognitivos aos seus aplicativos. A API fornece uma experiência semelhante à [Bing notícias](https://www.bing.com/news), permitindo-lhe enviar consultas de pesquisa e receber artigos de notícias relevantes.

Lembre-se de que a API de pesquisa de notícias do Bing fornece apenas resultados de pesquisa de notícias. Utilize o [API de pesquisa Web Bing](../bing-web-search/search-the-web.md), [API de pesquisa de vídeos](../bing-video-search/search-the-web.md) e [API de pesquisa de imagens](../bing-image-search/overview.md) para outros tipos de conteúdo web.

## <a name="bing-news-search-api-features"></a>Funcionalidades de API de pesquisa de notícias do Bing

Embora a API de pesquisa de notícias do Bing principalmente localiza e devolve os artigos de notícias relevantes, fornece vários recursos para obtenção de notícias inteligentes e focada na web.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugerindo e utilizar os termos de pesquisa](concepts/search-for-news.md#suggest-and-use-search-terms)     | Melhorar a sua experiência de pesquisa, utilizando o [a API de sugestão automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para apresentar os termos de pesquisa sugerida, à medida que estão a ser digitados.         |
|[Receba notícias gerais](concepts/search-for-news.md#get-general-news)     | Encontre notícias ao enviar uma consulta de pesquisa para a API de pesquisa de notícias do Bing e voltando uma lista de artigos de notícias relevantes.           |
|[Notícias de principais de hoje](concepts/search-for-news.md#get-todays-top-news)      | Obtenha as histórias de notícias superior para o dia, em todas as categorias.       |
|[Notícias por categoria](concepts/search-for-news.md)     | Pesquisar notícias em categorias específicas.        | 
|[Manchetes](concepts/search-for-news.md)     | Pesquisar manchetes superior em todas as categorias.         |

## <a name="workflow"></a>Fluxo de trabalho

A API de pesquisa de notícias do Bing é um serviço RESTful web, facilitando a chamar a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. Pode utilizar o serviço usando a API REST ou o SDK.

1. Crie uma conta de API dos serviços cognitivos com acesso às APIs de pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api).

2. Envie um pedido para a API, com uma consulta de pesquisa válidos.

3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos Seguintes

Em primeiro lugar, tente o [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) para a API de pesquisa de notícias do Bing. Esta demonstração mostra como pode rapidamente personalizar uma consulta de pesquisa e encontra notícias na web.

Para começar rapidamente com o primeiro pedido de API, experimente um início rápido para o [REST API](quickstart.md) ou uma da [SDKs](sdk.md).

## <a name="see-also"></a>Consulte também

* O [API de pesquisa de notícias do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) secção de referência contém definições e informações sobre a pontos de extremidade, cabeçalhos, as respostas de API e parâmetros de consulta que pode utilizar para solicitar os resultados da pesquisa baseada em imagem.

* Os [Requisitos de apresentação e utilização do Bing](./useanddisplayrequirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.
