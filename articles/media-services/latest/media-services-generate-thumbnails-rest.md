---
title: Obtenha uma chave de assinatura de uma política utilizando a Azure Media Services v3 .NET
description: Este artigo mostra como usar o REST para codificar um ativo e gerar miniaturas ao mesmo tempo usando o Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 75b8fd53836cce02816413afaeb656c3ac3f8796
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935402"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>Como gerar miniaturas usando o Encoder Standard com REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pode utilizar o Media Encoder Standard para gerar uma ou mais miniaturas a partir do seu vídeo de entrada em formatos [JPEG,](https://en.wikipedia.org/wiki/JPEG) [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)ou ficheiro de imagem [BMP.](https://en.wikipedia.org/wiki/BMP_file_format)

## <a name="recommended-reading-and-practice"></a>Leitura e prática recomendadas

Recomenda-se que se familiarize com transformações personalizadas lendo [Como codificar com uma transformação personalizada - REST](custom-preset-rest-howto.md).

## <a name="thumbnail-parameters"></a>Parâmetros de miniatura

Deve definir os seguintes parâmetros:

- **iniciar** - A posição no vídeo de entrada de onde começar a gerar miniaturas. O valor pode ser no formato ISO 8601 (Por exemplo, PT05S a começar a 5 segundos), ou uma contagem de quadros (por exemplo, 10 para começar no 10º quadro), ou um valor relativo à duração do fluxo (por exemplo, 10% para começar com 10% da duração do fluxo). Também suporta uma macro {Best}, que diz ao codificador para selecionar a melhor miniatura dos primeiros segundos do vídeo e produzirá apenas uma miniatura, independentemente das outras definições para Step e Range. O valor predefinido é macro {Best}.
- **passo** - Os intervalos em que as miniaturas são geradas. O valor pode ser no formato ISO 8601 (por exemplo, PT05S para uma imagem a cada 5 segundos), ou uma contagem de quadros (por exemplo, 30 para uma imagem a cada 30 fotogramas), ou um valor relativo à duração do fluxo (por exemplo, 10% para uma imagem a cada 10% da duração do fluxo). O valor do passo afetará a primeira miniatura gerada, que pode não ser exatamente a especificada na hora de início pré-sintonia de transformação. Isto deve-se ao codificador, que tenta selecionar a melhor miniatura entre a hora de início e a posição do passo a partir da hora de início como primeira saída. Como o valor padrão é de 10%, significa que se o fluxo tiver uma longa duração, a primeira miniatura gerada pode estar longe da especificada na hora de início. Tente selecionar um valor razoável para o passo se se espera que a primeira miniatura esteja perto da hora de início, ou desa um valor de intervalo para 1 se apenas uma miniatura for necessária no momento de início.
- **gama** - A posição relativa à transformação da hora de início predefinida no vídeo de entrada para parar de gerar miniaturas. O valor pode estar no formato ISO 8601 (Por exemplo, PT5M30S para parar a 5 minutos e 30 segundos da hora de início), ou uma contagem de quadros (por exemplo, 300 para parar no 300º quadro a partir do quadro na hora de início. Se este valor for 1, significa apenas produzir uma miniatura na hora de início), ou um valor relativo à duração do fluxo (Por exemplo, 50% para parar a metade da duração do fluxo desde a hora de início). O valor predefinido é de 100%, o que significa parar no final do fluxo.
- **camadas** - Uma coleção de camadas de imagem de saída a produzir pelo codificadora.

## <a name="example-of-a-single-png-file-preset"></a>Exemplo de uma predefinição de "ficheiro PNG único"

A predefinição JSON a seguir pode ser usada para produzir um único ficheiro PNG de saída a partir dos primeiros segundos do vídeo de entrada, onde o codificadora faz uma melhor tentativa de encontrar uma moldura "interessante". Note que as dimensões da imagem de saída foram definidas a 100%, o que significa que estas correspondem às dimensões do vídeo de entrada. Note também como a definição de "Formato" em "Outputs" é necessária para corresponder à utilização de "PngLayers" na secção "Codecs".  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Exemplo de uma predefinição de "série de imagens JPEG"

A predefinição JSON a seguir pode ser usada para produzir um conjunto de 10 imagens em intervalos de tempo de 5%, 15%, ..., 95% da linha temporal de entrada, onde o tamanho da imagem é especificado como um quarto do vídeo de entrada.

### <a name="json-preset"></a>Predefinição JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Exemplo de uma predefinição de "uma imagem a uma determinada hora"

A predefinição JSON a seguir pode ser usada para produzir uma única imagem JPEG na marca de 30 segundos do vídeo de entrada. Esta predefinição espera que o vídeo de entrada tenha mais de 30 segundos de duração (caso contrário o trabalho falha).

### <a name="json-preset"></a>Predefinição JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "00:00:30",
      "Step": "1",
      "Range": "1",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Exemplo de "miniaturas em diferentes resoluções" predefinido

A predefinição a seguir pode ser usada para gerar miniaturas em diferentes resoluções numa só tarefa. No exemplo, em posições 5%, 15%, ..., 95% da linha temporal de entrada, o codificader gera duas imagens – uma a 100% da resolução de vídeo de entrada e outra a 50%.

Note a utilização de macro {Resolução} no Nome de Ficheiro; indica ao codificadora a utilização da largura e altura especificadas na secção de codificação da predefinição enquanto gera o nome do ficheiro das imagens de saída. Isto também ajuda a distinguir facilmente entre as diferentes imagens.

### <a name="json-preset"></a>Predefinição JSON

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "100%",
  "Height": "100%"
},
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "50%",
  "Height": "50%"
}

      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
      "Format": {
"Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Exemplo de gerar uma miniatura enquanto codifica

Embora todos os exemplos acima tenham discutido como pode submeter uma tarefa de codificação que apenas produz imagens, também pode combinar codificação vídeo/áudio com geração de miniaturas. A predefinição JSON seguinte diz à Encoder Standard para gerar uma miniatura durante a codificação.

### <a name="json-preset"></a>Predefinição JSON

Para obter informações sobre esquemas, consulte [este](../previous/media-services-mes-schema.md) artigo.

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": "true",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 4500,
          "MaxBitrate": 4500,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cabac",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"

        }
      ],
      "Type": "H264Video"
    },
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    },
    {
      "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximos passos
[Gerenas de miniaturas usando .NET](media-services-generate-thumbnails-dotnet.md)