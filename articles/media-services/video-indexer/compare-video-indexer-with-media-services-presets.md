---
title: Comparação de predefinições de Indexer de Vídeo e Azure Media Services v3
description: Este artigo compara as capacidades do Indexer de Vídeo e os predefinições do Azure Media Services v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: dcfc6ea4afe23424e72c625518356be52f62bc81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602185"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Compare as predefinições v3 da Azure Media Services e o Indexer de Vídeo 

Este artigo compara as capacidades dos **APIs** do Indexer de Vídeo e dos **Media Services v3 APIs**. 

Atualmente, existe uma sobreposição entre as funcionalidades oferecidas pelas [APIs](https://api-portal.videoindexer.ai/) do Indexer de Vídeo e pelos [Media Services v3 APIs](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). O quadro seguinte oferece a orientação atual para compreender as diferenças e semelhanças. 

## <a name="compare"></a>Comparar

|Funcionalidade|APIs indexador de vídeo |Presets de Analisador de Vídeo e Analisador de Áudio<br/>em Media Services v3 APIs|
|---|---|---|
|Media Insights|[Melhorada](video-indexer-output-json-v2.md) |[Noções Básicas](../latest/intelligence-concept.md)|
|Experiências|Consulte a lista completa de funcionalidades suportadas: <br/> [Descrição Geral](video-indexer-overview.md)|Devolve apenas informações de vídeo|
|Faturação|[Preços dos Serviços de Media](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Preços dos Serviços de Media](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformidade|Para as atualizações de conformidade mais atuais, visite [A Oferta de Conformidade Azure.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e procure por "Video Indexer" para ver se cumpre um certificado de interesse.|Para as atualizações de conformidade mais atuais, visite [A Oferta de Conformidade Azure.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e procure "Media Services" para ver se cumpre com um certificado de interesse.|
|Avaliação Gratuita|E.U.A. Leste|Não disponível|
|Disponibilidade de região|Ver Disponibilidade de [Serviços Cognitivos por região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Ver [Disponibilidade de Serviços de Media por região.](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)|

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do Video Indexer](video-indexer-overview.md)

[Visão geral do Media Services v3](../latest/media-services-overview.md)
