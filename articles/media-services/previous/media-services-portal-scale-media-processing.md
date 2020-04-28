---
title: Processamento de meios de escala utilizando o portal Azure [ Microsoft Docs
description: Este tutorial acompanha-o através dos passos do processamento de meios de escala utilizando o portal Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "61127546"
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

As contas dos Serviços de Multimédia estão associadas a um Tipo de Unidade Reservada, que determina a velocidade do processamento das suas tarefas de processamento de mulitmédia. Pode escolher entre os seguintes tipos de unidades reservados: **S1,** **S2,** ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**.

Além de especificar o tipo de unidade reservado, pode especificar para fornecer a sua conta com **Unidades Reservadas** (RUs). O número de RUs aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta.

>[!NOTE]
>As RUs destinam-se a paralelizar todos os processamentos de multimédia, incluindo trabalhos de indexação com o Azure Media Indexer. No entanto, ao contrário da codificação, os trabalhos de indexação não são processados mais depressa com unidades reservadas mais rápidas.

> [!IMPORTANT]
> Certifique-se de rever o tópico de [visão geral](media-services-scale-media-processing-overview.md) para obter mais informações sobre o tema de processamento de meios de escala.
> 
> 

## <a name="scale-media-processing"></a>Processamento de meios de escala
Para alterar o tipo de unidade reservada e o número de unidades reservadas, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na janela **Definições,** selecione **Unidades reservadas para media**.
   
    Para alterar o número de unidades reservadas para o tipo de unidade reservado selecionado, utilize o slider **Media Served Units** na parte superior do ecrã.
   
    Para alterar o **TIPO DE UNIDADE RESERVADO,** clique na **barra de velocidade das unidades de processamento reservadas.** Em seguida, selecione o nível de preços de que necessita: S1, S2 ou S3.
   
3. Prima o botão GUARDAR para guardar as alterações.
   
    As novas unidades reservadas são atribuídas quando premir SAVE.

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

