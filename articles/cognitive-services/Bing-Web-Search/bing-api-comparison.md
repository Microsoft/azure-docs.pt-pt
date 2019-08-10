---
title: Quais são os APIs de Pesquisa do Bing?
titleSuffix: Azure Cognitive Services
description: Use este artigo para saber mais sobre o APIs de Pesquisa do Bing e como você pode habilitar pesquisas cognitivas da Internet em seus aplicativos e serviços.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 0e9a71e1e826569930cf593a7e264020617bdc3a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883399"
---
# <a name="what-are-the-bing-search-apis"></a>Quais são os APIs de Pesquisa do Bing?

O APIs de Pesquisa do Bing permite que você crie aplicativos e serviços conectados à Web que encontrem páginas, imagens, notícias, locais e muito mais sem anúncios. Ao enviar solicitações de pesquisa usando o Pesquisa do Bing SDKs ou APIs REST, você pode obter informações relevantes e conteúdo para pesquisas na Web. Use este artigo para saber mais sobre as diferentes APIs de pesquisa do Bing e como você pode integrar pesquisas cognitivas em seus aplicativos e serviços. Os preços e os limites de taxa podem variar entre as APIs.

## <a name="the-bing-web-search-api"></a>O API de Pesquisa na Web do Bing

O [API de pesquisa na Web do Bing](../Bing-Web-Search/index.yml) retorna páginas da Web, imagens, vídeo, notícias e muito mais. Você pode filtrar as consultas de pesquisa enviadas a essa API para incluir ou excluir determinados tipos de conteúdo.

Considere usar o API de Pesquisa na Web do Bing em aplicativos que talvez precisem Pesquisar todos os tipos de conteúdo da Web relevante. Se seu aplicativo procurar um tipo específico de conteúdo online, considere uma das APIs de pesquisa abaixo:

## <a name="content-specific-bing-search-apis"></a>APIs de pesquisa do Bing específicas do conteúdo

As seguintes APIs de pesquisa do Bing retornam conteúdo específico da Web como imagens, notícias, empresas locais e vídeos.

| API do Bing | Descrição |
| -- | -- |
| [Pesquisa de Entidade](../Bing-Entities-Search/index.yml) | O API de Pesquisa de Entidade do Bing retorna resultados de pesquisa que contêm entidades, que podem ser pessoas, lugares ou coisas. Dependendo da consulta, a API retornará uma ou mais entidades que satisfaçam a consulta de pesquisa. A consulta de pesquisa pode incluir indivíduos notáveis, empresas locais, pontos de referência, destinos e muito mais. |
| [Pesquisa de Imagem](../Bing-Image-Search/index.yml) | O API de Pesquisa de Imagem do Bing permite pesquisar e localizar imagens estáticas e animadas de alta qualidade semelhantes a [Bing.com/images](https://www.Bing.com/images). Você pode refinar as pesquisas para incluir ou excluir imagens por atributo, incluindo tamanho, cor, licença e atualização. Você também pode pesquisar imagens de tendências, carregar imagens para obter informações sobre elas e exibir visualizações em miniatura. |
| [Pesquisa de Notícias](../Bing-News-Search/index.yml) | O API de Pesquisa de Notícias do Bing permite que você encontre histórias de notícias semelhantes a [Bing.com/News](https://www.Bing.com/news). A API retorna artigos de notícias de várias fontes ou domínios específicos. Você pode pesquisar entre categorias para obter artigos de tendência, principais histórias e manchetes. |
| [Pesquisa de Vídeo](../Bing-Video-Search/index.yml) | O API de Pesquisa de Vídeo do Bing permite encontrar vídeos na Web. Obtenha vídeos de tendência, conteúdo relacionado e visualizações em miniatura. |
| [Pesquisa Visual](../Bing-visual-search/index.yml) | Carregue uma imagem ou use uma URL para obter informações mais criteriosas sobre ela, como produtos visualmente semelhantes, imagens e pesquisas relacionadas. |
 [Pesquisa de negócios local](../bing-local-business-search/index.yml) | A API de pesquisa de negócios local do Bing permite que seus aplicativos encontrem informações de contato e local sobre empresas locais com base em consultas de pesquisa. |

## <a name="the-bing-custom-search-api"></a>O API de Pesquisa Personalizada do Bing

Criar uma instância de pesquisa personalizada com a API de [pesquisa personalizada do Bing](../Bing-Custom-Search/index.yml) permite que você crie uma experiência de pesquisa focada apenas no conteúdo e nos tópicos sobre os quais você se preocupa. Por exemplo, depois de especificar os domínios, os sites e as páginas da Web específicas que o Bing pesquisará, Pesquisa Personalizada do Bing personalizará os resultados para esse conteúdo específico. Você pode incorporar as APIs de sugestão automática personalizada do Bing, imagem e Pesquisa de Vídeo para personalizar ainda mais sua experiência de pesquisa.

## <a name="additional-bing-search-apis"></a>APIs de Pesquisa do Bing adicionais

Os APIs de Pesquisa do Bing a seguir permitem melhorar sua experiência de pesquisa combinando-as com outras APIs de pesquisa do Bing.

| API | Descrição |
| -- | -- |
| [Sugestão Automática do Bing](../Bing-Autosuggest/index.yml) | Melhore a experiência de pesquisa do seu aplicativo com a API de Sugestão Automática do Bing retornando pesquisas sugeridas em tempo real.  |
| [Estatísticas do Bing](bing-web-stats.md) | As estatísticas do Bing fornecem análises para o APIs de Pesquisa do Bing que seu aplicativo usa. Algumas das análises disponíveis incluem volume de chamada, cadeias de caracteres de consulta principais e distribuição geográfica. |

## <a name="next-steps"></a>Passos Seguintes

* Detalhes de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) da API pesquisa do Bing
* Os [Requisitos de apresentação e utilização do Bing](./use-display-requirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.
