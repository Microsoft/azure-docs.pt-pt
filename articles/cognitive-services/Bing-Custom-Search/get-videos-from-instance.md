---
title: Obter vídeos do modo de exibição personalizado-Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Visão geral de alto nível sobre como usar Pesquisa Personalizada do Bing para obter vídeos de sua exibição personalizada da Web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 222256036a59c7df302546bbf82648c4d524d43f
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405089"
---
# <a name="get-videos-from-your-custom-view"></a>Obter vídeos de sua exibição personalizada

A pesquisa de vídeos personalizados do Bing permite enriquecer sua experiência de pesquisa personalizada com vídeos. À semelhança dos resultados da Web, a pesquisa personalizada suporta a pesquisa de vídeos na lista de sites da sua instância. Você pode obter os vídeos usando a API de pesquisa de vídeos personalizados do Bing ou o recurso de interface do usuário hospedada. O uso do recurso de interface do usuário hospedada é simples de usar e recomendado para que sua experiência de pesquisa fique em funcionamento em ordem curta. Para obter informações sobre como configurar sua interface do usuário hospedada para incluir vídeos, consulte [configurar sua experiência de interface do usuário hospedada](hosted-ui.md).

Se você quiser mais controle sobre a exibição dos resultados da pesquisa, poderá usar a API de pesquisa de vídeos personalizados do Bing. Como chamar a API é semelhante a chamar o API de Pesquisa de Vídeo do Bing, faça o check-out [pesquisa de vídeo do Bing](../Bing-Video-Search/search-the-web.md) para obter exemplos de como chamar a API. Mas antes de fazer isso, familiarize-se com o conteúdo de [referência da API de pesquisa de vídeos personalizados](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) . As principais diferenças são os parâmetros de consulta com suporte (você deve incluir o parâmetro de consulta configuração customconfig) e o ponto de extremidade para o qual você envia solicitações.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
