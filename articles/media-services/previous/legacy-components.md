---
title: Componentes herdados dos serviços de mídia do Azure | Microsoft Docs
description: Este tópico aborda os componentes herdados dos serviços de mídia do Azure.
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
ms.date: 11/22/2019
ms.author: juliako
ms.openlocfilehash: bbf3ba17e7f91e88136f2dac50c908262100e77a
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048786"
---
# <a name="azure-media-services-legacy-components"></a>Componentes herdados dos serviços de mídia do Azure

Com o tempo, houve aprimoramentos e aprimoramentos contínuos nos componentes do serviço de mídia. Como resultado, alguns componentes herdados foram desativados. Você pode encontrar as instruções sobre como migrar seu aplicativo do componente herdado para um componente atual nos artigos a seguir.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Planos de aposentadoria de componentes herdados e diretrizes de migração

Estamos a anunciar a depreciação dos processadores de media *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME). Estes processadores vão ser retirados a 31 de março de 2020.

* [Migrar do Windows Azure Media Encoder para media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrar do Encoder Azure Media para media Encoder Standard](migrate-azure-media-encoder.md)

Também estamos anunciando a desativação dos seguintes Análise de Mídia processadores de mídia: 
 
|Nome do processador de mulitmédia|Data de aposentadoria|Notas adicionais|
|---|---|
|[Indexador azure media 2](media-services-process-content-with-indexer2.md)| 1º de outubro de 2020|Este processador de mídia será substituído pelo [Azure Media Services Video Indexer.](https://docs.microsoft.com/azure/media-services/video-indexer/) Para mais informações, consulte [Migrate do Azure Media Indexer 2 para O Indexer de Vídeo dos Serviços De Mídia Azure](migrate-indexer-v1-v2.md).|
|[Indexador de mídia azure](media-services-index-content.md)|1º de outubro de 2020|Este processador de mídia será substituído pelo [Azure Media Services Video Indexer.](https://docs.microsoft.com/azure/media-services/video-indexer/) Para mais informações, consulte [Migrate from Azure Media Indexer para Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>Passos seguintes

[Orientação de migração para a mudança dos Serviços de Media v2 para v3](../latest/migrate-from-v2-to-v3.md)
