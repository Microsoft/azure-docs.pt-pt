---
title: Códigos de erro de eventos ao vivo da Azure Media Services Microsoft Docs
description: Este artigo lista códigos de erro de eventos ao vivo.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599473"
---
# <a name="media-services-live-event-error-codes"></a>Códigos de erro de eventos ao vivo dos Media Services

A tabela abaixo lista os códigos de erro [do Evento Ao Vivo:](live-events-outputs-concept.md)

|Erro|Descrição|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|Este erro ocorre quando o codificador de entrada está a enviar fluxos superiores a 30fps para codificar eventos/canais ao vivo.|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|Este erro ocorre quando o codificador de entrada está a enviar streams que excedem as seguintes resoluções: 1920x1088 para codificar eventos/canais ao vivo e 4096 x 2160 para eventos/canais pass-through.|

## <a name="see-also"></a>Consulte também

[Códigos de erro de Streaming Endpoint (Origem)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Stream live with Media Services](stream-live-tutorial-with-api.md)
