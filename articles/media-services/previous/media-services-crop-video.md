---
title: Como recortar vídeos com o Media Encoder Standard - Azure | Documentos da Microsoft
description: Este artigo mostra como recortar vídeos com o Media Encoder Standard.
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
ms.author: anilmur;juliako;
ms.openlocfilehash: 9a81050fca935f688f2ff58cb04a148bf676f04b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176655"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Recortar vídeos com o Media Encoder Standard  

Pode usar o Media Encoder Standard (MES) para cortar sua entrada de vídeo. Corte é o processo de seleção de uma janela retangular dentro do quadro do vídeo e a codificação de apenas os pixels nessa janela. O diagrama a seguir ajuda a ilustrar o processo.

![Recortar um vídeo](./media/media-services-crop-video/media-services-crop-video01.png)

Suponha que tenha como entrada um vídeo que tem uma resolução de 1920 x 1080 pixels (proporção de 16:9), mas tem o pretas barras (caixas de pilar) à esquerda e direita, para que apenas uma janela de 4:3 ou 1440 x 1080 pixels contém vídeo Active Directory. Pode utilizar o MES para cortar ou editar as barras de pretas e codificar a região de 1440 x 1080.

Corte em MES é um estágio de pré-processamento, pelo que se aplicam os parâmetros de recorte, a predefinição de codificação para o vídeo de entrada original. Codificação é uma fase subsequente, e as definições de largura/altura aplicam-se para o *previamente processada* vídeo e não para o vídeo original. Ao conceber a sua configuração predefinida que precisa fazer o seguinte: (a) selecione os parâmetros de recorte com base no vídeo de entrada original e (b) selecione as definições com base no vídeo cropped de codificar. Se não forem iguais a codificar as definições para o vídeo cropped, a saída não estarão conforme o esperado.

O [seguintes](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) tópico mostra como criar uma tarefa de codificação com MES e como especificar um personalizado predefinido para a tarefa de codificação. 

## <a name="creating-a-custom-preset"></a>Criar uma configuração predefinida personalizada
No exemplo mostrado no diagrama:

1. Entrada original é 1920 x 1080
2. Ele precisa ser recortada para uma saída 1440 x 1080, que se centra-se na estrutura de entrada
3. Isso significa que um desvio de X de (1920 – 1440) / 2 = 240 e um Y de deslocamento de zero
4. A largura e altura do retângulo corte são 1440 e 1080, respetivamente
5. Na fase encode, do ask é produzir três camadas, são as resoluções 1440 x 1080, 960 x 720 e 480 x 360, respetivamente

### <a name="json-preset"></a>Configuração predefinida JSON
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
O recurso de recorte deve ser manual. Terá de carregar o seu vídeo de entrada para uma ferramenta de edição adequada que permite que selecione quadros de interesse, posicione o cursor para determinar os desvios para o retângulo de recorte, para determinar a predefinição de codificação concebido para esse etc de vídeo, específico. Esta funcionalidade não se destina a ativar a coisas como: a deteção automática e a remoção de bordas pretas letterbox/pillarbox no seu vídeo de entrada.

As seguintes restrições aplicam-se para o recurso de recorte. Se eles não forem cumpridos, a codificar tarefas pode falhar ou produzir um resultado inesperado.

1. O ordinates conjunta e o tamanho do retângulo corte tem que caber no vídeo de entrada
2. Conforme mencionado acima, a largura e altura nas definições de codificar tem de corresponder ao vídeo cropped
3. Corte aplica-se a vídeos capturados no modo horizontal (ou seja, mantido não aplicável para os vídeos gravados com um smartphone, verticalmente ou no modo vertical)
4. Funciona melhor com vídeo progressivo capturado com pixels quadrados

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Passo seguinte
Veja o Azure Media Services percursos de aprendizagem para saber mais sobre os excelentes recursos oferecidos pelo AMS.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
