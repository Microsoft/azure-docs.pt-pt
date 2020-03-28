---
title: O que é a API de Pesquisa de Vídeos do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba como pesquisar vídeos através da web, utilizando a API bing video search.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 8377f0f5d586212c94bb763598b6e7a9e391073c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382723"
---
# <a name="what-is-the-bing-video-search-api"></a>O que é a API de Pesquisa de Vídeos do Bing?

A API bing video search facilita a adição de capacidades de pesquisa de vídeo aos seus serviços e aplicações. Ao enviar consultas de pesquisa de utilizadores com a API, pode obter e exibir vídeos relevantes e de alta qualidade semelhantes ao [Bing Video](https://www.bing.com/video). Utilize esta API para obter resultados de pesquisa que contenham apenas vídeos. O [Bing Web Search API](../bing-web-search/search-the-web.md) pode devolver outros tipos de conteúdos web, incluindo páginas web, vídeos, notícias e imagens.

## <a name="bing-video-search-api-features"></a>Recursos da API de pesquisa de vídeo bing

| Funcionalidade                                                                                                                                                                                 | Descrição                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir termos de pesquisa em tempo real](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Melhore a sua experiência de aplicação utilizando a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para apresentar os termos de pesquisa sugeridos, à medida que estão a ser escritos. |
| [Filtrar e restringir os resultados do vídeo](concepts/get-videos.md#filtering-videos)                      | Filtrar os vídeos devolvidos editando parâmetros de consulta.                                                                                                       |
| [Recortar, redimensionar e apresentar miniaturas](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Editar e exibir pré-visualizações de miniaturas para os vídeos devolvidos pela Bing Video Search API.                                                                                      |
| [Obter vídeos populares](trending-videos.md) | Procure por vídeos de tendência de todo o mundo.                                                                                                          |
| [Obter informações de vídeo](video-insights.md) | Personalize uma pesquisa por vídeos de tendência de todo o mundo.                                                                                                          |

## <a name="workflow"></a>Fluxo de trabalho

O Bing Video Search API é um serviço web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar a JSON. Pode utilizar o serviço através da [API REST](csharp.md) ou o [SDK](video-search-sdk-quickstart.md).

1. Crie uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.
2. Envie um pedido para a API com uma consulta de pesquisa válida.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.


## <a name="next-steps"></a>Passos seguintes

A [demo interativa](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) Bing Video Search API mostra como pode personalizar uma consulta de pesquisa e pesquisar na web por vídeos.

Quando estiver pronto para chamar a API, crie uma [conta dos serviços cognitivos da API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.

Use o [quickstart](csharp.md) para começar rapidamente com o seu primeiro pedido de API.

## <a name="see-also"></a>Consulte também

* A página de referência [Bing Video Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) contém a lista de pontos finais, cabeçalhos e parâmetros de consulta utilizados para solicitar resultados de pesquisa.

* Os [Requisitos de apresentação e utilização do Bing](./useanddisplayrequirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.

* Visite a página do centro da [Bing Search API](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.