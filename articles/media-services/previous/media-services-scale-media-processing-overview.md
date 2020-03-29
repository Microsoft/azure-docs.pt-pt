---
title: Visão geral do processamento de meios de comunicação de escala [ Microsoft Docs
description: Este tema é uma visão geral da escala de Media Processing com a Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: 780d3ab5047bff321d0c554880ba2995bcf25524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102902"
---
# <a name="scaling-media-processing-overview"></a>Scaling Media Processing overview (Descrição geral do dimensionamento do processamento de multimédia) 
Esta página dá uma visão geral de como e porquê escalar o processamento dos meios de comunicação. 

## <a name="overview"></a>Descrição geral
As contas dos Serviços de Multimédia estão associadas a um Tipo de Unidade Reservada, que determina a velocidade do processamento das suas tarefas de processamento de mulitmédia. Pode escolher entre os seguintes tipos de unidades reservados: **S1,** **S2,** ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**. Para mais informações, consulte os [Tipos de Unidade Reservado](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Além de especificar o tipo de unidade reservado, pode especificar para fornecer a sua conta com unidades reservadas. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. Por exemplo, se a sua conta tiver cinco unidades reservadas, então cinco tarefas de mídia estarão em execução simultaneamente, desde que existam tarefas a serem processadas. As restantes tarefas vão esperar na fila e serão apanhadas para processamento sequencialmente quando uma tarefa de execução terminar. Se uma conta não tiver unidades reservadas, as tarefas serão recolhidas sequencialmente. Neste caso, o tempo de espera entre uma tarefa de conclusão e a próxima a partir dependerá da disponibilidade de recursos no sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolha entre diferentes tipos de unidades reservadas
A tabela seguinte ajuda-o a tomar uma decisão ao escolher entre diferentes velocidades de codificação. Também fornece alguns casos de referência [num vídeo que pode descarregar](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) para realizar os seus próprios testes:

|Tipo RU|Cenário|Exemplo resultados para o vídeo de [7 min 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Codificação única. <br/>Ficheiros em SD ou abaixo de resoluções, não sensíveis ao tempo, baixo custo.|A codificação para um ficheiro MP4 de resolução sd única de bitrate utilizando "H264 Single Bitrate SD 16x9" demora cerca de 7 minutos.|
| **S2**|Codificação de bitrate único e múltiplo bitrate.<br/>Utilização normal tanto para codificação de SD como HD.|A codificação com predefinição "H264 Single Bitrate 720p" demora cerca de 6 minutos.<br/><br/>A codificação com predefinição "H264 Multiple Bitrate 720p" demora cerca de 12 minutos.|
| **S3**|Codificação de bitrate único e múltiplo bitrate.<br/>Vídeos completos de resolução de HD e 4K. Sensível ao tempo, uma reviravolta mais rápida.|A codificação com predefinição "H264 Single Bitrate 1080p" demora aproximadamente 3 minutos.<br/><br/>A codificação com predefinição "H264 Multiple Bitrate 1080p" demora aproximadamente 8 minutos.|

## <a name="considerations"></a>Considerações
> [!IMPORTANT]
> Analisar considerações descritas nesta secção.  
> 
> 

* Para os trabalhos de Análise de Áudio e Análise de Vídeo que são desencadeados por Media Services v3 ou Video Indexer, o tipo de unidade S3 é altamente recomendado.
* Se utilizar a piscina partilhada, isto é, sem unidades reservadas, então as suas tarefas codificadas têm o mesmo desempenho que com as RUs S1. No entanto, não existe um limite superior ao tempo que as suas Tarefas podem passar em estado de fila, e em qualquer momento, no máximo, apenas uma Tarefa estará em execução.

## <a name="billing"></a>Faturação

É cobrado com base no número de minutos que as Unidades Reservadas de Mídia estão aprovisionadas na sua conta. Isto ocorre independentemente de existirem algum Jobs a funcionar na sua conta. Para obter uma explicação detalhada, consulte a secção DE PERGUNTAS FAQ da página [de preços dos Serviços de Media.](https://azure.microsoft.com/pricing/details/media-services/)   

## <a name="quotas-and-limitations"></a>Quotas e limitações
Para obter informações sobre quotas e limitações e como abrir um bilhete de apoio, consulte [Quotas e limitações.](media-services-quotas-and-limitations.md)

## <a name="next-step"></a>Passo seguinte
Alcance a tarefa de processamento de meios de escala com uma destas tecnologias: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Para obter a versão mais recente do Java SDK e começar a programar com o Java, veja [Introdução ao SDK do cliente de Java dos Serviços de Multimédia](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Para transferir o SDK mais recente do PHP para os Serviços de Multimédia, procure a versão 0.5.7 do pacote Microsoft/WindowAzure no [repositório Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

