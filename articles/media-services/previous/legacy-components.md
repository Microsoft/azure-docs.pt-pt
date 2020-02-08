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
ms.openlocfilehash: cfc9d944f7851f9b802e18c861fd16438171b8e7
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069588"
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
|[Indexador azure media 2](media-services-process-content-with-indexer2.md)|1 de janeiro de 2020|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer.](https://docs.microsoft.com/azure/media-services/video-indexer/) Para mais informações, consulte [Migrate do Azure Media Indexer 2 para O Indexer de Vídeo dos Serviços De Mídia Azure](migrate-indexer-v1-v2.md).|
|[Indexador de mídia azure](media-services-index-content.md)|1 de março de 2023|Este processador de mídia está a ser substituído pelo [Azure Media Services Video Indexer.](https://docs.microsoft.com/azure/media-services/video-indexer/) Para mais informações, consulte [Migrate from Azure Media Indexer para Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>Passos seguintes

[Orientação de migração para a mudança dos Serviços de Media v2 para v3](../latest/migrate-from-v2-to-v3.md)
