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
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9f0a7425fc09d391828a748832f662f02c6022cf
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970790"
---
# <a name="scaling-media-processing"></a>Dimensionar processamento de multimédia

Os Serviços de Multimédia do Azure permitem-lhe dimensionar o processamento de multimédia na sua conta através da gestão de Unidades Reservadas de Multimédia (MRUs). As MrUs determinam a rapidez com que as suas tarefas de processamento de meios de comunicação são processadas. Pode escolher de entre os tipos de unidades reservadas **S1**, **S2** ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**. 

Além de especificar o tipo de unidade reservado, pode especificar para fornecer a sua conta com unidades reservadas. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. Por exemplo, se a sua conta tiver cinco unidades reservadas, então cinco tarefas de mídia estarão em execução simultaneamente, desde que existam tarefas a serem processadas. As restantes tarefas vão esperar na fila e serão apanhadas para processamento sequencialmente quando uma tarefa de execução terminar. Se uma conta não tiver unidades reservadas, as tarefas serão recolhidas sequencialmente. Neste caso, o tempo de espera entre uma tarefa de conclusão e a próxima a partir dependerá da disponibilidade de recursos no sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolha entre diferentes tipos de unidades reservadas

A tabela seguinte ajuda-o a tomar uma decisão ao escolher entre diferentes velocidades de codificação. Também fornece alguns casos de referência [num vídeo que pode descarregar](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) para realizar os seus próprios testes:

|Tipo RU|Cenário|Exemplo resultados para o vídeo de [7 min 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Codificação única. <br/>Ficheiros em SD ou abaixo de resoluções, não sensíveis ao tempo, baixo custo.|A codificação para um ficheiro MP4 de resolução sd única de bitrate utilizando "H264 Single Bitrate SD 16x9" demora cerca de 7 minutos.|
| **S2**|Codificação de bitrate único e múltiplo bitrate.<br/>Utilização normal tanto para codificação de SD como HD.|A codificação com predefinição "H264 Single Bitrate 720p" demora cerca de 6 minutos.<br/><br/>A codificação com predefinição "H264 Multiple Bitrate 720p" demora cerca de 12 minutos.|
| **S3**|Codificação de bitrate único e múltiplo bitrate.<br/>Vídeos completos de resolução de HD e 4K. Sensível ao tempo, uma reviravolta mais rápida.|A codificação com predefinição "H264 Single Bitrate 1080p" demora aproximadamente 3 minutos.<br/><br/>A codificação com predefinição "H264 Multiple Bitrate 1080p" demora aproximadamente 8 minutos.|

## <a name="considerations"></a>Considerações

* Para os trabalhos de Análise de Áudio e Análise de Vídeo que são desencadeados por Media Services v3 ou Video Indexer, o tipo de unidade S3 é altamente recomendado.
* Se utilizar a piscina partilhada, isto é, sem unidades reservadas, então as suas tarefas codificadas têm o mesmo desempenho que com as RUs S1. No entanto, não existe um limite superior ao tempo que as suas Tarefas podem passar em estado de fila, e em qualquer momento, no máximo, apenas uma Tarefa estará em execução.

O resto do artigo mostra como usar os [Serviços de Media v3 CLI](https://aka.ms/ams-v3-cli-ref) para escalar as MrUs.

> [!NOTE]
> Para as tarefas de Análise de áudio e de Análise de vídeo acionadas por Serviços de Multimédia v3 ou Video Indexer, é altamente recomendado que aprovisione a sua conta com 10 MRUs de S3. Se precisar de mais de 10 MrUs S3, abra um bilhete de apoio utilizando o [portal Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services.](create-account-cli-how-to.md)

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Unidades com a CLI reservadas de multimédia de dimensionamento

Execute o comando `mru`.

A [conta az ams](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) seguinte si põe unidades reservadas de mídia na conta "amsaccount" utilizando os parâmetros **de contagem** e **tipo.**

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Faturação

É cobrado com base no número de minutos que as Unidades Reservadas de Mídia estão aprovisionadas na sua conta. Isto ocorre independentemente de existirem algum Jobs a funcionar na sua conta. Para obter uma explicação detalhada, consulte a secção DE PERGUNTAS FAQ da página [de preços dos Serviços de Media.](https://azure.microsoft.com/pricing/details/media-services/)   

## <a name="next-step"></a>Passo seguinte

[Analisar vídeos](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Consulte também

* [Quotas e limitações](limits-quotas-constraints.md)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
