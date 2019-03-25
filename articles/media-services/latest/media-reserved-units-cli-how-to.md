---
title: Utilizar a CLI para dimensionar unidades reservadas de multimédia - Azure | Documentos da Microsoft
description: Este tópico mostra como utilizar a CLI para dimensionar processamento de multimédia com serviços de multimédia do Azure.
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
ms.date: 03/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3f0d3c5748afaac2544232fc1ff84316d9eb7347
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351070"
---
# <a name="scaling-media-processing"></a>Dimensionar processamento de multimédia

Serviços de multimédia do Azure permite-lhe dimensionar o processamento de multimédia na sua conta através da gestão de unidades reservadas de multimédia (MRUs). MRUs determinar a velocidade com que o suporte de dados de tarefas de processamento é processados. Pode escolher de entre os seguintes tipos de unidade reservada: **S1**, **S2**, ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**. 

Além de especificar o tipo de unidade reservada, pode especificar para o aprovisionamento da sua conta com unidades reservadas. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. Por exemplo, se a sua conta tiver cinco unidades reservadas, em seguida, as tarefas de cinco multimédia serão executados em simultâneo, desde como existem tarefas a serem processados. As tarefas restantes irão aguardar na fila e irão obter escolhidas para processamento, sequencialmente, quando uma tarefa em execução for concluída. Se uma conta não tem quaisquer unidades reservadas aprovisionadas, em seguida, tarefas serão detetadas sequencialmente. Neste caso, o tempo de espera entre uma tarefa de terminar e iniciar a seguir um dependerá da disponibilidade de recursos no sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolher entre tipos diferentes de unidade reservada

A tabela seguinte ajuda-o a tomar uma decisão, ao escolher entre diferentes velocidades de codificação. Ele também fornece alguns casos de benchmark em [um vídeo que pode baixar](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) para executar seus próprios testes:

|Tipo do RU|Cenário|Resultados de exemplo para o [7 min 1080p vídeo](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Codificação de velocidade de transmissão única. <br/>Ficheiros em SD ou abaixo de resoluções, não um prazo confidenciais, baixo custo.|Codificação a ser o único ficheiro MP4 de velocidade de transmissão SD resolução usando "H264 taxa de bits única de velocidade de transmissão SD 16 x 9" leva de 10 minutos.|
| **S2**|Velocidade de transmissão única e a codificação de velocidade de transmissão múltiplas.<br/>Utilização normal para a codificação SD e HD.|Com "H264 taxa de bits única 720p" predefinição de codificação demora cerca de 8 minutos.<br/><br/>Codificação com "h264 taxa de bits múltiplas 720p" configuração predefinida demora, aproximadamente 16.8 minutos.|
| **S3**|Velocidade de transmissão única e a codificação de velocidade de transmissão múltiplas.<br/>Vídeos de resolução do HD e 4K completos. Tempo de codificação de face àquilo confidencial, mais rápido.|Com "H264 taxa de bits única 1080p" predefinição de codificação demora cerca de 4 minutos.<br/><br/>Codificação com "H264 Multiple Bitrate 1080p" configuração predefinida demora, aproximadamente 8 minutos.|

## <a name="considerations"></a>Considerações

* Para as tarefas de análise de áudio e análise de vídeo que são acionadas por serviços de multimédia v3 ou Video Indexer, é altamente recomendável o tipo de unidade de S3.
* Se utilizar o conjunto partilhado, ou seja, sem quaisquer unidades reservadas, em seguida, as tarefas de codificar tem o mesmo desempenho como com Urs S1. No entanto, não existe nenhum limite superior para o tempo de que suas tarefas podem gastar no estado de fila e, em qualquer momento, no máximo apenas uma tarefa será executado.

O resto do artigo mostra como usar [dos serviços de multimédia v3 CLI](https://aka.ms/ams-v3-cli-ref) Dimensionar MRUs.

> [!NOTE]
> Para a análise de áudio e tarefas de análise de vídeo que são acionados por serviços de multimédia v3 ou Video Indexer, recomenda-se elevada para aprovisionar a sua conta com 10 MRUs de S3. Se precisar de mais de 10 S3 MRUs, abra um pedido de suporte através do [portal do Azure](https://portal.azure.com/).
>
> Atualmente, não é possível utilizar o portal do Azure para gerir outros recursos de v3. Utilize o [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), ou um suportadas [SDKs](developers-guide.md).

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Unidades com a CLI reservadas de multimédia de dimensionamento

Execute o comando `mru`.

O seguinte procedimento [mru de contas do az ams](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) comando conjuntos de unidades reservadas de multimédia "amsaccount" conta utilizando o **contagem** e **tipo** parâmetros.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Faturação

Cobrado consoante o número, o tipo e o período de tempo que MRUs são aprovisionados na sua conta. Aplicam-se custos se ou não executar quaisquer tarefas. Para obter uma explicação detalhada, consulte a secção de FAQ do [preços de serviços de multimédia](https://azure.microsoft.com/pricing/details/media-services/) página.   

## <a name="next-step"></a>Passo seguinte

[Analisar vídeos](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Consulte também

* [Quotas e limitações](limits-quotas-constraints.md)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
