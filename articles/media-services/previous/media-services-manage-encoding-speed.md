---
title: Gerir a velocidade e simultaneidade da codificação com os serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo fornece uma breve descrição geral de como pode gerir a velocidade e simultaneidade de seus trabalhos/tarefas de codificação com os serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bcaadc8dd61899aff860ad246e30170c99ec0f6
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188633"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Gerir a velocidade e simultaneidade da codificação  

Este artigo fornece uma breve descrição geral de como pode gerir a velocidade e simultaneidade de seus trabalhos/tarefas de codificação.

## <a name="overview"></a>Descrição geral

Nos Media Services, um **tipo de unidade reservada** determina a velocidade com que o suporte de dados de tarefas de processamento é processados. Pode escolher de entre os seguintes tipos de unidade reservada: **S1**, **S2**, ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**. O [dimensionamento de unidades de codificação](media-services-scale-media-processing-overview.md) tópico mostra uma tabela que o ajuda a tomar a decisão ao escolher entre diferentes velocidades de codificação.

Além de especificar o tipo de unidade reservada, pode especificar para aprovisionar a sua conta com **unidades reservadas**. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. Por exemplo, se a sua conta tiver cinco unidades reservadas, em seguida, as tarefas de cinco multimédia serão executados em simultâneo, desde como existem tarefas a serem processados. As tarefas restantes irão aguardar na fila e irão obter escolhidas para processamento, sequencialmente, quando uma tarefa em execução for concluída. Se uma conta não tem quaisquer unidades reservadas aprovisionadas, em seguida, tarefas serão detetadas sequencialmente. Neste caso, o tempo de espera entre uma tarefa de terminar e iniciar a seguir um dependerá da disponibilidade de recursos no sistema.

Para obter informações detalhadas e exemplos que mostram como dimensionar as unidades de codificação, consulte [isso](media-services-scale-media-processing-overview.md) tópico.

## <a name="next-step"></a>Passo seguinte

[Unidades de codificação de dimensionamento](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

