---
title: Codificar um ativo usando Media Encoder Standard no portal do Azure | Microsoft Docs
description: Este tutorial orienta você pelas etapas de codificação de um ativo usando Media Encoder Standard no portal do Azure.
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
ms.openlocfilehash: 7c147d99eaabee70316521d1f2bdc41933162ab1
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542601"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Codificar um ativo usando Media Encoder Standard no portal do Azure

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Um dos cenários mais comuns no trabalho com os serviços de mídia do Azure é fornecer streaming de taxa de bits adaptável para seus clientes. Os serviços de mídia oferecem suporte às seguintes tecnologias de streaming de taxa de bits adaptável: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming e streaming adaptável dinâmico sobre HTTP (DASH, também chamado de MPEG-DASH). Para preparar seus vídeos para streaming de taxa de bits adaptável, primeiro Codifique o vídeo de origem como arquivos de múltiplas taxas de bits. Você pode usar Media Encoder Standard para codificar seus vídeos.  

Os Serviços de Multimédia proporcionam-lhe empacotamento dinâmico. Com o empacotamento dinâmico, você pode entregar suas MP4s de múltiplas taxas de bits em HLS, Smooth Streaming e MPEG-DASH, sem reempacotar nesses formatos de streaming. Ao usar o empacotamento dinâmico, você pode armazenar e pagar pelos arquivos no formato de armazenamento único. Os serviços de mídia criam e servem a resposta apropriada com base na solicitação de um cliente.

Para tirar partido do empacotamento dinâmico, tem de codificar o ficheiro de origem num conjunto de ficheiros MP4 de velocidade de transmissão múltipla. As etapas de codificação são demonstradas posteriormente neste artigo.

Para saber como dimensionar o processamento de mídia, confira [escala de processamento de mídia usando o portal do Azure](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Codificar na portal do Azure

Para codificar seu conteúdo usando Media Encoder Standard:

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
* [Monitore o progresso do seu trabalho de codificação](media-services-portal-check-job-progress.md) no portal do Azure.  

