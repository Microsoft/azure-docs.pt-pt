---
title: Comparação entre as predefinições do Video Indexer e dos serviços de mídia do Azure v3
description: Este artigo compara Video Indexer recursos e as predefinições dos serviços de mídia do Azure v3.
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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 2e2abd4ffe5a6d0a6336d811599db687da146f1e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513189"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Comparar as predefinições e Video Indexer dos serviços de mídia do Azure v3 

Este artigo compara os recursos de **APIs de video indexer** e **APIs do Media Services V3**. 

Atualmente, há uma sobreposição entre os recursos oferecidos pelas [APIs de video indexer](https://api-portal.videoindexer.ai/) e as [APIs dos serviços de mídia v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). A tabela a seguir oferece a diretriz atual para entender as diferenças e as semelhanças. 

## <a name="compare"></a>Comparar

|Funcionalidade|APIs de Video Indexer |Analisador de vídeo e predefinições do analisador de áudio<br/>em APIs do Media Services V3|
|---|---|---|
|Informações de mídia|[Elevar](video-indexer-output-json-v2.md) |[Noções Básicas](../latest/intelligence-concept.md)|
|Apresenta|Consulte a lista completa de recursos com suporte: <br/> [Descrição geral](video-indexer-overview.md)|Retorna apenas o vídeo insights|
|Faturação|[Preços dos serviços de mídia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Preços dos serviços de mídia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformidade|[Iso 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1, 2, 3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)e [HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) certificados. Para obter as atualizações mais recentes, visite o [status de certificações atuais de video indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Os serviços de mídia estão em conformidade com muitas certificações. Confira as [ofertas de conformidade do Azure. pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e pesquise "serviços de mídia" para ver se ele está em conformidade com um certificado de interesse.|
|Avaliação gratuita|Este dos E.U.A.|Não disponível|
|Disponibilidade regional|Leste dos EUA 2, Sul EUA Central, oeste dos EUA 2, Europa Setentrional, Europa Ocidental, Sudeste Asiático, Ásia Oriental e leste da Austrália.  Para obter as atualizações mais recentes, visite a página [produtos por região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) .|Consulte [status do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Passos seguintes

[Descrição geral do Video Indexer](video-indexer-overview.md)

[Visão geral dos serviços de mídia v3](../latest/media-services-overview.md)
