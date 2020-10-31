---
title: O que é a API de Pesquisa de Vídeos do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba como procurar vídeos em toda a web, utilizando a API de Pesquisa de Vídeo Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 6f5da1d17722f89bee88a4b69177583e095eeac3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094290"
---
# <a name="what-is-the-bing-video-search-api"></a>O que é a API de Pesquisa de Vídeos do Bing?

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

A API de Pesquisa de Vídeo Bing facilita a adição de capacidades de pesquisa de vídeo aos seus serviços e aplicações. Ao enviar consultas de pesquisa de utilizadores com a API, pode obter e exibir vídeos relevantes e de alta qualidade semelhantes ao [Bing Video](https://www.bing.com/video). Utilize esta API para obter resultados de pesquisa que contenham apenas vídeos. A [API de Pesquisa Web Bing](../bing-web-search/search-the-web.md) pode devolver outros tipos de conteúdo web, incluindo páginas web, vídeos, notícias e imagens.

## <a name="bing-video-search-api-features"></a>Bing Video Search API

| Funcionalidade                                                                                                                                                                                 | Descrição                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir termos de pesquisa em tempo real](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Melhore a sua experiência de aplicação utilizando a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para apresentar os termos de pesquisa sugeridos, à medida que estão a ser escritos. |
| [Filtrar e restringir os resultados do vídeo](concepts/get-videos.md#filtering-videos)                      | Filtrar os vídeos devolvidos editando parâmetros de consulta.                                                                                                       |
| [Recortar, redimensionar e apresentar miniaturas](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Editar e exibir pré-visualizações de miniaturas para os vídeos devolvidos pela Bing Video Search API.                                                                                      |
| [Obter vídeos populares](trending-videos.md) | Procure vídeos de tendências de todo o mundo.                                                                                                          |
| [Obter informações de vídeo](video-insights.md) | Personalize uma pesquisa de vídeos de tendência de todo o mundo.                                                                                                          |

## <a name="workflow"></a>Fluxo de trabalho

A Bing Video Search API é um serviço web RESTful, facilitando a chamada a partir de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar o JSON. Pode utilizar o serviço através da [API REST](csharp.md) ou o [SDK](video-search-sdk-quickstart.md).

1. Crie uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuita.
2. Envie um pedido para a API com uma consulta de pesquisa válida.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.


## <a name="next-steps"></a>Passos seguintes

A [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) Bing Video Search API mostra como pode personalizar uma consulta de pesquisa e procurar vídeos na web.

Utilize o [quickstart](csharp.md) para começar rapidamente com o seu primeiro pedido de API.

## <a name="see-also"></a>Ver também

* A página de referência [da API v7 de pesquisa de vídeo Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) contém a lista de pontos finais, cabeçalhos e parâmetros de consulta utilizados para solicitar resultados de pesquisa.

* Os [Requisitos de apresentação e utilização do Bing](./useanddisplayrequirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.

* Visite a página do hub da [API de Pesquisa de Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.