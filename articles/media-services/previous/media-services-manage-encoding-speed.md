---
title: Gerencie a velocidade e a coneção da sua codificação com a Azure Media Services [ Serviços De Comunicação Social] Microsoft Docs
description: Este artigo dá uma breve visão geral de como pode gerir a velocidade e a conmoedação dos seus trabalhos/tarefas de codificação com a Azure Media Services.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "61463757"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Gerir a velocidade e simultaneidade da codificação  

Este artigo dá uma breve visão geral de como pode gerir a velocidade e a conmoedação dos seus trabalhos/tarefas codificantes.

## <a name="overview"></a>Descrição geral

Nos Serviços de Media, um **Tipo de Unidade Reservada** determina a rapidez com que as suas tarefas de processamento de meios de comunicação são processadas. Pode escolher entre os seguintes tipos de unidades reservados: **S1,** **S2,** ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**. O tópico das unidades de codificação de [escalonamento](media-services-scale-media-processing-overview.md) mostra uma tabela que o ajuda a tomar uma decisão ao escolher entre diferentes velocidades de codificação.

Além de especificar o tipo de unidade reservado, pode especificar para fornecer a sua conta com **Unidades Reservadas**. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. Por exemplo, se a sua conta tiver cinco unidades reservadas, então cinco tarefas de mídia estarão em execução simultaneamente, desde que existam tarefas a serem processadas. As restantes tarefas vão esperar na fila e serão apanhadas para processamento sequencialmente quando uma tarefa de execução terminar. Se uma conta não tiver unidades reservadas, as tarefas serão recolhidas sequencialmente. Neste caso, o tempo de espera entre uma tarefa de conclusão e a próxima a partir dependerá da disponibilidade de recursos no sistema.

Para obter informações detalhadas e exemplos que mostrem como escalar unidades de codificação, consulte [este](media-services-scale-media-processing-overview.md) tópico.

## <a name="next-step"></a>Passo seguinte

[Unidades de codificação de escala](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

