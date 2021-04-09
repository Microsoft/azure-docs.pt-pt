---
title: Componentes legados da Azure Media Services | Microsoft Docs
description: Este tópico discute componentes legados da Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 3487e735e48cb5067515762d6276429ca81e43fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008744"
---
# <a name="azure-media-services-legacy-components"></a>Componentes legados da Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Com o tempo, melhoramos os componentes do Media Service e retiramos componentes legados. Este artigo ajuda-o a migrar a sua aplicação de um componente legado para um componente atual.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Planos de aposentadoria de componentes legados e orientação migratória

Os processadores de mídia *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME) são depreciados.

* [Migrar do Windows Azure Media Encoder para Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrar do Azure Media Encoder para Media Encoder Standard](migrate-azure-media-encoder.md)

Os seguintes processadores de media Media Analytics são depreciados ou em breve serão depreciados:

  
 
| **Nome do processador de mulitmédia** | **Data da reforma** | **Notas adicionais** |
| --- | --- | ---|
| Azure Media Indexer 2 | 1 de janeiro de 2020 | Este processador de mídia será substituído pelo [modo Base AudioAnalyzerPreset dos Media Services v3 AudioAnalyzerPreset](../latest/analyzing-video-audio-files-concept.md). Para obter mais informações, consulte [Migrar do Azure Media Indexer 2 para Azure Media Services Video Indexer](migrate-indexer-v1-v2.md). |
| Azure Media Indexer | 1 de março de 2023 | Este processador de mídia será substituído pelo [modo Base AudioAnalyzerPreset dos Media Services v3 AudioAnalyzerPreset](../latest/analyzing-video-audio-files-concept.md). Para obter mais informações, consulte [Migrar do Azure Media Indexer 2 para Azure Media Services Video Indexer](migrate-indexer-v1-v2.md). |
| Deteção de Movimentos | 1 de junho de 2020|Não há planos de substituição neste momento. |
| Resumo de vídeo |1 de junho de 2020|Não há planos de substituição neste momento.|
| Reconhecimento de caracteres óticos de vídeo | 1 de junho de 2020 |Este processador de mídia foi substituído por [Azure Media Services Video Indexer](../video-indexer/index.yml). Além disso, considere a utilização [da Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [o Compare Azure Media Services v3 predefinições e Índice de Vídeo](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Detetor de Rosto | 1 de junho de 2020 | Este processador de mídia foi substituído por [Azure Media Services Video Indexer](../video-indexer/index.yml). Além disso, considere a utilização [da Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [o Compare Azure Media Services v3 predefinições e Índice de Vídeo](../video-indexer/compare-video-indexer-with-media-services-presets.md). |
| Content Moderator | 1 de junho de 2020 |Este processador de mídia foi substituído por [Azure Media Services Video Indexer](../video-indexer/index.yml). Além disso, considere a utilização [da Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [o Compare Azure Media Services v3 predefinições e Índice de Vídeo](../video-indexer/compare-video-indexer-with-media-services-presets.md). |

## <a name="next-steps"></a>Passos seguintes

[Orientação de migração para passar dos Serviços de Comunicação v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)
