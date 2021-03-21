---
title: Visão geral das unidades reservadas aos meios de comunicação social | Microsoft Docs
description: Este artigo é uma visão geral do dimensionamento do processamento de mídia com a Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d0692996c27f969ffc90078db2ddcc849ee15ab1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012722"
---
# <a name="media-reserved-units"></a>Unidades reservadas de multimédia

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

A Azure Media Services permite-lhe escalar o processamento de meios de comunicação através da gestão de Unidades Reservadas de Mídia (MRUs). Uma MRU fornece capacidade de computação adicional necessária para os meios de codificação. O número de MRUs determina a rapidez com que as suas tarefas de media são processadas e quantas tarefas mediáticas podem ser processadas simultaneamente numa conta. Por exemplo, se a sua conta tiver cinco MRUs e houver tarefas a serem processadas, então cinco tarefas mediáticas podem ser executadas simultaneamente. Quaisquer tarefas restantes serão recolhidas e podem ser recolhidas para processamento sequencialmente quando uma tarefa em execução terminar. Cada MRU que fornece resulta numa reserva de capacidade, mas não lhe fornece recursos dedicados. Em tempos de procura extremamente elevada, todas as suas MrUs podem não começar a processar imediatamente.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolher entre diferentes tipos de unidades reservadas

A tabela a seguir ajuda-o a tomar uma decisão ao escolher entre diferentes velocidades de codificação.  Mostra a duração da codificação durante um vídeo de 7 minutos e 1080p, dependendo da ressonância magnética utilizada.

|Tipo RU|Scenario|Resultados de exemplo para o vídeo de 7 min 1080p |
|---|---|---|
| **S1**|Codificação de bitrate única. <br/>Ficheiros em SD ou abaixo de resoluções, não sensíveis ao tempo, baixo custo.|A codificação para um único ficheiro MP4 de resolução de bitrate sd utilizando "H264 Single Bitrate SD 16x9" demora cerca de 7 minutos.|
| **S2**|Bitrate único e múltiplas codificações bitrate.<br/>Utilização normal para codificação SD e HD.|A codificação com a predefinição "H264 Single Bitrate 720p" demora cerca de 6 minutos.<br/><br/>A codificação com a predefinição "H264 Multiple Bitrate 720p" demora cerca de 12 minutos.|
| **S3**|Bitrate único e múltiplas codificações bitrate.<br/>Vídeos de resolução Full HD e 4K. Tempo sensível, reviravolta mais rápida codificação.|A codificação com a predefinição "H264 Single Bitrate 1080p" demora aproximadamente 3 minutos.<br/><br/>A codificação com a predefinição "H264 Multiple Bitrate 1080p" demora aproximadamente 8 minutos.|

> [!NOTE]
> Se não forte mru's para a sua conta, as suas tarefas de media serão processadas com o desempenho de uma MRU S1 e as tarefas serão recolhidas sequencialmente. Nenhuma capacidade de processamento é reservada, pelo que o tempo de espera entre um acabamento de uma tarefa e o próximo arranque dependerá da disponibilidade de recursos no sistema.

## <a name="considerations"></a>Considerações

* Para trabalhos de Análise de Áudio e Análise de Vídeo que são desencadeados por Media Services v3 ou Video Indexer, é altamente recomendado a provisionação da conta com dez unidades S3. Se precisar de mais de 10 MRUs S3, abra um bilhete de apoio utilizando o [portal Azure](https://portal.azure.com/).
* Para tarefas de codificação que não têm MRUs, não há limite superior ao tempo que as suas tarefas podem passar em estado de fila, e no máximo apenas uma tarefa estará a decorrer de cada vez.

## <a name="billing"></a>Faturação

É cobrado com base no número de minutos que as Unidades Reservadas de Mídia estão a ser adsteadas na sua conta. Isto ocorre independentemente de existir algum Jobs a decorrer na sua conta. Para obter uma explicação detalhada, consulte a secção de PERGUNTAS Frequentes da página [de preços dos Serviços de Comunicação Social.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="quotas-and-limitations"></a>Quotas e limitações

Para obter informações sobre quotas e limitações e como abrir um bilhete de apoio, consulte [Quotas e limitações.](media-services-quotas-and-limitations.md)

## <a name="next-steps"></a>Passos seguintes

Experimente o processamento de mídia de escala com uma destas tecnologias:

[.NET](media-services-dotnet-encoding-units.md) 
 [Portal](media-services-portal-scale-media-processing.md) 
 [DESCANSE](/rest/api/media/operations/encodingreservedunittype) 
 [Rio Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples) 
 [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)