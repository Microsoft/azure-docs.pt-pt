---
title: Codificar um ativo utilizando a Norma Media Encoder no portal Azure [ Microsoft Docs
description: Este tutorial acompanha-o através dos passos de codificação de um ativo utilizando o Media Encoder Standard no portal Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69542601"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Encode an asset by using Media Encoder Standard in the Azure portal (Codificar um recurso com o Media Encoder Standard no portal do Azure)

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para mais detalhes, consulte [o teste gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/) 
> 
> 

Um dos cenários mais comuns no trabalho com a Azure Media Services é entregar streaming de bitrate adaptativo aos seus clientes. Os Media Services suportam as seguintes tecnologias de streaming adaptativo: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming e Dynamic Adaptive Streaming over HTTP (DASH, também chamado MPEG-DASH). Para preparar os seus vídeos para streaming de bitrate adaptativo, primeiro codifique o seu vídeo de origem como ficheiros multibitáveis. Pode utilizar o Media Encoder Standard para codificar os seus vídeos.  

Os Serviços de Multimédia proporcionam-lhe empacotamento dinâmico. Com embalagens dinâmicas, pode entregar os seus MP4 s multibitáveis em HLS, Smooth Streaming e MPEG-DASH, sem reembalar nestes formatos de streaming. Quando utilizar embalagens dinâmicas, pode armazenar e pagar os ficheiros em formato de armazenamento único. A Media Services constrói e serve a resposta adequada com base no pedido de um cliente.

Para tirar partido do empacotamento dinâmico, tem de codificar o ficheiro de origem num conjunto de ficheiros MP4 de velocidade de transmissão múltipla. Os passos de codificação são demonstrados mais tarde neste artigo.

Para aprender a dimensionar o processamento dos meios de comunicação, consulte [o processamento de meios de escala utilizando o portal Azure](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Codificar no portal Azure

Para codificar o seu conteúdo utilizando o Media Encoder Standard:

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Selecione**Ativos**de **Definições** > . Selecione o elemento que pretende codificar.
3. Selecione o botão **Codificar**.
4. No painel **Codificar um elemento**, selecione o processador **Media Encoder Standard** e uma predefinição. Para obter informações sobre as predefinições, veja [Auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) (Gerar automaticamente uma escala de velocidade de transmissão) e [Task presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Predefinições de tarefas para o Media Encoder Standard). É importante escolher a predefinição que funcionará melhor no seu vídeo de entrada. Por exemplo, se sabe que o seu vídeo de entrada tem uma resolução de 1920 &#215; 1080 pixels, pode escolher a predefinição **H264 Multiple Bitrate 1080p**. Se o vídeo for de baixa resolução (640 &#215; 360), não deve utilizar a predefinição **H264 Multiple Bitrate 1080p**.
   
   Para o ajudar a gerir os seus recursos, pode editar o nome do elemento de saída e o nome da tarefa.
   
   ![Codificar elementos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Selecione **Criar**.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes
* [Monitorize o progresso do seu trabalho de codificação](media-services-portal-check-job-progress.md) no portal Azure.  

