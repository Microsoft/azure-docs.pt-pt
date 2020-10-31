---
title: Obtenha vídeos a partir da sua vista personalizada - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Visão geral de alto nível sobre a utilização de Bing Custom Search para obter vídeos a partir da sua visão personalizada da Web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 7dbd9f609944fc63c186ca150d5b9921f3e86622
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090584"
---
# <a name="get-videos-from-your-custom-view"></a>Obtenha vídeos a partir da sua vista personalizada

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Bing Vídeos Personalizados A pesquisa permite-lhe enriquecer a sua experiência de pesquisa personalizada com vídeos. À semelhança dos resultados da Web, a pesquisa personalizada suporta a pesquisa de vídeos na lista de sites da sua instância. Pode obter os vídeos utilizando a API de Pesquisa de Vídeos Personalizados de Bing ou através da funcionalidade UI hospedada. A utilização da funcionalidade de UI hospedada é simples de usar e recomendada para obter a sua experiência de pesquisa em funcionamento em curto prazo. Para obter informações sobre a configuração da sua UI hospedada para incluir vídeos, consulte [configurar a sua experiência de UI hospedada](hosted-ui.md).

Se quiser mais controlo sobre a exibição dos resultados da pesquisa, pode utilizar a API de Pesquisa de Vídeos Personalizados do Bing. Porque ligar para a API é semelhante a chamar a API de Pesquisa de Vídeo Bing, checkout [Bing Video Search](../Bing-Video-Search/search-the-web.md) por exemplos chamando a API. Mas antes de o fazer, familiarize-se com o conteúdo de referência de [pesquisa de vídeos personalizados.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) As principais diferenças são os parâmetros de consulta suportados (deve incluir o parâmetro de consulta personalizadaConfig) e o ponto final para o quais envia pedidos.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
