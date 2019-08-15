---
title: Dimensionar o processamento de mídia adicionando unidades de codificação – Azure |  Microsoft Docs
description: Saiba como adicionar unidades de codificação com o .NET
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milangada
ms.openlocfilehash: 9d393e07008d981834d9deb48ded73995366d7e4
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016576"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Como dimensionar a codificação com o .NET SDK
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Descrição geral
> [!IMPORTANT]
> Certifique-se de examinar a [visão geral](media-services-scale-media-processing-overview.md) para obter mais informações sobre como dimensionar o processamento de mídia.
> 
> 

Para alterar o tipo de unidade reservada e o número de unidades reservadas para codificação usando o SDK do .NET, faça o seguinte:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Abrindo um tíquete de suporte

Por padrão, cada conta dos serviços de mídia pode ser dimensionada para até 10 unidades reservadas de mídia S2 ou S3 (MRUs) ou 25 S1 MRUs e 5 unidades reservadas de streaming sob demanda. Você pode solicitar um limite mais alto abrindo um tíquete de suporte.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

