---
title: O que é a API de Pesquisa Web Bing?
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa Web Bing é um serviço RESTful que fornece respostas instantâneas para consultas de pesquisa web. Configure os resultados para incluir páginas web, imagens, vídeos, notícias e muito mais. Os resultados são fornecidos como JSON e têm base na relevância de pesquisa e nas suas subscrições de Pesquisa na Web do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 7643486962df0516cc61857a7e31cf34bc41c732
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350641"
---
# <a name="what-is-the-bing-web-search-api"></a>O que é a API de Pesquisa Web Bing?

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

A API de Pesquisa na Web do Bing é um serviço RESTful que fornece respostas instantâneas às consultas do utilizador. Os resultados da pesquisa são facilmente configurados para incluir páginas da web, imagens, vídeos, notícias, traduções e muito mais. Bing Web Search fornece os resultados como JSON com base na relevância de pesquisa e nas suas subscrições de Pesquisa Web Bing.

Esta API é ideal para aplicações que precisam de acesso a todo o conteúdo que é relevante para uma consulta de pesquisa de um utilizador. Se estiver a criar uma aplicação que exige apenas um tipo específico de resultado, considere utilizar a [API de Pesquisa de Imagens do Bing](../Bing-Image-Search/overview.md), [API de Pesquisa de Vídeos do Bing](../bing-video-search/overview.md) ou [API de Pesquisa de Notícias do Bing](../Bing-News-Search/search-the-web.md). Consulte [APIs Serviços Cognitivos](../index.yml) para ver uma lista completa das APIs de Pesquisa do Bing.

Quer ver como funciona? Experimente a nossa [demonstração da API de Pesquisa na Web do Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Funcionalidades  

Bing Web Search não só lhe dá acesso a respostas instantâneas. Também fornece funcionalidades e funcionalidades adicionais que lhe permitem personalizar os resultados de pesquisa para os seus utilizadores.

| Funcionalidade | Descrição |
|---------|-------------|
| [Sugerir termos de pesquisa em tempo real](../bing-autosuggest/get-suggested-search-terms.md) | Melhore a sua experiência de aplicação utilizando a API de Sugestão Automática do Bing para apresentar os termos de pesquisa sugeridos, à medida que estão a ser escritos. |
| [Filtrar e restringir os resultados por tipo de conteúdo](filter-answers.md) | Personalize e refine os resultados de pesquisa com filtros e parâmetros de consulta para páginas da web, imagens, vídeos, pesquisa segura e muito mais. |
| [Detetor de ocorrências para carateres unicode](hit-highlighting.md) | Identifique e remova os carateres unicode indesejados dos resultados de pesquisa antes de os mostrar aos utilizadores com o detetor de ocorrências. |
| [Localizar os resultados de pesquisa por país, região e/ou mercado](./language-support.md) | A Pesquisa na Web do Bing suporta mais de três dúzias de países e regiões. Utilize esta funcionalidade para refinar os resultados de pesquisa para um/uma determinado país/região ou o mercado. |
| [Analisar as métricas de pesquisa com as Estatísticas do Bing](bing-web-stats.md) | Estatísticas do Bing é uma subscrição paga que fornece uma análise do volume de chamadas, das principais cadeias de consulta, da distribuição geográfica e muito mais. |

## <a name="workflow"></a>Fluxo de trabalho

A API de Pesquisa na Web do Bing é fácil de chamar a partir de qualquer linguagem que possa fazer pedidos e analisar as respostas JSON. O serviço é acessível utilizando a [API REST](quickstarts/python.md) ou as [bibliotecas de clientes Bing Web Search.](./quickstarts/client-libraries.md)

1. [Crie um recurso Azure](../cognitive-services-apis-create-account.md) para as APIs de pesquisa de Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/cognitive-services/).  
2. Envie um [pedido para a API de Pesquisa na Web do Bing](quickstarts/python.md).
3. Analise a resposta JSON.

## <a name="next-steps"></a>Passos seguintes

* Utilize o nosso [guia de introdução do Python](./quickstarts/client-libraries.md?pivots=programming-language-python) para efetuar a primeira chamada à API de Pesquisa na Web do Bing.  
* [Construa uma aplicação web de uma página](tutorial-bing-web-search-single-page-app.md)única.
* Reveja a documentação sobre [API de Pesquisa na Web v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).  
* Saiba mais sobre os [requisitos de utilização e apresentação](./use-display-requirements.md) da Pesquisa na Web do Bing.