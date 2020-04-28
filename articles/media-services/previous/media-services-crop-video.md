---
title: Como plantar vídeos com media Encoder Standard - Azure [ Microsoft Docs
description: A colheita é o processo de selecionar uma janela retangular dentro da moldura de vídeo, e codificar apenas os pixels dentro dessa janela. Este artigo demonstra como fazer a colheita de vídeos com a Media Encoder Standard.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74887769"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Recortar vídeos com o Media Encoder Standard  

Pode utilizar o Media Encoder Standard (MES) para cortar o seu vídeo de entrada. A colheita é o processo de selecionar uma janela retangular dentro da moldura de vídeo, e codificar apenas os pixels dentro dessa janela. O diagrama que se segue ajuda a ilustrar o processo.

![Corte um vídeo](./media/media-services-crop-video/media-services-crop-video01.png)

Suponha que tenha como entrada um vídeo que tenha uma resolução de 1920x1080 pixels (relação de aspeto 16:9), mas tem barras pretas (caixas de pilares) à esquerda e à direita, para que apenas uma janela 4:3 ou 1440x1080 pixels contenha vídeo ativo. Você pode usar MES para cortar ou editar as barras pretas, e codificar a região 1440x1080.

A colheita em MES é uma fase de pré-processamento, pelo que os parâmetros de colheita no predefinição de codificação aplicam-se ao vídeo de entrada original. A codificação é uma fase posterior, e as definições de largura/altura aplicam-se ao vídeo *pré-processado* e não ao vídeo original. Ao desenhar o seu preset, tem de fazer o seguinte: (a) selecione os parâmetros de cultura com base no vídeo de entrada original e (b) selecione as definições de codificação com base no vídeo cortado. Se não corresponder as definições de codificação ao vídeo cortado, a saída não será como espera.

O [tópico seguinte](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) mostra como criar um trabalho de codificação com MES e como especificar um predefinido personalizado para a tarefa de codificação. 

## <a name="creating-a-custom-preset"></a>Criar um preset personalizado
No exemplo mostrado no diagrama:

1. Entrada original é 1920x1080
2. Precisa de ser cortado a uma saída de 1440x1080, que está centrada no quadro de entrada
3. Isto significa um x offset de (1920 - 1440)/2 = 240, e um Y offset de zero
4. A largura e altura do retângulo da colheita são 1440 e 1080, respectivamente
5. Na fase de codificação, o pedido é produzir três camadas, são resoluções 1440x1080, 960x720 e 480x360, respectivamente

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


## <a name="restrictions-on-cropping"></a>Restrições à cultura
A característica de corte deve ser manual. Você precisaria de carregar o seu vídeo de entrada numa ferramenta de edição adequada que lhe permite selecionar quadros de interesse, posicionar o cursor para determinar compensações para o retângulo de corte, para determinar o preconjunto de codificação que está sintonizado para esse vídeo em particular, etc. Esta funcionalidade não pretende permitir coisas como: deteção automática e remoção de bordas de caixa de correio/pilares pretas no seu vídeo de entrada.

Os seguintes constrangimentos aplicam-se à função de corte. Se estes não forem cumpridos, a tarefa codificada pode falhar ou produzir uma saída inesperada.

1. As coordenadas e o tamanho do retângulo crop têm de caber dentro do vídeo de entrada
2. Como mencionado acima, a Altura & largura nas definições de código de código tem de corresponder ao vídeo cortado
3. A colheita aplica-se a vídeos capturados em modo paisagístico (ou seja, não aplicáveis a vídeos gravados com um smartphone mantido verticalmente ou em modo retrato)
4. Funciona melhor com vídeo progressivo capturado com pixels quadrados

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Passo seguinte
Consulte os caminhos de aprendizagem da Azure Media Services para o ajudar a aprender sobre as grandes funcionalidades oferecidas pela AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
