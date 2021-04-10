---
title: Como cortar vídeos com Media Encoder Standard - Azure | Microsoft Docs
description: Cortar é o processo de selecionar uma janela retangular dentro da moldura de vídeo, e codificar apenas os pixels dentro dessa janela. Este artigo demonstra como cortar vídeos com a Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 6a15e57884f85e2d56b77c3e5eec1267133fe96d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016734"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Recortar vídeos com o Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Pode utilizar o Media Encoder Standard (MES) para cortar o seu vídeo de entrada. Cortar é o processo de selecionar uma janela retangular dentro da moldura de vídeo, e codificar apenas os pixels dentro dessa janela. O diagrama a seguir ajuda a ilustrar o processo.

![Corte um vídeo](./media/media-services-crop-video/media-services-crop-video01.png)

Suponha que tenha como entrada um vídeo que tenha uma resolução de 1920x1080 pixels (relação de aspeto 16:9), mas tem barras pretas (caixas de pilares) à esquerda e à direita, de modo que apenas uma janela de 4:3 ou 1440x1080 pixels contém vídeo ativo. Você pode usar MES para cortar ou editar as barras pretas, e codificar a região 1440x1080.

A cultura em MES é uma fase de pré-processamento, pelo que os parâmetros de corte na predefinição codificante aplicam-se ao vídeo de entrada original. A codificação é uma fase subsequente, e as definições de largura/altura aplicam-se ao vídeo *pré-processado* e não ao vídeo original. Ao conceber a sua pré-sintonia, tem de fazer o seguinte: (a) selecionar os parâmetros de cultura com base no vídeo de entrada original e (b) selecionar as definições de codificação com base no vídeo cortado. Se não corresponder às definições de codificação com o vídeo cortado, a saída não será como se espera.

O [tópico que se segue](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) mostra como criar um trabalho de codificação com MES e como especificar uma predefinição personalizada para a tarefa de codificação. 

## <a name="creating-a-custom-preset"></a>Criação de uma predefinição personalizada
No exemplo mostrado no diagrama:

1. Entrada original é 1920x1080
2. Precisa de ser cortado para uma saída de 1440x1080, que está centrada no quadro de entrada
3. Isto significa uma compensação X de (1920 - 1440)/2 = 240, e uma compensação Y de zero
4. A largura e altura do retângulo da cultura são 1440 e 1080, respectivamente
5. Na fase de codificação, o pedido é produzir três camadas, são resoluções 1440x1080, 960x720 e 480x360, respectivamente

### <a name="json-preset"></a>Predefinição JSON

```json
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
```

## <a name="restrictions-on-cropping"></a>Restrições à cultura
A função de corte deve ser manual. Você precisaria de carregar o seu vídeo de entrada numa ferramenta de edição adequada que lhe permite selecionar quadros de interesse, posicionar o cursor para determinar compensações para o retângulo de corte, para determinar a predefinição codificante que está sintonizada para esse vídeo em particular, etc. Esta funcionalidade não se destina a permitir coisas como: deteção automática e remoção de bordas de caixa de correio/pilares pretas no seu vídeo de entrada.

Os seguintes constrangimentos aplicam-se à função de corte. Se estes não forem cumpridos, a Tarefa de codificação pode falhar ou produzir uma saída inesperada.

1. As coordenadas e o tamanho do retângulo crop têm de caber dentro do vídeo de entrada
2. Como mencionado acima, a Largura & Altura nas definições de codificação tem de corresponder ao vídeo cortado
3. A cultura aplica-se a vídeos capturados em modo paisagístico (isto é, não aplicável a vídeos gravados com um smartphone mantido verticalmente ou em modo retrato)
4. Funciona melhor com vídeo progressivo capturado com píxeis quadrados

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Passo seguinte
Veja os caminhos de aprendizagem da Azure Media Services para ajudá-lo a aprender sobre as grandes funcionalidades oferecidas pela AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
