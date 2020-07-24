---
title: Use o CLI para escalar unidades reservadas de mídia - Azure / Microsoft Docs
description: Este tópico mostra como usar o CLI para escalar o processamento de mídia com a Azure Media Services.
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
ms.openlocfilehash: 6715014485b227713447ce5d552cf7ba79737845
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053213"
---
# <a name="scaling-media-processing"></a>Scaling media processing (Dimensionar o processamento de multimédia)

Os Serviços de Multimédia do Azure permitem-lhe dimensionar o processamento de multimédia na sua conta através da gestão de Unidades Reservadas de Multimédia (MRUs). As MRUs determinam a rapidez com que as suas tarefas de processamento de meios são processadas. Pode escolher entre os seguintes tipos de unidade reservada: **S1,** **S2**ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**. 

Além de especificar o tipo de unidade reservada, pode especificar a disponibilização da sua conta com unidades reservadas. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. Por exemplo, se a sua conta tiver cinco unidades reservadas, então cinco tarefas de media serão executadas simultaneamente enquanto houver tarefas a serem processadas. As restantes tarefas aguardarão na fila e serão recolhidas para processamento sequencialmente quando uma tarefa de execução terminar. Se uma conta não tiver nenhuma unidade reservada a provisionada, então as tarefas serão recolhidas sequencialmente. Neste caso, o tempo de espera entre um acabamento de uma tarefa e o próximo arranque dependerá da disponibilidade de recursos no sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolher entre diferentes tipos de unidades reservadas

A tabela a seguir ajuda-o a tomar uma decisão ao escolher entre diferentes velocidades de codificação. Também fornece alguns casos de referência [num vídeo que pode descarregar](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) para realizar os seus próprios testes:

|Tipo RU|Cenário|Resultados de exemplo para o vídeo de [7 min 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Codificação de bitrate única. <br/>Ficheiros em SD ou abaixo de resoluções, não sensíveis ao tempo, baixo custo.|A codificação para um único ficheiro MP4 de resolução de bitrate sd utilizando "H264 Single Bitrate SD 16x9" demora cerca de 7 minutos.|
| **S2**|Bitrate único e múltiplas codificações bitrate.<br/>Utilização normal para codificação SD e HD.|A codificação com a predefinição "H264 Single Bitrate 720p" demora cerca de 6 minutos.<br/><br/>A codificação com a predefinição "H264 Multiple Bitrate 720p" demora cerca de 12 minutos.|
| **S3**|Bitrate único e múltiplas codificações bitrate.<br/>Vídeos de resolução Full HD e 4K. Tempo sensível, reviravolta mais rápida codificação.|A codificação com a predefinição "H264 Single Bitrate 1080p" demora aproximadamente 3 minutos.<br/><br/>A codificação com a predefinição "H264 Multiple Bitrate 1080p" demora aproximadamente 8 minutos.|

## <a name="considerations"></a>Considerações

* Para os trabalhos de Análise de Áudio e Análise de Vídeo que são desencadeados por Media Services v3 ou Video Indexer, o tipo de unidade S3 é altamente recomendado.
* Se utilizar a piscina partilhada, isto é, sem quaisquer unidades reservadas, então as suas tarefas de codificação têm o mesmo desempenho que com as RUs S1. No entanto, não há limite superior ao tempo que as suas Tarefas podem passar em estado de fila, e em qualquer momento, no máximo, apenas uma Tarefa estará em execução.

O resto do artigo mostra como usar os [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) para escalar as MRUs.

> [!NOTE]
> Para as tarefas de Análise de áudio e de Análise de vídeo acionadas por Serviços de Multimédia v3 ou Video Indexer, é altamente recomendado que aprovisione a sua conta com 10 MRUs de S3. Se precisar de mais de 10 MRUs S3, abra um bilhete de apoio utilizando o [portal Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Unidades reservadas para mídia de escala com CLI

Execute o comando `mru`.

O comando [mru da conta AZ AMS](/cli/azure/ams/account/mru?view=azure-cli-latest) define As Unidades Reservadas de Mídia na conta "amsaccount" utilizando os parâmetros **de contagem** e **tipo.**

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Faturação

É cobrado com base no número de minutos que as Unidades Reservadas de Mídia estão a ser adsteadas na sua conta. Isto ocorre independentemente de existir algum Jobs a decorrer na sua conta. Para obter uma explicação detalhada, consulte a secção de PERGUNTAS Frequentes da página [de preços dos Serviços de Comunicação Social.](https://azure.microsoft.com/pricing/details/media-services/)   

## <a name="next-step"></a>Passo seguinte

[Analisar vídeos](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Ver também

* [Quotas e limites](limits-quotas-constraints.md)
* [CLI do Azure](/cli/azure/ams?view=azure-cli-latest)
