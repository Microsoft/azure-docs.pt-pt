---
title: Configurar codificadores no local ao utilizar os serviços de multimédia do Azure para criar transmissões em fluxo | Documentos da Microsoft
description: Este tópico lista os codificadores em direto no local que pode utilizar para capturar os eventos em direto e enviar uma transmissão em direto de velocidade de transmissão única para canais de AMS (o que são a codificação em tempo real ativada) para processamento adicional. As ligações de tópico para tutoriais que mostram como configurar codificadores listadas.
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
ms.openlocfilehash: 8bd0014518dc93abb952114a1728f8fc1d3fb4a5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708071"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Como configurar codificadores no local ao utilizar os serviços de multimédia do Azure para criar transmissões em fluxo
Este tópico lista os codificadores em direto no local que pode utilizar para capturar os eventos em direto e enviar uma transmissão em direto de velocidade de transmissão única para canais de AMS (o que são a codificação em tempo real ativada) para processamento adicional. O tópico também contém ligações para tutoriais que mostram como configurar codificadores listadas.

> [!NOTE]
> Ao transmitir em fluxo através de RTMP, verifique as definições da firewall e/ou do proxy, para confirmar que as portas TCP de saída 1935 e 1936 estão abertas.

## <a name="flash-media-live-encoder"></a>Codificador em direto de mídia Flash
Para obter informações sobre como configurar o [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) encoder (FMLE) para enviar um fluxo em direto com velocidade de transmissão única para um canal de AMS, consulte [configurar FMLE](media-services-configure-fmle-live-encoder.md).

## <a name="haivision-kb-encoder"></a>Codificador Haivision KB
Para obter informações sobre como configurar o [Haivision KB codificador](https://www.haivision.com/products/kb-series/) codificador para enviar um fluxo em direto com velocidade de transmissão única para um canal de AMS, consulte [configurar o Haivision KB codificador](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Para obter informações sobre como configurar o [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) codificador para enviar um fluxo em direto com velocidade de transmissão única para um canal de AMS, consulte [configurar Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>Codificador de NewTek TriCaster
Para obter informações sobre como configurar o [Tricaster](https://newtek.com/products/tricaster-40.html) codificador para enviar um fluxo em direto com velocidade de transmissão única para um canal de AMS, consulte [configurar Tricaster](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Para obter mais informações, consulte [Live elementar](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes

[Transmissão em fluxo através dos serviços de multimédia do Azure para criar transmissões em fluxo do Live](media-services-manage-live-encoder-enabled-channels.md).

