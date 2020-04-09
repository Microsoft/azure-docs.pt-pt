---
title: Pontos finais de streaming de escala com o portal Azure / Microsoft Docs
description: Este tutorial acompanha-o através dos degraus de pontas de streaming de escala com o portal Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 40820170bae275f090c5f898387698fc562e59ae
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985546"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Dimensionar os pontos finais de transmissão em fluxo com o Portal do Azure
## <a name="overview"></a>Descrição geral

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Os pontos finais de transmissão em fluxo **Premium** são adequadas para cargas de trabalho avançadas, ao fornecer uma capacidade de largura de banda dimensionável e dedicada. Por predefinição, os clientes que têm os pontos finais de transmissão em fluxo **Premium** recebem uma unidade de transmissão em fluxo (SU). O ponto final de transmissão em fluxo pode ser dimensionado mediante a adição de mais SUs. Cada SU fornece capacidade de largura de banda adicional à aplicação. Para obter mais informações sobre os tipos de pontofinal de streaming e a configuração do CDN, consulte o tópico de [visão geral do Streaming Endpoint.](media-services-streaming-endpoints-overview.md)
 
Este tópico mostra como escalar um ponto final de streaming.

Para obter informações sobre os detalhes de preços, consulte [Detalhes de Preços dos Media Services](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="scale-streaming-endpoints"></a>Pontos finais de streaming de escala

Para alterar o número de unidades de streaming, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na janela **Definições,** selecione **pontos finais de streaming**.
3. Clique no ponto final de streaming que pretende escalar. 

    > [!NOTE] 
    > Só pode escalar os pontos finais de streaming **Premium.**

4. Mova o slider para especificar o número de unidades de streaming.

    ![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

