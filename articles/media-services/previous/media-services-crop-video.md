---
title: Como cortar vídeos com o Media Encoder Standard-Azure | Microsoft Docs
description: O corte é o processo de seleção de uma janela retangular dentro do quadro de vídeo e a codificação apenas dos pixels dentro dessa janela. Este artigo demonstra como cortar vídeos com Media Encoder Standard.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 059816284e39c65bb772bd02f066d73da624722f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74887769"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Recortar vídeos com o Media Encoder Standard  

Você pode usar Media Encoder Standard (MES) para cortar o vídeo de entrada. O corte é o processo de seleção de uma janela retangular dentro do quadro de vídeo e a codificação apenas dos pixels dentro dessa janela. O diagrama a seguir ajuda a ilustrar o processo.

![Cortar um vídeo](./media/media-services-crop-video/media-services-crop-video01.png)

Suponha que você tenha como entrada um vídeo que tenha uma resolução de pixels de 1920 x 1080 (taxa de proporção de 16:9), mas que tenha barras pretas (caixas de pilar) à esquerda e à direita, para que apenas uma janela 4:3 ou 1440 x 1080 pixels contenham vídeo ativo. Você pode usar MES para cortar ou editar as barras pretas e codificar a região 1440 x 1080.

O corte em MES é um estágio de pré-processamento, portanto, os parâmetros de corte na predefinição de codificação se aplicam ao vídeo de entrada original. A codificação é um estágio subsequente, e as configurações de largura/altura se aplicam ao vídeo *previamente processado* e não ao vídeo original. Ao criar sua predefinição, você precisa fazer o seguinte: (a) Selecione os parâmetros de corte com base no vídeo de entrada original e (b) selecione as configurações de codificação com base no vídeo cortado. Se você não corresponder às configurações de codificação para o vídeo cortado, a saída não será a esperada.

O tópico a [seguir](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) mostra como criar um trabalho de codificação com mes e como especificar uma predefinição personalizada para a tarefa de codificação. 

## <a name="creating-a-custom-preset"></a>Criando uma predefinição personalizada
No exemplo mostrado no diagrama:

1. A entrada original é 1920 x 1080
2. Ele precisa ser cortado para uma saída de 1440 x 1080, que é centralizada no quadro de entrada
3. Isso significa um deslocamento X de (1920 – 1440)/2 = 240 e um deslocamento Y de zero
4. A largura e a altura do retângulo de corte são 1440 e 1080, respectivamente
5. No estágio de codificação, a pergunta é produzir três camadas, são as resoluções 1440 x 1080, 960x720 e 480x360, respectivamente

### <a name="json-preset"></a>Predefinição JSON
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


## <a name="restrictions-on-cropping"></a>Restrições de corte
O recurso de corte deve ser manual. Você precisaria carregar seu vídeo de entrada em uma ferramenta de edição adequada que permita selecionar quadros de interesse, posicionar o cursor para determinar deslocamentos para o retângulo de corte, para determinar a predefinição de codificação ajustada para esse vídeo específico, etc. Esse recurso não deve permitir coisas como: detecção automática e remoção de bordas Letterbox/pillarbox pretas em seu vídeo de entrada.

As restrições a seguir se aplicam ao recurso de corte. Se eles não forem atendidos, a tarefa de codificação poderá falhar ou produzir uma saída inesperada.

1. As contornos e o tamanho do retângulo de corte devem se ajustar no vídeo de entrada
2. Conforme mencionado acima, a largura & altura nas configurações de codificação devem corresponder ao vídeo cortado
3. O corte se aplica a vídeos capturados no modo paisagem (ou seja, não aplicável a vídeos registrados com um smartphone mantido verticalmente ou no modo retrato)
4. Funciona melhor com vídeo progressivo capturado com pixels quadrados

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Passo seguinte
Consulte os roteiros de aprendizagem dos serviços de mídia do Azure para ajudá-lo a saber mais sobre os ótimos recursos oferecidos pelo AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
