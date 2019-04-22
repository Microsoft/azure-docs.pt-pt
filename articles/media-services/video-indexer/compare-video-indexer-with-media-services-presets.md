---
title: Comparação das configurações predefinidas de v3 do Video Indexer e dos serviços de multimédia do Azure | Documentos da Microsoft
description: Este tópico compara o Video Indexer e configurações predefinidas de v3 de serviços de multimédia do Azure.
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
ms.date: 04/07/2019
ms.author: juliako
ms.openlocfilehash: 2c98f6d12f4868e5f90874fe3210fe5368f7ca2d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270341"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Comparar configurações predefinidas de v3 de serviços de multimédia do Azure e o Video Indexer 

Este artigo compara as capacidades do **as APIs do indexador de vídeo** e **dos serviços de multimédia v3 APIs**. 

Atualmente, existe uma sobreposição entre recursos oferecidos pelos [as APIs do indexador de vídeo](https://api-portal.videoindexer.ai/) e o [dos serviços de multimédia v3 APIs](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). A tabela a seguir oferece a diretriz atual para compreender as diferenças e semelhanças. 

## <a name="compare"></a>Comparar

|Funcionalidade|APIs de indexador de vídeos |Analisador de vídeo e áudio Analyzer predefinições<br/>nas APIs de v3 de serviços de multimédia|
|---|---|---|
|Informações de suporte de dados|[Avançada](video-indexer-output-json-v2.md) |[Noções Básicas](../latest/intelligence-concept.md)|
|Experiências|Ver a lista completa das funcionalidades suportadas: <br/> [Descrição geral](video-indexer-overview.md)|Devolve apenas informações de vídeo|
|Faturação|[Preços dos serviços de multimédia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Preços dos serviços de multimédia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformidade|[A ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC SUM(1,2,3](https://www.microsoft.com/TrustCenter/Compliance/SOC), [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa), [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp), [PCI](https://www.microsoft.com/trustcenter/compliance/pci)e [ HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) certified. Para as atualizações mais recentes, visite [status atual de certificações do Video Indexer](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).|Serviços de multimédia estão em conformidade com várias certificações. Confira [Offerings.pdf de conformidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e procure "Serviços de multimédia" ver se ele está em conformidade com um certificado de interesse.|
|Avaliação gratuita|EUA Leste|Não disponível|
|Disponibilidade de região|E.U.A. Leste 2, Centro-Sul dos E.U.A., E.U.A. oeste 2, Europa do Norte, Europa Ocidental, Sudeste asiático, Sudeste asiático e leste da Austrália.  Para as atualizações mais recentes, visite o [produtos por região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) página.|Ver [estado do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral do Video Indexer](video-indexer-overview.md)

[Descrição geral do serviços de multimédia v3](../latest/media-services-overview.md)
