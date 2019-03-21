---
title: Codificar um recurso com o Media Encoder Standard no portal do Azure | Documentos da Microsoft
description: Este tutorial explica-lhe os passos da codificação de um recurso com o Media Encoder Standard no portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 90190f426419e65bd580b9004ae76a2c6b0c12e2
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258484"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Codificar um recurso com o Media Encoder Standard no portal do Azure

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Um dos cenários mais comuns ao trabalhar com os serviços de multimédia do Azure é a entrega de velocidade de transmissão adaptável, transmissão em fluxo para os seus clientes. Os Media Services suportam a velocidade de transmissão adaptável seguinte tecnologias de transmissão em fluxo: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming e Dynamic Adaptive Streaming através de HTTP (DASH, também chamado de MPEG-DASH). Para preparar os seus vídeos para transmissão em fluxo de velocidade de transmissão adaptável, primeiro a codificar o seu vídeo de origem como arquivos de velocidade de transmissão. Pode utilizar o Azure Media Encoder Standard para codificar seus vídeos.  

Os Serviços de Multimédia proporcionam-lhe empacotamento dinâmico. Com o empacotamento dinâmico, pode fornecer dos seus MP4s de velocidade de transmissão em HLS, Smooth Streaming e MPEG-DASH, sem reempacotamento estes formatos de transmissão em fluxo. Quando utiliza o empacotamento dinâmico, pode armazenar e pagar pelos ficheiros num formato de armazenamento única. Serviços de multimédia criam e entregam a resposta adequada com base na solicitação de um cliente.

Para tirar partido do empacotamento dinâmico, tem de codificar o ficheiro de origem num conjunto de ficheiros MP4 de velocidade de transmissão múltipla. Os passos da codificação são demonstrados mais à frente neste artigo.

Para saber como dimensionar o processamento de multimédia, veja [dimensionar com o portal do Azure de processamento de multimédia](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Codificar no portal do Azure

Para codificar o conteúdo com o Media Encoder Standard:

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Selecione **Definições** > **Elementos**. Selecione o elemento que pretende codificar.
3. Selecione o botão **Codificar**.
4. No painel **Codificar um elemento**, selecione o processador **Media Encoder Standard** e uma predefinição. Para obter informações sobre as predefinições, veja [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Gerar automaticamente uma escala de velocidade de transmissão) e [Task presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Predefinições de tarefas para o Media Encoder Standard). É importante escolher a predefinição que funcionará melhor no seu vídeo de entrada. Por exemplo, se sabe que o seu vídeo de entrada tem uma resolução de 1920 &#215; 1080 pixels, pode escolher a predefinição **H264 Multiple Bitrate 1080p**. Se o vídeo for de baixa resolução (640 &#215; 360), não deve utilizar a predefinição **H264 Multiple Bitrate 1080p**.
   
   Para o ajudar a gerir os seus recursos, pode editar o nome do elemento de saída e o nome da tarefa.
   
   ![Codificar elementos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Selecione **Criar**.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes
* [Monitorizar o progresso da tarefa de codificação](media-services-portal-check-job-progress.md) no portal do Azure.  

