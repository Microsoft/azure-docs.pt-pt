---
title: O que são as APIs de Pesquisa bing?
titleSuffix: Azure Cognitive Services
description: Use este artigo para saber sobre as APIs de Pesquisa de Bing e como pode permitir pesquisas cognitivas na Internet nas suas apps e serviços.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 82b1f13562a49284059c25bcbd39a33daf949dcc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74775545"
---
# <a name="what-are-the-bing-search-apis"></a>O que são as APIs de Pesquisa bing?

As APIs de Pesquisa do Bing permitem-lhe criar aplicações e serviços ligados à Web capazes de encontrar páginas Web, imagens, notícias, localizações e muito mais sem anúncios. Ao enviar pedidos de pesquisa utilizando as APIs ou SDKs de Pesquisa bing, pode obter informações e conteúdos relevantes para pesquisas web. Use este artigo para saber sobre as diferentes APIs de pesquisa de Bing e como pode integrar pesquisas cognitivas nas suas aplicações e serviços. Os preços e os limites de taxas podem variar entre APIs.

## <a name="the-bing-web-search-api"></a>A API de Pesquisa web bing

A [API bing Web Search](../Bing-Web-Search/overview.md) retorna páginas web, imagens, vídeo, notícias e muito mais. Pode filtrar as consultas de pesquisa enviadas para esta API para incluir ou excluir certos tipos de conteúdo.

Considere utilizar a API bing Web Search em aplicações que possam precisar de procurar todos os tipos de conteúdo web relevantes. Se a sua aplicação procurar um tipo específico de conteúdo online, considere uma das APIs de pesquisa abaixo:

## <a name="content-specific-bing-search-apis"></a>APIs de pesquisa de Bing específicos do conteúdo

As APIs de pesquisa de Bing retornam conteúdo específico da web como imagens, notícias, empresas locais e vídeos.

| Bing API | Descrição |
| -- | -- |
| [Pesquisa de Entidades](../Bing-Entities-Search/overview.md) | A API de Pesquisa de Entidades Bing devolve resultados de pesquisa contendo entidades, que podem ser pessoas, lugares ou coisas. Dependendo da consulta, a API devolverá uma ou mais entidades que satisfaçam a consulta de pesquisa. A consulta de pesquisa pode incluir indivíduos notáveis, empresas locais, marcos históricos, destinos, e muito mais. |
| [Pesquisa de Imagens](../Bing-Image-Search/overview.md) | A API de Pesquisa de Imagem Bing permite-lhe pesquisar e encontrar imagens estáticas e animadas de alta qualidade semelhantes a [Bing.com/images](https://www.Bing.com/images). Pode refinar pesquisas para incluir ou excluir imagens por atributo, incluindo tamanho, cor, licença e frescura. Também pode pesquisar imagens de tendência, carregar imagens para obter informações sobre elas e apresentar pré-visualizações de miniaturas. |
| [Pesquisa de Notícias](../Bing-News-Search/search-the-web.md) | A API de Pesquisa de Notícias Bing permite encontrar notícias semelhantes a [Bing.com/news](https://www.Bing.com/news). A API devolve artigos noticiosos de múltiplas fontes ou domínios específicos. Você pode pesquisar em todas as categorias para obter artigos de tendência, histórias de topo e manchetes. |
| [Pesquisa de Vídeos](../Bing-Video-Search/overview.md) | A API de Pesquisa de Vídeo Bing permite-lhe encontrar vídeos através da web. Obtenha vídeos de tendência, conteúdo relacionado e pré-visualizações de miniaturas. |
| [Pesquisa Visual](../Bing-visual-search/overview.md) | Faça upload de uma imagem ou use um URL para obter informações perspicazes sobre a mesma, como produtos, imagens e pesquisas relacionadas visualmente. |
 [Pesquisa de Negócios Locais](../bing-local-business-search/overview.md) | A API de Pesquisa de Negócios Local Bing permite que as suas aplicações encontrem informações de contacto e localização sobre empresas locais com base em consultas de pesquisa. |

## <a name="the-bing-custom-search-api"></a>A API de Pesquisa Personalizada bing

Criar uma instância de pesquisa personalizada com a [API](../Bing-Custom-Search/overview.md) de pesquisa personalizada do Bing permite-lhe criar uma experiência de pesquisa focada apenas em conteúdos e tópicos com os seus interesses. Por exemplo, depois de especificar os domínios, websites e páginas web específicas que bing irá pesquisar, bing Custom Search irá adaptar os resultados a esse conteúdo específico. Pode incorporar as APIs de Pesquisa Personalizada si, Imagem e Pesquisa de Vídeo bing custom para personalizar ainda mais a sua experiência de pesquisa.

## <a name="additional-bing-search-apis"></a>APIs de pesquisa de bing adicional

As seguintes APIs de Pesquisa bing permitem-lhe melhorar a sua experiência de pesquisa combinando-as com outras APIs de pesquisa bing.

| API | Descrição |
| -- | -- |
| [Sugestão Automática do Bing](../Bing-Autosuggest/get-suggested-search-terms.md) | Melhore a experiência de pesquisa da sua aplicação com a API Bing Autosuggest, devolvendo pesquisas sugeridas em tempo real.  |
| [Estatísticas bing](bing-web-stats.md) | Bing Statistics fornece análisepara as APIs de pesquisa de bing que a sua aplicação utiliza. Algumas das análises disponíveis incluem volume de chamadas, cordas de consulta superior e distribuição geográfica. |

## <a name="next-steps"></a>Passos seguintes

* Detalhes de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) da API de pesquisa de bing
* Os [Requisitos de apresentação e utilização do Bing](./use-display-requirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.
