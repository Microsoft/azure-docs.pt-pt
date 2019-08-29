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
ms.date: 08/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 13fa733417558ab8be9ff1e5a9f1e484fb40f445
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102936"
---
# <a name="scaling-media-processing"></a>Dimensionar processamento de multimédia

Serviços de multimédia do Azure permite-lhe dimensionar o processamento de multimédia na sua conta através da gestão de unidades reservadas de multimédia (MRUs). MRUs determinam a velocidade com que suas tarefas de processamento de mídia são processadas. Você pode escolher entre os seguintes tipos de unidade reservada: **S1**, **S2**ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**. 

Além de especificar o tipo de unidade reservada, você pode especificar para provisionar sua conta com unidades reservadas. O número de unidades reservadas aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta. Por exemplo, se sua conta tiver cinco unidades reservadas, cinco tarefas de mídia serão executadas simultaneamente, desde que haja tarefas a serem processadas. As tarefas restantes aguardarão na fila e serão selecionadas para o processamento sequencialmente quando uma tarefa em execução for concluída. Se uma conta não tiver nenhuma unidade reservada provisionada, as tarefas serão selecionadas sequencialmente. Nesse caso, o tempo de espera entre a conclusão de uma tarefa e a próxima começando dependerá da disponibilidade dos recursos no sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolhendo entre diferentes tipos de unidade reservada

A tabela a seguir ajuda você a tomar uma decisão ao escolher entre diferentes velocidades de codificação. Ele também fornece alguns casos de benchmark em [um vídeo que você pode baixar](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) para executar seus próprios testes:

|Tipo de RU|Cenário|Resultados de exemplo para o [vídeo de 8 min 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Codificação de taxa de bits única. <br/>Arquivos em SD ou abaixo das resoluções, não diferenciando tempo, baixo custo.|A codificação para o arquivo MP4 de resolução SD de taxa de bits única usando "H264 taxa de bits única SD 16x9" leva cerca de 7 minutos.|
| **S2**|Taxa de bits única e codificação de taxa de bits múltipla.<br/>Uso normal para codificação SD e HD.|A codificação com a predefinição "H264 taxa de bits única 720p" leva cerca de 6 minutos.<br/><br/>A codificação com a predefinição "H264 taxas de bits múltiplas 720p" leva cerca de 12 minutos.|
| **S3**|Taxa de bits única e codificação de taxa de bits múltipla.<br/>Vídeos completos de resolução de HD e 4K. Codificação de retorno sensível à hora e mais rápida.|A codificação com a predefinição "H264 taxa de bits única 1080p" leva aproximadamente 3 minutos.<br/><br/>A codificação com a predefinição "H264 taxas de bits múltiplas 1080p" leva aproximadamente 8 minutos.|

## <a name="considerations"></a>Considerações

* Para a análise de áudio e os trabalhos de análise de vídeo disparados pelos serviços de mídia V3 ou Video Indexer, o tipo de unidade S3 é altamente recomendado.
* Se estiver usando o pool compartilhado, ou seja, sem nenhuma unidade reservada, as tarefas de codificação terão o mesmo desempenho que com o RUs S1. No entanto, não há nenhum limite superior ao tempo que suas tarefas podem gastar em estado enfileirado e a qualquer momento, no máximo uma tarefa será executada.

O restante do artigo mostra como usar a [CLI do Media Services V3](https://aka.ms/ams-v3-cli-ref) para dimensionar o MRUs.

> [!NOTE]
> Para a análise de áudio e tarefas de análise de vídeo que são acionados por serviços de multimédia v3 ou Video Indexer, recomenda-se elevada para aprovisionar a sua conta com 10 MRUs de S3. Se precisar de mais de 10 S3 MRUs, abra um pedido de suporte através do [portal do Azure](https://portal.azure.com/).
>
> No momento, você não pode usar o portal do Azure para gerenciar outros recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Unidades com a CLI reservadas de multimédia de dimensionamento

Execute o comando `mru`.

O seguinte procedimento [mru de contas do az ams](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) comando conjuntos de unidades reservadas de multimédia "amsaccount" conta utilizando o **contagem** e **tipo** parâmetros.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Faturação

Você é cobrado com base em quantos minutos as unidades reservadas de mídia são provisionadas em sua conta. Isso ocorre independentemente de qualquer trabalho em execução em sua conta. Para obter uma explicação detalhada, consulte a secção de FAQ do [preços de serviços de multimédia](https://azure.microsoft.com/pricing/details/media-services/) página.   

## <a name="next-step"></a>Passo seguinte

[Analisar vídeos](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Consulte também

* [Quotas e limitações](limits-quotas-constraints.md)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
