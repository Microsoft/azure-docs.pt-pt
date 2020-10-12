---
title: Configurar codificadores no local ao utilizar os Serviços de Media Azure para criar fluxos multi-bitrates [ Microsoft Docs
description: Este tópico lista os codificadores ao vivo que pode usar para capturar os seus eventos ao vivo e enviar um único bitrate live stream para canais AMS (que estão ativados em codificação ao vivo) para posterior processamento. O tópico liga-se a tutoriais que mostram como configurar codificadores listados.
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
ms.openlocfilehash: 77f2d433b32d180c7b3a819af96a50b721c2087f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89263439"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Como configurar codificadores no local ao utilizar os Azure Media Services para criar fluxos multi-bitrates

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este tópico lista os codificadores ao vivo que pode usar para capturar os seus eventos ao vivo e enviar um único bitrate live stream para canais AMS (que estão ativados em codificação ao vivo) para posterior processamento. O tópico também se liga a tutoriais que mostram como configurar codificadores listados.

> [!NOTE]
> Ao transmitir em fluxo através de RTMP, verifique as definições da firewall e/ou do proxy, para confirmar que as portas TCP de saída 1935 e 1936 estão abertas.

## <a name="haivision-kb-encoder"></a>Codificação Haivision KB
Para obter informações sobre como configurar o codificadora [Haivision KB Encoder](https://www.haivision.com/products/kb-series/) para enviar um único bitrate live stream para um canal AMS, consulte [o Codificante Haivision KB Encoder](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Para obter informações sobre como configurar o codificadora [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) para enviar uma única transmissão em direto para um canal AMS, consulte [a Configuração da Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Para mais informações, consulte [Elemental Live](https://www.elemental.com/products/aws-elemental-appliances-software/#elemental-live).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes

[Streaming ao vivo usando a Azure Media Services para criar streams multi-bitrates](media-services-manage-live-encoder-enabled-channels.md).

