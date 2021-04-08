---
title: Obtenha imagens a partir da sua vista personalizada - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Visão geral de alto nível sobre a utilização de Bing Custom Search para obter imagens a partir da sua visão personalizada da Web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5025a68030f5dc3aec07d33af3f98370c8d64b87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338474"
---
# <a name="get-images-from-your-custom-view"></a>Obtenha imagens da sua vista personalizada

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Bing Custom Images Search permite-lhe enriquecer a sua experiência de pesquisa personalizada com imagens. À semelhança dos resultados da Web, a pesquisa personalizada suporta a pesquisa de imagens na lista de sites da sua instância. Pode obter as imagens utilizando a API de Pesquisa de Imagens Personalizadas de Bing ou através da funcionalidade UI hospedada. A utilização da funcionalidade de UI hospedada é simples de usar e recomendada para obter a sua experiência de pesquisa em funcionamento em curto prazo.  Para obter informações sobre a configuração da sua UI hospedada para incluir imagens, consulte [configurar a sua experiência de UI hospedada](hosted-ui.md).

Se quiser mais controlo sobre a exibição dos resultados da pesquisa, pode utilizar a API de Pesquisa de Imagens Personalizadas de Bing. Porque chamar a API é semelhante a chamar a API de Pesquisa de Imagem Bing, checkout [Bing Image Search](../Bing-Image-Search/overview.md) por exemplos chamando a API. Mas antes de o fazer, familiarize-se com o conteúdo de referência da [API de pesquisa de imagens personalizadas.](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) As principais diferenças são os parâmetros de consulta suportados (deve incluir o parâmetro de consulta personalizadaConfig) e o ponto final para o quais envia pedidos.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->