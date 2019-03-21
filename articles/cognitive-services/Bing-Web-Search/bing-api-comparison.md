---
title: Quais são as APIs de pesquisa do Bing?
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para saber mais sobre as APIs de pesquisa do Bing e como pode permitir cognitivos pesquisas da internet nas suas aplicações e serviços.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 5a883fcb3533374afbbf946281b6a4a1e9a2912e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903118"
---
# <a name="what-are-the-bing-search-apis"></a>Quais são as APIs de pesquisa do Bing?

As APIs de pesquisa do Bing permitem-lhe criar aplicações web ligadas ao e serviços que encontrar páginas da Web, imagens, notícias, localizações e muito mais sem anúncios. Ao enviar pedidos de pesquisa com os SDKs ou APIs de REST de pesquisa do Bing, pode obter informações relevantes e conteúdo para as pesquisas da web. Utilize este artigo para saber mais sobre a diferente APIs de pesquisa do Bing e como integrar o cognitivas pesquisas em seus aplicativos e serviços. Limites de taxa e preços podem variar entre as APIs.

## <a name="the-bing-web-search-api"></a>A API de pesquisa Web Bing

O [API de pesquisa Web Bing](../Bing-Web-Search/index.yml) retorna páginas da Web, imagens, vídeo, notícias e muito mais. Pode filtrar as consultas de pesquisa enviadas para esta API para incluir ou excluir determinados tipos de conteúdo.

Considere utilizar a API de pesquisa Web Bing em aplicações que poderão ter de procurar todos os tipos de conteúdo web relevantes. Se o aplicativo procura um tipo específico de conteúdo online, considere um da pesquisa APIs abaixo:

## <a name="content-specific-bing-search-apis"></a>APIs de pesquisa do Bing do específico de conteúdo

A APIs de pesquisa do Bing seguinte retornar conteúdo específico da web, como imagens, notícias, negócios locais e vídeos.

| API do Bing | Descrição |
| -- | -- |
| [Pesquisa de entidades](../Bing-Entities-Search/index.yml) | A API de pesquisa de entidades do Bing devolve resultados de pesquisa que contém entidades, que podem ser pessoas, lugares ou coisas. Dependendo da consulta, a API irá devolver uma ou mais entidades que satisfazem a consulta de pesquisa. A consulta de pesquisa pode incluir indivíduos digno de nota, negócios locais, pontos de referência, destinos e muito mais. |
| [Pesquisa de imagens](../Bing-Image-Search/index.yml) | A API de pesquisa de imagens do Bing permite-lhe procurar e localizar alta qualidade estática e animados imagens semelhante [Bing.com/images](https://www.Bing.com/images). Pode refinar pesquisas para incluir ou excluir imagens pelo atributo, incluindo o tamanho, a cor, licença e atualização. Também pode procurar imagens populares, carregar imagens para obter informações sobre eles e apresentar visualizações de miniaturas. |
| [Pesquisa de notícias](../Bing-News-Search/index.yml) | A API de pesquisa de notícias do Bing permite que encontre histórias de notícias semelhante [Bing.com/news](https://www.Bing.com/news). A API devolve artigos de notícias de várias origens ou a domínios específicos. Pode pesquisar em categorias para obter tendências artigos, principais histórias e manchetes. |
| [Pesquisa de vídeos](../Bing-Video-Search/index.yml) | A API de pesquisa de vídeos do Bing permite que encontre vídeos na web. Obtenha vídeos populares, conteúdo relacionado e visualizações de miniaturas. |
| [Pesquisa Visual](../Bing-visual-search/index.yml) | Carregue uma imagem ou utilize um URL para obter informações criteriosas sobre ele, como pesquisas relacionadas, imagens e produtos visualmente semelhantes. |
 [Pesquisa de comercial](../bing-local-business-search/index.yml) | A API de pesquisa de negócios locais do Bing permite que seus aplicativos encontrar informações de contacto e localização sobre negócios locais com base nas consultas de pesquisa. |

## <a name="the-bing-custom-search-api"></a>A API de pesquisa personalizada do Bing

Criar uma instância de pesquisa personalizada com o [pesquisa personalizada do Bing](../Bing-Custom-Search/index.yml) API permite-lhe criar uma experiência de pesquisa concentra-se apenas no conteúdo e os tópicos que mais lhe interessa. Por exemplo, depois de especificar os domínios, sites e páginas Web específica que serão de pesquisa do Bing, pesquisa personalizada do Bing irá adaptar os resultados para esse conteúdo específico. Pode incorporar o Custom sugestão automática do Bing, imagem, e a experiência de APIs de pesquisa de vídeo para personalizar ainda mais a sua pesquisa.

## <a name="additional-bing-search-apis"></a>APIs de pesquisa Bing adicionais

As APIs de pesquisa do Bing seguintes permitem-lhe melhorar a sua experiência de pesquisa ao combiná-los com outra APIs de pesquisa do Bing.

| API | Descrição |
| -- | -- |
| [Sugestão Automática do Bing](../Bing-Autosuggest/index.yml) | Melhore a experiência de pesquisa da sua aplicação com a API de sugestão automática do Bing retornando sugeridas pesquisas em tempo real.  |
| [Estatísticas do Bing](bing-web-stats.md) | Estatísticas do Bing fornece análises para as APIs de pesquisa do Bing utiliza o seu aplicativo. Alguns o analytics disponível incluem-se ao volume de chamadas, cadeias de consulta superior e a distribuição geográfica. |

## <a name="next-steps"></a>Passos Seguintes

* API de pesquisa Bing [os detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* Os [Requisitos de apresentação e utilização do Bing](./use-display-requirements.md) especificam as utilizações aceitáveis do conteúdo e as informações obtidas por meio das APIs de Pesquisa do Bing.
