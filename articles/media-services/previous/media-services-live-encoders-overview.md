---
title: Configure os codificadores no local ao utilizar os Serviços De Mídia Azure para criar fluxos multibitantes [ Microsoft Docs
description: Este tópico lista no local codificadores ao vivo que pode usar para capturar os seus eventos ao vivo e enviar um único stream bitrate ao vivo para os canais AMS (que estão habilitados para codificação ao vivo) para posterior processamento. O tópico liga-se a tutoriais que mostram como configurar codificadores listados.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5a17f98d69cb2196f92e19b66fc23dcd89bce188
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687151"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Como configurar codificadores no local ao utilizar os Serviços De Mídia Azure para criar fluxos multibitantes
Este tópico lista no local codificadores ao vivo que pode usar para capturar os seus eventos ao vivo e enviar um único stream bitrate ao vivo para os canais AMS (que estão habilitados para codificação ao vivo) para posterior processamento. O tópico também se liga a tutoriais que mostram como configurar codificadores listados.

> [!NOTE]
> Ao transmitir em fluxo através de RTMP, verifique as definições da firewall e/ou do proxy, para confirmar que as portas TCP de saída 1935 e 1936 estão abertas.

## <a name="haivision-kb-encoder"></a>Codificador Haivision KB
Para obter informações sobre como configurar o [codificador de codificador Haivision KB](https://www.haivision.com/products/kb-series/) para enviar um único stream bitrate ao vivo para um canal AMS, consulte [Configurar o Codificador Haivision KB](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Difusão telestream
Para obter informações sobre como configurar o codificador [telestream wirecast](https://www.telestream.net/wirecast/overview.htm) para enviar um único fluxo ao vivo bitrate para um canal AMS, consulte [configurar a wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Para mais informações, consulte [Elemental Live](https://www.elemental.com/products/aws-elemental-appliances-software/#elemental-live).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes

[Streaming ao vivo utilizando o Azure Media Services para criar fluxos multibitantes.](media-services-manage-live-encoder-enabled-channels.md)

