---
title: Processamento de meios de escala adicionando unidades de codificação - Azure [  Microsoft Docs
description: Este artigo demonstra como adicionar unidades de codificação com a Azure Media Services .NET.
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
ms.openlocfilehash: 86fd923c121b9d46109529f75bc3d0d040f1a7a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74887293"
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
> Certifique-se de rever a [visão geral](media-services-scale-media-processing-overview.md) para obter mais informações sobre o processamento de meios de escala.
> 
> 

Para alterar o tipo de unidade reservada e o número de unidades reservadas de codificação utilizando o .NET SDK, faça o seguinte:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Abertura de um bilhete de apoio

Por predefinição, todas as contas de Serviços de Media podem escalar até 10 Unidades Reservadas s2 ou S3 Media (MRUs) ou 25 S1 MRUs e 5 Unidades Reservadas de Streaming A Pedido. Você pode pedir um limite mais alto abrindo um bilhete de apoio.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

