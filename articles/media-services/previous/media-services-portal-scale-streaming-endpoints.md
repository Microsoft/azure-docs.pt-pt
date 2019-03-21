---
title: Dimensionar transmissão em fluxo de pontos de extremidade com o portal do Azure | Documentos da Microsoft
description: Este tutorial explica-lhe os passos de dimensionar os pontos finais de transmissão em fluxo com o portal do Azure.
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
ms.openlocfilehash: 23eb51428dcf4961febfb592bf957bb8beeeda57
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58257212"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Dimensionar os pontos finais de transmissão em fluxo com o Portal do Azure
## <a name="overview"></a>Descrição geral

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Os pontos finais de transmissão em fluxo **Premium** são adequadas para cargas de trabalho avançadas, ao fornecer uma capacidade de largura de banda dimensionável e dedicada. Por predefinição, os clientes que têm os pontos finais de transmissão em fluxo **Premium** recebem uma unidade de transmissão em fluxo (SU). O ponto final de transmissão em fluxo pode ser dimensionado mediante a adição de mais SUs. Cada SU fornece capacidade de largura de banda adicional à aplicação. Para obter mais informações sobre a transmissão em fluxo de tipos de ponto final e a configuração de CDN, consulte a [descrição geral de ponto final de transmissão em fluxo](media-services-streaming-endpoints-overview.md) tópico.
 
Este tópico mostra como dimensionar um ponto de final de transmissão em fluxo.

Para obter informações sobre os detalhes de preços, consulte [Detalhes de Preços dos Media Services](https://go.microsoft.com/fwlink/?LinkId=275107).

## <a name="scale-streaming-endpoints"></a>Dimensionar pontos finais de transmissão em fluxo

Para alterar o número de unidades de transmissão em fluxo, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na **configurações** janela, selecione **pontos finais de transmissão em fluxo**.
3. Clique no ponto de final de transmissão em fluxo que pretende dimensionar. 

    > [!NOTE] 
    > Apenas pode dimensionar **Premium** pontos finais de transmissão em fluxo.

4. Mova o controlo de deslize para especificar o número de unidades de transmissão em fluxo.

    ![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Passos Seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

