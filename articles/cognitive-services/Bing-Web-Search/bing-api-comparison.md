---
title: Quais são as APIs de Pesquisa de Bing?
titleSuffix: Azure Cognitive Services
description: Use este artigo para aprender sobre as APIs de Pesquisa de Bing e como pode ativar pesquisas cognitivas na Internet nas suas apps e serviços.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 72b4755c6f01a10851e79cf274842f1599bc2c55
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349542"
---
# <a name="what-are-the-bing-search-apis"></a>Quais são as APIs de Pesquisa de Bing?

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

As APIs de Pesquisa do Bing permitem-lhe criar aplicações e serviços ligados à Web capazes de encontrar páginas Web, imagens, notícias, localizações e muito mais sem anúncios. Ao enviar pedidos de pesquisa utilizando as APIs ou SDKs de pesquisa de Bing Search, pode obter informações e conteúdos relevantes para pesquisas na Web. Use este artigo para aprender sobre as diferentes APIs de pesquisa de Bing e como você pode integrar pesquisas cognitivas nas suas aplicações e serviços. Os limites de preços e de tarifas podem variar entre as APIs.

## <a name="the-bing-web-search-api"></a>A API de Pesquisa Web Bing

A [API de Pesquisa Web Bing](../Bing-Web-Search/overview.md) retorna páginas web, imagens, vídeo, notícias e muito mais. Pode filtrar as consultas de pesquisa enviadas a esta API para incluir ou excluir certos tipos de conteúdo.

Considere usar a API de Pesquisa Web Bing em aplicações que possam necessitar de procurar por todos os tipos de conteúdo web relevante. Se a sua aplicação procurar um tipo específico de conteúdo online, considere uma das APIs de pesquisa abaixo:

## <a name="content-specific-bing-search-apis"></a>APIs de pesquisa de Bing específicas de conteúdo

As APIs de pesquisa bing seguintes devolvem conteúdo específico da web como imagens, notícias, negócios locais e vídeos.

| Bing API | Descrição |
| -- | -- |
| [Pesquisa de entidades](../Bing-Entities-Search/overview.md) | A API de Pesquisa de Entidade Bing devolve os resultados da pesquisa que contêm entidades, que podem ser pessoas, locais ou coisas. Dependendo da consulta, a API devolverá uma ou mais entidades que satisfaçam a consulta de pesquisa. A consulta de pesquisa pode incluir indivíduos notáveis, empresas locais, marcos, destinos e muito mais. |
| [Pesquisa de Imagens](../Bing-Image-Search/overview.md) | A API de Pesquisa de Imagem Bing permite-lhe procurar e encontrar imagens estáticas e animadas de alta qualidade semelhantes a [Bing.com/images](https://www.Bing.com/images). Pode refinar pesquisas para incluir ou excluir imagens por atributo, incluindo tamanho, cor, licença e frescura. Também pode pesquisar imagens de tendência, fazer upload de imagens para obter insights sobre elas e exibir pré-visualizações de miniaturas. |
| [Pesquisa de Notícias](../Bing-News-Search/search-the-web.md) | A API de Pesquisa de Notícias Bing permite-lhe encontrar notícias semelhantes a [Bing.com/news](https://www.Bing.com/news). A API devolve artigos noticiosos de várias fontes ou domínios específicos. Você pode pesquisar através de categorias para obter artigos de tendência, histórias de topo e manchetes. |
| [Pesquisa de Vídeos](../Bing-Video-Search/overview.md) | A API de Pesquisa de Vídeo Bing permite encontrar vídeos em toda a web. Obtenha vídeos de tendência, conteúdo relacionado e pré-visualizações de miniaturas. |
| [Pesquisa Visual](../Bing-visual-search/overview.md) | Faça upload de uma imagem ou use um URL para obter informações perspicazes sobre o mesmo, como produtos, imagens e pesquisas relacionadas visualmente similares. |
 [Pesquisa de negócios locais](../bing-local-business-search/overview.md) | A API de Pesquisa de Negócios Locais de Bing permite que as suas aplicações encontrem informações de contacto e localização sobre empresas locais com base em consultas de pesquisa. |

## <a name="the-bing-custom-search-api"></a>A API de Pesquisa Personalizada Bing

Criar um caso de pesquisa personalizado com a [API de Pesquisa Personalizada Bing](../Bing-Custom-Search/overview.md) permite-lhe criar uma experiência de pesquisa focada apenas em conteúdos e tópicos que lhe interessam. Por exemplo, depois de especificar os domínios, websites e páginas web específicas que o Bing irá pesquisar, a Bing Custom Search adaptará os resultados a esse conteúdo específico. Pode incorporar as APIs de pesquisa automática, imagem e pesquisa de vídeo bing para personalizar ainda mais a sua experiência de pesquisa.

## <a name="additional-bing-search-apis"></a>APIs adicionais de pesquisa de Bing

As APIs de pesquisa de Bing seguintes permitem-lhe melhorar a sua experiência de pesquisa, combinando-as com outras APIs de pesquisa de Bing.

| API | Descrição |
| -- | -- |
| [Sugestão Automática do Bing](../Bing-Autosuggest/get-suggested-search-terms.md) | Melhore a experiência de pesquisa da sua aplicação com a Bing Autosuggest API, devolvendo as pesquisas sugeridas em tempo real.  |
| [Estatísticas Bing](bing-web-stats.md) | Bing Statistics fornece análise para as APIs de pesquisa de Bing que a sua aplicação utiliza. Algumas das análises disponíveis incluem volume de chamadas, cadeias de consulta superior e distribuição geográfica. |

## <a name="next-steps"></a>Passos seguintes

* Bing Search Detalhes [de preços da](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) API
* Os [Requisitos de apresentação e utilização do Bing](./use-display-requirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.