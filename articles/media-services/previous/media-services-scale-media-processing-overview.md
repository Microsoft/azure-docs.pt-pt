---
title: Descrição geral de processamento de multimédia do dimensionamento | Documentos da Microsoft
description: Este tópico é uma visão geral do dimensionamento processamento de multimédia com os serviços de multimédia do Azure.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 3a88c29739faca33794ce10de895c0439f007532
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294234"
---
# <a name="scaling-media-processing-overview"></a>Descrição geral de processamento de multimédia do dimensionamento 
Esta página fornece uma descrição geral de como e porquê dimensionar o processamento de multimédia. 

## <a name="overview"></a>Descrição geral
As contas dos Serviços de Multimédia estão associadas a um Tipo de Unidade Reservada, que determina a velocidade do processamento das suas tarefas de processamento de mulitmédia. Pode escolher de entre os seguintes tipos de unidade reservada: **S1**, **S2**, ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**. Para obter mais informações, consulte a [tipos de unidades reservadas](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Além de especificar o tipo de unidade reservada, pode especificar para o aprovisionamento da sua conta com unidades reservadas. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. Por exemplo, se a sua conta tiver cinco unidades reservadas, em seguida, as tarefas de cinco multimédia serão executados em simultâneo, desde como existem tarefas a serem processados. As tarefas restantes irão aguardar na fila e irão obter escolhidas para processamento, sequencialmente, quando uma tarefa em execução for concluída. Se uma conta não tem quaisquer unidades reservadas aprovisionadas, em seguida, tarefas serão detetadas sequencialmente. Neste caso, o tempo de espera entre uma tarefa de terminar e iniciar a seguir um dependerá da disponibilidade de recursos no sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolher entre tipos diferentes de unidade reservada
A tabela seguinte ajuda-o a tomar uma decisão, ao escolher entre diferentes velocidades de codificação. Ele também fornece alguns casos de benchmark em [um vídeo que pode baixar](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) para executar seus próprios testes:

|Tipo do RU|Cenário|Resultados de exemplo para o [7 min 1080p vídeo](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Codificação de velocidade de transmissão única. <br/>Ficheiros em SD ou abaixo de resoluções, não um prazo confidenciais, baixo custo.|Codificação a ser o único ficheiro MP4 de velocidade de transmissão SD resolução usando "H264 taxa de bits única de velocidade de transmissão SD 16 x 9" leva de 10 minutos.|
| **S2**|Velocidade de transmissão única e a codificação de velocidade de transmissão múltiplas.<br/>Utilização normal para a codificação SD e HD.|Com "H264 taxa de bits única 720p" predefinição de codificação demora cerca de 8 minutos.<br/><br/>Codificação com "h264 taxa de bits múltiplas 720p" configuração predefinida demora, aproximadamente 16.8 minutos.|
| **S3**|Velocidade de transmissão única e a codificação de velocidade de transmissão múltiplas.<br/>Vídeos de resolução do HD e 4K completos. Tempo de codificação de face àquilo confidencial, mais rápido.|Com "H264 taxa de bits única 1080p" predefinição de codificação demora cerca de 4 minutos.<br/><br/>Codificação com "H264 Multiple Bitrate 1080p" configuração predefinida demora, aproximadamente 8 minutos.|

## <a name="considerations"></a>Considerações
> [!IMPORTANT]
> Reveja as considerações descritas nesta secção.  
> 
> 

* Para as tarefas de análise de áudio e análise de vídeo que são acionadas por serviços de multimédia v3 ou Video Indexer, é altamente recomendável o tipo de unidade de S3.
* Se utilizar o conjunto partilhado, ou seja, sem quaisquer unidades reservadas, em seguida, as tarefas de codificar tem o mesmo desempenho como com Urs S1. No entanto, não existe nenhum limite superior para o tempo de que suas tarefas podem gastar no estado de fila e, em qualquer momento, no máximo apenas uma tarefa será executado.

## <a name="billing"></a>Faturação

São-lhe cobrados os minutos efetivos de utilização de Unidades Reservadas de Multimédia. Para obter uma explicação detalhada, consulte a secção de FAQ do [preços de serviços de multimédia](https://azure.microsoft.com/pricing/details/media-services/) página.   

## <a name="quotas-and-limitations"></a>Quotas e limitações
Para obter informações sobre as quotas e limitações e como abrir um pedido de suporte, consulte [Quotas e limitações](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Passo seguinte
Obter a tarefa de processamento de multimédia dimensionamento com uma das seguintes tecnologias: 

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

