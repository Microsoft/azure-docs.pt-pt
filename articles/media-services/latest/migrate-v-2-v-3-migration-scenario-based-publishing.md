---
title: Orientação de migração de embalagens e entregas
description: Este artigo dá-lhe orientação baseada em cenário para embalagem e entrega que o ajudará na migração da Azure Media Services v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 0a0052fa3d78a3b77094cfccbd4c011321ac5925
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279022"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Orientação de migração baseada em cenários de embalagem e entrega

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

Este artigo dá-lhe orientações baseadas em cenários para embalagem e entrega que o ajudarão na migração da Azure Media Services v2 para v3.

Grandes alterações na forma como o conteúdo é publicado na V3 API. O novo modelo de publicação é simplificado e utiliza menos entidades para criar um Localizador de Streaming. A API reduziu para apenas duas entidades vs. as quatro entidades anteriormente exigidas. As políticas-chave de conteúdo e os localizadores de streaming substituem agora a necessidade `ContentKeyAuthoriationPolicy` `AssetDeliveyPolicy` `ContentKey` de, e `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Embalagem e entrega em v3

1. Criar [Políticas de Chave de Conteúdo](drm-content-key-policy-concept.md).
1. Criar [localizadores de streaming](stream-streaming-locators-concept.md).
1. Obtenha os [caminhos de streaming](create-streaming-locator-build-url.md) 
    1. Configuure-o para um leitor [DE DASH](encode-dynamic-packaging-concept.md#mpeg-dash-protocol) ou [HLS.](encode-dynamic-packaging-concept.md#hls-protocol)

Consulte conceitos de publicação, tutoriais e como guiar abaixo para etapas específicas.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Conceitos de publicação, tutoriais e como guiar

### <a name="concepts"></a>Conceitos

- [Embalagem dinâmica em Media Services v3](encode-dynamic-packaging-concept.md)
- [Filtros](filters-concept.md)
- [Filtrar os seus manifestos usando o Dynamic Packager](filters-dynamic-manifest-concept.md)
- [Streaming Endpoints (Origem) em Azure Media Services](stream-streaming-endpoint-concept.md)
- [Stream de conteúdo com integração de CDN](stream-scale-streaming-cdn-concept.md)
- [Localizadores de streaming](stream-streaming-locators-concept.md)

### <a name="how-to-guides"></a>Guias de procedimentos

- [Gerir pontos finais de streaming com os Media Services v3](stream-manage-streaming-endpoints-how-to.md)
- [Exemplo da CLI: publicar um elemento](cli-publish-asset.md)
- [Criar um localizador de transmissão e compilar URLs](create-streaming-locator-build-url.md)
- [Descarregue os resultados de um trabalho](job-download-results-how-to.md)
- [Faixas de áudio descritivas de sinal](signal-descriptive-audio-howto.md)
- [Configuração completa do Leitor de Multimédia do Azure](../azure-media-player/azure-media-player-full-setup.md)
- [Como utilizar o jogador Video.js com a Azure Media Services](player-how-to-video-js-player.md)
- [Como usar o jogador Shaka com a Azure Media Services](player-shaka-player-how-to.md)

## <a name="samples"></a>Amostras

Também pode [comparar o código V2 e V3 nas amostras de código](migrate-v-2-v-3-migration-samples.md).
