---
title: O que é a API de Pesquisa de Notícias do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API de Pesquisa de Notícias Bing para pesquisar na web manchetes atuais entre categorias, incluindo manchetes e tópicos de tendência.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: c6fad3a006d2f3da74638e0680d6ba65f736ab7b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351236"
---
# <a name="what-is-the-bing-news-search-api"></a>O que é a API de Pesquisa de Notícias do Bing?

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A API de Pesquisa de Notícias Bing facilita a integração das capacidades de pesquisa de notícias cognitivas de Bing nas suas aplicações. A API proporciona uma experiência semelhante à [Bing News,](https://www.bing.com/news)permitindo-lhe enviar consultas de pesquisa e receber artigos de notícias relevantes.

Esteja ciente de que a API de Pesquisa de Notícias Bing fornece apenas resultados de pesquisa de notícias. Utilize a [API de Pesquisa Web Bing,](../bing-web-search/overview.md) [API de Pesquisa de Vídeo](../bing-video-search/overview.md) e [API de Pesquisa de Imagem](../bing-image-search/overview.md) para outros tipos de conteúdo web.

## <a name="bing-news-search-api-features"></a>Bing News Search API

Embora a API de Pesquisa de Notícias Bing encontre e devolva artigos de notícias relevantes, fornece várias funcionalidades para a recuperação de notícias inteligentes e focadas na web.

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugerir e usar termos de pesquisa](concepts/search-for-news.md#suggest-and-use-search-terms)     | Melhore a sua experiência de pesquisa utilizando [a API Bing Autosuggest](../bing-autosuggest/get-suggested-search-terms.md) para exibir termos de pesquisa sugeridos à medida que são dactilografados.         |
|[Receba notícias gerais](concepts/search-for-news.md#get-general-news)     | Encontre notícias enviando uma consulta de pesquisa para a API de Pesquisa de Notícias Bing e recebendo uma lista de artigos de notícias relevantes.           |
|[Notícias principais de hoje](concepts/search-for-news.md#get-todays-top-news)      | Receba as principais notícias do dia, em todas as categorias.       |
|[Notícias por categoria](concepts/search-for-news.md)     | Procure notícias em categorias específicas.        | 
|[Notícias de manchete](concepts/search-for-news.md)     | Procure manchetes de topo em todas as categorias.         |

## <a name="workflow"></a>Fluxo de trabalho

A Bing News Search API é um serviço web RESTful, facilitando a chamada a partir de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar o JSON. Pode utilizar o serviço através da API REST ou o SDK.

1. Crie uma [conta de API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuita.
2. Envie um pedido para a API com uma consulta de pesquisa válida.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos seguintes

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) para a API de Pesquisa de Notícias Bing. Esta demonstração mostra como você pode personalizar rapidamente uma consulta de pesquisa e encontrar notícias na web.

Para começar rapidamente com o seu primeiro pedido de API, experimente um quickstart para a [REST API](./csharp.md) ou um dos [SDKs.](./quickstarts/client-libraries.md?pivots=programming-language-csharp)

## <a name="see-also"></a>Ver também

* A secção de referência [de API v7 de pesquisa de notícias Bing News](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) contém definições e informações sobre os pontos finais, cabeçalhos, respostas API e parâmetros de consulta que pode utilizar para solicitar resultados de pesquisa baseados na imagem.
* Os [Requisitos de apresentação e utilização do Bing](../bing-web-search/use-display-requirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.
* Visite a página do hub da [API de Pesquisa de Bing](../bing-web-search/overview.md) para explorar as outras APIs disponíveis.