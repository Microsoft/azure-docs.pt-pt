---
title: Componentes legados da Azure Media Services Microsoft Docs
description: Este tema discute os componentes legados da Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921084"
---
# <a name="azure-media-services-legacy-components"></a>Componentes legados da Azure Media Services

Ao longo do tempo, tem havido melhorias e melhorias constantes nos componentes do Serviço de Media. Como resultado, alguns componentes legados foram retirados. Pode encontrar as instruções sobre como migrar a sua aplicação do componente legado para um componente atual nos seguintes artigos.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Planos de reforma dos componentes legados e orientação para a migração

Estamos a anunciar a depreciação dos processadores de media *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME). Estes processadores vão ser retirados a 31 de março de 2020.

* [Migrar do Windows Azure Media Encoder para media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrar do Encoder Azure Media para media Encoder Standard](migrate-azure-media-encoder.md)

Estamos também a nunciando a retirada dos seguintes processadores de media Media Analytics: 
 
|Nome do processador de mulitmédia|Data de aposentadoria|Notas adicionais|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 de janeiro de 2020|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer.](https://docs.microsoft.com/azure/media-services/video-indexer/) Para mais informações, consulte [Migrate do Azure Media Indexer 2 para O Indexer de Vídeo dos Serviços De Mídia Azure](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 de março de 2023|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer.](https://docs.microsoft.com/azure/media-services/video-indexer/) Para mais informações, consulte [Migrate from Azure Media Indexer para Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)|
|[Deteção de Movimento](media-services-motion-detection.md)|1 de junho de 2020|Não há planos de substituição neste momento.|
|[Resumo de Vídeo](media-services-video-summarization.md)|1 de junho de 2020|Não há planos de substituição neste momento.|
|[Reconhecimento de caracteres óticos de vídeo](media-services-video-optical-character-recognition.md)|1 de junho de 2020|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer.](https://docs.microsoft.com/azure/media-services/video-indexer/) Além disso, considere utilizar o [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Ver [Compare Azure Media Services v3 presets e Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Detetor de Rosto](media-services-face-and-emotion-detection.md)|1 de junho de 2020|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer.](https://docs.microsoft.com/azure/media-services/video-indexer/) Além disso, considere utilizar o [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Ver [Compare Azure Media Services v3 presets e Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Content Moderator](media-services-content-moderation.md)|1 de junho de 2020|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer.](https://docs.microsoft.com/azure/media-services/video-indexer/) Além disso, considere utilizar o [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Ver [Compare Azure Media Services v3 presets e Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Passos seguintes

[Orientação de migração para a mudança dos Serviços de Media v2 para v3](../latest/migrate-from-v2-to-v3.md)
