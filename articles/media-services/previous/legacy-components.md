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
ms.openlocfilehash: 6a61c868da7b979b1ac5654f4be93b5245179105
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423929"
---
# <a name="azure-media-services-legacy-components"></a>Componentes herdados dos serviços de mídia do Azure

Com o tempo, houve aprimoramentos e aprimoramentos contínuos nos componentes do serviço de mídia. Como resultado, alguns componentes herdados foram desativados. Você pode encontrar as instruções sobre como migrar seu aplicativo do componente herdado para um componente atual nos artigos a seguir.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Planos de aposentadoria de componentes herdados e diretrizes de migração

Estamos anunciando a reprovação dos processadores de mídia do *Windows Azure Media Encoder* (WAME) e *do Azure Media Encoder* (ame). Esses processadores estão sendo desativados em 31 de março de 2020.

* [Migrar do codificador de mídia do Windows Azure para Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrar do Azure Media Encoder para Media Encoder Standard](migrate-azure-media-encoder.md)

Também estamos anunciando a desativação dos seguintes Análise de Mídia processadores de mídia: 
 
|Nome do processador de mulitmédia|Data de aposentadoria|Notas adicionais|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 1º de janeiro de 2020|Esse processador de mídia será substituído pelos [serviços de mídia do Azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Para obter mais informações, consulte [migrar do Azure Media indexer 2 para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1º de outubro de 2020|Esse processador de mídia será substituído pelos [serviços de mídia do Azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Para obter mais informações, consulte [migrar do Azure Media indexer para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>Passos Seguintes

[Diretrizes de migração para mudar dos serviços de mídia v2 para v3](../latest/migrate-from-v2-to-v3.md)
