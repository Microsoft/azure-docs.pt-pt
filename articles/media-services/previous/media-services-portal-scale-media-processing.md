---
title: Dimensionar processamento de multimédia no portal do Azure | Documentos da Microsoft
description: Este tutorial orienta-o pelos passos de dimensionamento no portal do Azure de processamento de multimédia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c840764dc978a8dacb3450c0aca5e5d93284b8a6
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260116"
---
# <a name="change-the-reserved-unit-type"></a>Alterar tipo de unidade reservada
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Descrição geral

As contas dos Serviços de Multimédia estão associadas a um Tipo de Unidade Reservada, que determina a velocidade do processamento das suas tarefas de processamento de mulitmédia. Pode escolher de entre os seguintes tipos de unidade reservada: **S1**, **S2**, ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**.

Para além de especificar o tipo de unidade reservada, pode especificar o aprovisionamento da sua conta com **Unidades Reservadas** (RUs). O número de RUs aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta.

>[!NOTE]
>As RUs destinam-se a paralelizar todos os processamentos de multimédia, incluindo trabalhos de indexação com o Azure Media Indexer. No entanto, ao contrário da codificação, os trabalhos de indexação não são processados mais depressa com unidades reservadas mais rápidas.

> [!IMPORTANT]
> Lembre-se de que reveja os [descrição geral](media-services-scale-media-processing-overview.md) tópico para obter mais informações sobre o dimensionamento de tópico de processamento de multimédia.
> 
> 

## <a name="scale-media-processing"></a>Dimensionar processamento de multimédia
Para alterar o tipo de unidade reservada e o número de unidades reservadas, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na **configurações** janela, selecione **unidades reservadas de multimédia**.
   
    Para alterar o número de unidades reservadas para o tipo de unidade reservada selecionado, utilize o **unidades de servidos multimédia** controlo de deslize na parte superior do ecrã.
   
    Para alterar o **tipo de unidade reservada**, clique nas **velocidade das unidades de processamento reservadas** barra. Em seguida, selecione o escalão de preço que tem de: S1, S2 ou S3.
   
3. Prima o botão GUARDAR para guardar as alterações.
   
    As unidades reservadas novas são alocadas quando pressiona SALVAR.

## <a name="next-steps"></a>Passos Seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

