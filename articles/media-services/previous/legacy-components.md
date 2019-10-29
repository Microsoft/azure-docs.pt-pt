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
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 30e8fa51df42d202d77ecdbc6a31fe3c7aaac6f4
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968626"
---
# <a name="azure-media-services-legacy-components"></a>Componentes herdados dos serviços de mídia do Azure

Com o tempo, houve aprimoramentos e aprimoramentos contínuos nos componentes do serviço de mídia. Como resultado, alguns componentes herdados foram desativados. Você pode encontrar as instruções sobre como migrar seu aplicativo do componente herdado para um componente atual nos artigos a seguir.

## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Planos de aposentadoria de componentes herdados e diretrizes de migração

Estamos anunciando a reprovação dos processadores de mídia do *Windows Azure Media Encoder* (WAME) e *do Azure Media Encoder* (ame). Esses processadores estão sendo desativados em 30 de novembro de 2019.

* [Migrar do codificador de mídia do Windows Azure para Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrar do Azure Media Encoder para Media Encoder Standard](migrate-azure-media-encoder.md)

Também estamos anunciando a desativação dos seguintes Análise de Mídia processadores de mídia: 

|Nome do processador de mulitmédia|Data de aposentadoria|Notas adicionais|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1º de janeiro de 2020|Esse processador de mídia será substituído pelos [serviços de mídia do Azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Para obter mais informações, consulte [migrar do Azure Media indexer 2 para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1º de outubro de 2020|Esse processador de mídia será substituído pelos [serviços de mídia do Azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Para obter mais informações, consulte [migrar do Azure Media indexer para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md)
|[Azure Media Face Detector](media-services-face-and-emotion-detection.md)|1º de fevereiro de 2020|Esse Análise de Mídia processador de visualização será desativado e não será movido para a disponibilidade geral. Nós iremos avaliar seus cenários e casos de uso com os clientes para futuros investimentos.|
|[Azure Media Motion Detector](media-services-motion-detection.md)|1º de fevereiro de 2020|Esse Análise de Mídia processador de visualização será desativado e não será movido para a disponibilidade geral. Nós iremos avaliar seus cenários e casos de uso com os clientes para futuros investimentos.|
|[OCR de mídia do Azure](media-services-video-optical-character-recognition.md)|1º de fevereiro de 2020|Esse processador de mídia será substituído pelos [serviços de mídia do azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) e a [Predefinição do analisador de vídeo da API dos serviços de mídia do Azure v3](../latest/analyzing-video-audio-files-concept.md).|
|[Azure Media Video Thumbnails](media-services-video-summarization.md)|1º de fevereiro de 2020|Esse processador de mídia será substituído pelos [serviços de mídia do azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) e a [Predefinição do analisador de vídeo da API dos serviços de mídia do Azure v3](../latest/analyzing-video-audio-files-concept.md).|

## <a name="next-steps"></a>Passos seguintes

[Diretrizes de migração para mudar dos serviços de mídia v2 para v3](../latest/migrate-from-v2-to-v3.md)
