---
title: O que é o API de Pesquisa na Web do Bing?
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa na Web do Bing é um serviço RESTful que fornece respostas instantâneas às consultas do utilizador. Os resultados da pesquisa são facilmente configurados para incluir páginas da web, imagens, vídeos, notícias, traduções e muito mais. Os resultados são fornecidos como JSON e têm base na relevância de pesquisa e nas suas subscrições de Pesquisa na Web do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 12/05/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 6615f166ced4a93115f9cbaae1c6ce5b57cf57bc
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892996"
---
# <a name="what-is-the-bing-web-search-api"></a>O que é o API de Pesquisa na Web do Bing?

A API de Pesquisa na Web do Bing é um serviço RESTful que fornece respostas instantâneas às consultas do utilizador. Os resultados da pesquisa são facilmente configurados para incluir páginas da web, imagens, vídeos, notícias, traduções e muito mais. Pesquisa na Web do Bing fornece os resultados como JSON com base na relevância de pesquisa e em suas assinaturas de Pesquisa na Web do Bing.

Esta API é ideal para aplicações que precisam de acesso a todo o conteúdo que é relevante para uma consulta de pesquisa de um utilizador. Se estiver a criar uma aplicação que exige apenas um tipo específico de resultado, considere utilizar a [API de Pesquisa de Imagens do Bing](../Bing-Image-Search/overview.md), [API de Pesquisa de Vídeos do Bing](../Bing-Video-Search/search-the-web.md) ou [API de Pesquisa de Notícias do Bing](../Bing-News-Search/search-the-web.md). Consulte [APIs Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services) para ver uma lista completa das APIs de Pesquisa do Bing.

Quer ver como funciona? Experimente a nossa [demonstração da API de Pesquisa na Web do Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Funcionalidades  

Pesquisa na Web do Bing não só dá acesso a respostas instantâneas. Ele também fornece recursos adicionais e funcionalidade que permitem que você personalize os resultados da pesquisa para seus usuários.

| Funcionalidade | Descrição |
|---------|-------------|
| [Sugerir termos de pesquisa em tempo real](../bing-autosuggest/get-suggested-search-terms.md) | Melhore a sua experiência de aplicação utilizando a API de Sugestão Automática do Bing para apresentar os termos de pesquisa sugeridos, à medida que estão a ser escritos. |
| [Filtrar e restringir os resultados por tipo de conteúdo](filter-answers.md) | Personalize e refine os resultados de pesquisa com filtros e parâmetros de consulta para páginas da web, imagens, vídeos, pesquisa segura e muito mais. |
| [Detetor de ocorrências para carateres unicode](hit-highlighting.md) | Identifique e remova os carateres unicode indesejados dos resultados de pesquisa antes de os mostrar aos utilizadores com o detetor de ocorrências. |
| [Localizar os resultados de pesquisa por país, região e/ou mercado](supported-countries-markets.md) | A Pesquisa na Web do Bing suporta mais de três dúzias de países e regiões. Utilize esta funcionalidade para refinar os resultados de pesquisa para um/uma determinado país/região ou o mercado. |
| [Analisar as métricas de pesquisa com as Estatísticas do Bing](bing-web-stats.md) | Estatísticas do Bing é uma subscrição paga que fornece uma análise do volume de chamadas, das principais cadeias de consulta, da distribuição geográfica e muito mais. |

## <a name="workflow"></a>Fluxo de Trabalho

A API de Pesquisa na Web do Bing é fácil de chamar a partir de qualquer linguagem que possa fazer pedidos e analisar as respostas JSON. O serviço está acessível através da [API REST](quickstarts/python.md) ou dos [SDKs da Pesquisa na Web do Bing](web-sdk-python-quickstart.md).

1. [Crie um recurso do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para o APIs de pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).  
2. Envie um [pedido para a API de Pesquisa na Web do Bing](quickstarts/python.md).
3. Analise a resposta JSON.

## <a name="next-steps"></a>Passos seguintes

* Utilize o nosso [guia de introdução do Python](quickstarts/python.md) para efetuar a primeira chamada à API de Pesquisa na Web do Bing.  
* [Crie uma aplicação Web de página única](tutorial-bing-web-search-single-page-app.md).
* Reveja a documentação sobre [API de Pesquisa na Web v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).  
* Saiba mais sobre os [requisitos de utilização e apresentação](UseAndDisplayRequirements.md) da Pesquisa na Web do Bing.  
