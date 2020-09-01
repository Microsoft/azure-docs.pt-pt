---
title: Componentes legados da Azure Media Services Microsoft Docs
description: Este tópico discute componentes legados da Azure Media Services.
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
ms.openlocfilehash: 1c3e50fd64c113aafd2d70cc76c7f789e080ad23
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269527"
---
# <a name="azure-media-services-legacy-components"></a>Componentes legados da Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ao longo do tempo, tem havido melhorias e melhorias constantes nos componentes do Media Service. Como resultado, alguns componentes do legado foram retirados. Pode encontrar as instruções sobre como migrar a sua aplicação do componente legado para um componente atual nos seguintes artigos.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Planos de aposentadoria de componentes legados e orientação migratória

Estamos a anunciar a depreciação dos processadores de mídia *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME). Estes processadores vão ser reformados a 31 de março de 2020.

* [Migrar do Windows Azure Media Encoder para Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrar do Azure Media Encoder para Media Encoder Standard](migrate-azure-media-encoder.md)

Também anunciamos a retirada dos seguintes processadores de media Media Analytics: 
 
|Nome do processador de mulitmédia|Data da reforma|Notas adicionais|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 de janeiro de 2020|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer](../video-indexer/index.yml). Para obter mais informações, consulte [Migrar do Azure Media Indexer 2 para Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 de março de 2023|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer](../video-indexer/index.yml). Para mais informações, consulte [Migrar do Azure Media Indexer para O Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)|
|[Deteção de Movimentos](media-services-motion-detection.md)|1 de junho de 2020|Não há planos de substituição neste momento.|
|[Resumo de vídeo](media-services-video-summarization.md)|1 de junho de 2020|Não há planos de substituição neste momento.|
|[Reconhecimento de caracteres óticos de vídeo](media-services-video-optical-character-recognition.md)|1 de junho de 2020|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer](../video-indexer/index.yml). Além disso, considere a utilização [da Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Ver [Comparar Azure Media Services v3 predefinições e Indexante de Vídeo](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Detetor de Rosto](media-services-face-and-emotion-detection.md)|1 de junho de 2020|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer](../video-indexer/index.yml). Além disso, considere a utilização [da Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Ver [Comparar Azure Media Services v3 predefinições e Indexante de Vídeo](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Content Moderator](media-services-content-moderation.md)|1 de junho de 2020|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer](../video-indexer/index.yml). Além disso, considere a utilização [da Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Ver [Comparar Azure Media Services v3 predefinições e Indexante de Vídeo](../video-indexer/compare-video-indexer-with-media-services-presets.md)|

## <a name="next-steps"></a>Passos seguintes

[Orientação de migração para passar dos Serviços de Comunicação v2 para v3](../latest/migrate-from-v2-to-v3.md)
