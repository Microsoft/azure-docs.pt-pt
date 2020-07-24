---
title: Processamento de mídia em escala usando o portal Azure / Microsoft Docs
description: Este tutorial acompanha-o através dos passos de dimensionamento do processamento de mídia usando o portal Azure.
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
ms.openlocfilehash: 3001cd89b5dbb55728fc1f8411e0bb87e17044b7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084454"
---
# <a name="change-the-reserved-unit-type"></a>Alterar tipo de unidade reservada
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Descrição geral

As contas dos Serviços de Multimédia estão associadas a um Tipo de Unidade Reservada, que determina a velocidade do processamento das suas tarefas de processamento de mulitmédia. Pode escolher entre os seguintes tipos de unidade reservada: **S1,** **S2**ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**.

Além de especificar o tipo de unidade reservada, pode especificar a disponibilização da sua conta com **Unidades Reservadas** (RUs). O número de RUs aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta.

>[!NOTE]
>As RUs destinam-se a paralelizar todos os processamentos de multimédia, incluindo trabalhos de indexação com o Azure Media Indexer. No entanto, ao contrário da codificação, os trabalhos de indexação não são processados mais depressa com unidades reservadas mais rápidas.

> [!IMPORTANT]
> Certifique-se de rever o tópico [de visão geral](media-services-scale-media-processing-overview.md) para obter mais informações sobre o tópico de processamento de mídia de escala.
> 
> 

## <a name="scale-media-processing"></a>Processamento de mídia em escala
Para alterar o tipo de unidade reservada e o número de unidades reservadas, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na janela **Definições,** selecione **Unidades reservadas para os meios**de comunicação .
   
    Para alterar o número de unidades reservadas para o tipo de unidade reservada selecionada, utilize o slider **Unidades De Serviço de Mídia** na parte superior do ecrã.
   
    Para alterar o **TIPO UNITÁRIO RESERVADO,** clique na **barra de velocidade das unidades de processamento reservadas.** Em seguida, selecione o nível de preços de que necessita: S1, S2 ou S3.
   
3. Prima o botão GUARDAR para guardar as alterações.
   
    As novas unidades reservadas são atribuídas quando premir SAVE.

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
