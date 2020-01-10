---
title: O que é a API de Pesquisa de Vídeos do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba como pesquisar vídeos na Web usando o API de Pesquisa de Vídeo do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 8377f0f5d586212c94bb763598b6e7a9e391073c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75382723"
---
# <a name="what-is-the-bing-video-search-api"></a>O que é a API de Pesquisa de Vídeos do Bing?

O API de Pesquisa de Vídeo do Bing facilita a adição de recursos de pesquisa de vídeo aos seus serviços e aplicativos. Ao enviar consultas de pesquisa de usuário com a API, você pode obter e exibir vídeos relevantes e de alta qualidade semelhantes ao [vídeo do Bing](https://www.bing.com/video). Use esta API para resultados de pesquisa que contenham apenas vídeos. O [API de pesquisa na Web do Bing](../bing-web-search/search-the-web.md) pode retornar outros tipos de conteúdo da Web, incluindo páginas da Web, vídeos, notícias e imagens.

## <a name="bing-video-search-api-features"></a>Recursos de API de Pesquisa de Vídeo do Bing

| Funcionalidade                                                                                                                                                                                 | Descrição                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir termos de pesquisa em tempo real](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Melhore a sua experiência de aplicação utilizando a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para apresentar os termos de pesquisa sugeridos, à medida que estão a ser escritos. |
| [Filtrar e restringir os resultados de vídeo](concepts/get-videos.md#filtering-videos)                      | Filtre os vídeos retornados pela edição de parâmetros de consulta.                                                                                                       |
| [Recortar, redimensionar e apresentar miniaturas](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Edite e exiba visualizações em miniatura para os vídeos retornados por API de Pesquisa de Vídeo do Bing.                                                                                      |
| [Obter vídeos de tendência](trending-videos.md) | Pesquise vídeos de tendências em todo o mundo.                                                                                                          |
| [Obter informações de vídeo](video-insights.md) | Personalize uma pesquisa de vídeos de tendências em todo o mundo.                                                                                                          |

## <a name="workflow"></a>Fluxo de Trabalho

O API de Pesquisa de Vídeo do Bing é um serviço Web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer solicitações HTTP e analisar o JSON. Pode utilizar o serviço através da [API REST](csharp.md) ou o [SDK](video-search-sdk-quickstart.md).

1. Crie uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.
2. Envie uma solicitação para a API, com uma consulta de pesquisa válida.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.


## <a name="next-steps"></a>Passos seguintes

A [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) de API de pesquisa de vídeo do Bing mostra como você pode personalizar uma consulta de pesquisa e pesquisar vídeos na Web.

Quando estiver pronto para chamar a API, crie uma [conta dos serviços cognitivos da API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.

Use o guia de [início rápido](csharp.md) para começar rapidamente com sua primeira solicitação de API.

## <a name="see-also"></a>Ver também

* A página de referência do [API de pesquisa de vídeo do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) contém a lista de pontos de extremidade, cabeçalhos e parâmetros de consulta usados para solicitar resultados da pesquisa.

* Os [Requisitos de apresentação e utilização do Bing](./useanddisplayrequirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.

* Visite a [página do hub de API pesquisa do Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.