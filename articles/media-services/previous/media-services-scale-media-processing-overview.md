---
title: Visão geral do processamento de mídia de dimensionamento | Microsoft Docs
description: Este tópico é uma visão geral de como dimensionar o processamento de mídia com os serviços de mídia do Azure.
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
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102902"
---
# <a name="scaling-media-processing-overview"></a>Visão geral do processamento de mídia em escala 
Esta página fornece uma visão geral de como e por que dimensionar o processamento de mídia. 

## <a name="overview"></a>Descrição geral
As contas dos Serviços de Multimédia estão associadas a um Tipo de Unidade Reservada, que determina a velocidade do processamento das suas tarefas de processamento de mulitmédia. Você pode escolher entre os seguintes tipos de unidade reservada: **S1**, **S2**ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**. Para obter mais informações, consulte os [tipos de unidade reservadas](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Além de especificar o tipo de unidade reservada, você pode especificar para provisionar sua conta com unidades reservadas. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. Por exemplo, se sua conta tiver cinco unidades reservadas, cinco tarefas de mídia serão executadas simultaneamente, desde que haja tarefas a serem processadas. As tarefas restantes aguardarão na fila e serão selecionadas para o processamento sequencialmente quando uma tarefa em execução for concluída. Se uma conta não tiver nenhuma unidade reservada provisionada, as tarefas serão selecionadas sequencialmente. Nesse caso, o tempo de espera entre a conclusão de uma tarefa e a próxima começando dependerá da disponibilidade dos recursos no sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolhendo entre diferentes tipos de unidade reservada
A tabela a seguir ajuda você a tomar uma decisão ao escolher entre diferentes velocidades de codificação. Ele também fornece alguns casos de benchmark em [um vídeo que você pode baixar](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) para executar seus próprios testes:

|Tipo de RU|Cenário|Resultados de exemplo para o [vídeo de 8 min 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Codificação de taxa de bits única. <br/>Arquivos em SD ou abaixo das resoluções, não diferenciando tempo, baixo custo.|A codificação para o arquivo MP4 de resolução SD de taxa de bits única usando "H264 taxa de bits única SD 16x9" leva cerca de 7 minutos.|
| **S2**|Taxa de bits única e codificação de taxa de bits múltipla.<br/>Uso normal para codificação SD e HD.|A codificação com a predefinição "H264 taxa de bits única 720p" leva cerca de 6 minutos.<br/><br/>A codificação com a predefinição "H264 taxas de bits múltiplas 720p" leva cerca de 12 minutos.|
| **S3**|Taxa de bits única e codificação de taxa de bits múltipla.<br/>Vídeos completos de resolução de HD e 4K. Codificação de retorno sensível à hora e mais rápida.|A codificação com a predefinição "H264 taxa de bits única 1080p" leva aproximadamente 3 minutos.<br/><br/>A codificação com a predefinição "H264 taxas de bits múltiplas 1080p" leva aproximadamente 8 minutos.|

## <a name="considerations"></a>Considerações
> [!IMPORTANT]
> Examine as considerações descritas nesta seção.  
> 
> 

* Para a análise de áudio e os trabalhos de análise de vídeo disparados pelos serviços de mídia V3 ou Video Indexer, o tipo de unidade S3 é altamente recomendado.
* Se estiver usando o pool compartilhado, ou seja, sem nenhuma unidade reservada, as tarefas de codificação terão o mesmo desempenho que com o RUs S1. No entanto, não há nenhum limite superior ao tempo que suas tarefas podem gastar em estado enfileirado e a qualquer momento, no máximo uma tarefa será executada.

## <a name="billing"></a>Faturação

Você é cobrado com base em quantos minutos as unidades reservadas de mídia são provisionadas em sua conta. Isso ocorre independentemente de qualquer trabalho em execução em sua conta. Para obter uma explicação detalhada, consulte a secção de FAQ do [preços de serviços de multimédia](https://azure.microsoft.com/pricing/details/media-services/) página.   

## <a name="quotas-and-limitations"></a>Quotas e limitações
Para obter informações sobre cotas e limitações e como abrir um tíquete de suporte, consulte [cotas e limitações](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Passo seguinte
Obtenha a tarefa de processamento de mídia em escala com uma destas tecnologias: 

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

