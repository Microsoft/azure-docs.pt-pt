---
title: O que é a API de Pesquisa de Vídeos do Bing?
titlesuffix: Azure Cognitive Services
description: Saiba como procurar vídeos na web, com a API de pesquisa de vídeos do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 06/26/2019
ms.author: scottwhi
ms.openlocfilehash: 7b17c03b464ed7ccc13de2221c8e4cb36be05c3f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442700"
---
# <a name="what-is-the-bing-video-search-api"></a>O que é a API de Pesquisa de Vídeos do Bing?

A API de pesquisa de vídeos do Bing torna mais fácil adicionar capacidades de pesquisa de vídeo para seus serviços e aplicações. Através do envio de consultas de pesquisa de usuário com a API, pode obter e apresentar vídeos relevantes e de alta qualidade semelhantes [vídeos do Bing](https://www.bing.com/video). Utilize esta API para os resultados da pesquisa que contêm apenas a vídeos. O [API de pesquisa Web Bing](../bing-web-search/search-the-web.md) pode retornar a outros tipos de conteúdo da web, incluindo páginas da Web, vídeos, notícias e imagens.

## <a name="bing-video-search-api-features"></a>Funcionalidades de API de pesquisa de vídeos do Bing

| Funcionalidade                                                                                                                                                                                 | Descrição                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir termos de pesquisa em tempo real](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Melhore a sua experiência de aplicação utilizando a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para apresentar os termos de pesquisa sugeridos, à medida que estão a ser escritos. |
| [Filtrar e restringir os resultados de vídeo](concepts/get-videos.md#filtering-videos)                      | Filtre os vídeos devolvidos ao editar os parâmetros de consulta.                                                                                                       |
| [Recortar, redimensionar e apresentar miniaturas](resize-and-crop-thumbnails.md)                                                | Edição e exibição de visualizações de miniaturas para os vídeos devolvidos pela API de pesquisa de vídeos do Bing.                                                                                      |
| [Obtenha vídeos populares](trending-videos.md) | Procurar vídeos populares em todo o mundo.                                                                                                          |
| [Obtenha informações de vídeo](video-insights.md) | Personalize uma pesquisa de vídeos populares em todo o mundo.                                                                                                          |

## <a name="workflow"></a>Fluxo de trabalho

A API de pesquisa de vídeos do Bing é um serviço RESTful web, facilitando a chamar a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. Pode utilizar o serviço através da [API REST](csharp.md) ou o [SDK](video-search-sdk-quickstart.md).

1. Crie uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.
2. Envie um pedido para a API, com uma consulta de pesquisa válidos.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.


## <a name="next-steps"></a>Passos Seguintes

A API de pesquisa de vídeos do Bing [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) mostra como pode personalizar uma consulta de pesquisa e pesquisar na web para os vídeos.

Quando estiver pronto para chamar a API, crie uma [conta dos serviços cognitivos da API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.

Utilize o [guia de introdução](csharp.md) para começar rapidamente com o primeiro pedido de API.

## <a name="see-also"></a>Consulte também

* O [API de pesquisa de vídeos do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) página de referência contém a lista de pontos de extremidade, cabeçalhos e os parâmetros de consulta utilizados para pedir os resultados da pesquisa.

* Os [Requisitos de apresentação e utilização do Bing](./useanddisplayrequirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.