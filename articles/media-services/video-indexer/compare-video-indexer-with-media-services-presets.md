---
title: Comparação de vídeo indexador e Azure Media Services v3 predefinições
description: Este artigo compara as capacidades do Indexer de Vídeo e as predefinições V3 dos Azure Media Services.
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
ms.openlocfilehash: 0961aa3e573e511a6941bc59ddc335f64799abb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047337"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Compare os Azure Media Services v3 predefinições e indexante de vídeo 

Este artigo compara as capacidades das APIs do **Indexante** de Vídeo e **dos Serviços de Mídia v3 APIs**. 

Atualmente, existe uma sobreposição entre as funcionalidades oferecidas pelas [APIs do Indexante](https://api-portal.videoindexer.ai/) de Vídeo e os [Serviços de Comunicação v3 APIs](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). O quadro seguinte oferece a orientação atual para a compreensão das diferenças e semelhanças. 

## <a name="compare"></a>Comparar

|Funcionalidade|APIs indexante de vídeo |Predefinições de analisador de vídeo e analisador de áudio<br/>em Serviços de Mídia v3 APIs|
|---|---|---|
|Media Insights|[Melhorada](video-indexer-output-json-v2.md) |[Fundamentos](../latest/analyzing-video-audio-files-concept.md)|
|Experiências|Consulte a lista completa de funcionalidades suportadas: <br/> [Descrição geral](video-indexer-overview.md)|Retorna apenas insights de vídeo|
|Faturação|[Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformidade|Para obter as atualizações de conformidade mais atuais, visite [a Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e procure "Video Indexer" para ver se cumpre um certificado de interesse.|Para obter as atualizações de conformidade mais atuais, visite [a Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e procure "Media Services" para ver se cumpre um certificado de interesse.|
|Avaliação Gratuita|E.U.A. Leste|Não disponível|
|Disponibilidade de região|Ver [disponibilidade de Serviços Cognitivos por região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Consulte [a disponibilidade dos Serviços de Comunicação Social por região.](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)|

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do Video Indexer](video-indexer-overview.md)

[Visão geral dos Serviços de Comunicação Social v3](../latest/media-services-overview.md)
