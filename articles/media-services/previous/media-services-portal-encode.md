---
title: Codificar um ativo utilizando o Media Encoder Standard no portal Azure | Microsoft Docs
description: Este tutorial acompanha-o através dos passos de codificação de um ativo utilizando o Media Encoder Standard no portal Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5f4bb3c9b23ffd68939f1088b1252c6e31c1dad7
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010512"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Encode an asset by using Media Encoder Standard in the Azure portal (Codificar um recurso com o Media Encoder Standard no portal do Azure)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para mais detalhes, consulte [o julgamento gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/) 
> 
> 

Um dos cenários mais comuns no trabalho com a Azure Media Services é a entrega de bitrates adaptativos aos seus clientes. Os Media Services suportam as seguintes tecnologias de streaming de bitrate adaptativas: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming e Dynamic Adaptive Streaming over HTTP (DASH, também chamado MPEG-DASH). Para preparar os seus vídeos para o streaming de bitrate adaptativo, primeiro codificar o seu vídeo de origem como ficheiros multi-bitrate. Pode utilizar o Media Encoder Standard para codificar os seus vídeos.  

Os Serviços de Multimédia proporcionam-lhe empacotamento dinâmico. Com uma embalagem dinâmica, pode entregar os seus MP4s multi-bitrate em HLS, Smooth Streaming e MPEG-DASH, sem reembalar nestes formatos de streaming. Quando utilizar embalagens dinâmicas, pode armazenar e pagar os ficheiros em formato de armazenamento único. A Media Services constrói e serve a resposta adequada com base no pedido de um cliente.

Para tirar partido do empacotamento dinâmico, tem de codificar o ficheiro de origem num conjunto de ficheiros MP4 de velocidade de transmissão múltipla. Os passos de codificação são demonstrados mais tarde neste artigo.

Para aprender a escalar o processamento de mídia, consulte [o processamento de mídia scale utilizando o portal Azure](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Codificar no portal Azure

Para codificar o seu conteúdo utilizando o Media Encoder Standard:

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Selecione   >  **Configurações Ativos**. Selecione o elemento que pretende codificar.
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

