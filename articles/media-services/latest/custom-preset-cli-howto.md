---
title: Encode custom transform usando Media Services v3 Azure CLI [ Microsoft Docs
description: Este tópico mostra como usar o Azure Media Services v3 para codificar uma transformação personalizada usando o Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 7c1b446ccf04199449f012e738f6a03660735f50
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382958"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Como codificar com uma transformação personalizada - Azure CLI

Ao codificar com a Azure Media Services, pode começar rapidamente com uma das predefinições incorporadas recomendadas, com base nas melhores práticas da indústria, como demonstram os [ficheiros streaming](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) quickstart. Também pode construir um preset personalizado para direcionar os seus requisitos específicos para o seu cenário ou dispositivo.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, aplicam-se as seguintes considerações:

* Todos os valores para a altura e largura no teor de AVC devem ser múltiplos de 4.
* No Azure Media Services v3, todos os bitrates de codificação estão em bits por segundo. Isto é diferente dos presets com as nossas V2 APIs, que usavam quilobits/segundo como unidade. Por exemplo, se o bitrate em v2 fosse especificado como 128 (quilobits/segundo), em v3 seria fixado em 128000 (bits/segundo).

## <a name="prerequisites"></a>Pré-requisitos

[Criar uma conta de Media Services.](create-account-cli-how-to.md)

Lembre-se do nome do grupo de recursos e do nome da conta Media Services.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definir um preset personalizado

O exemplo seguinte define o corpo de pedido de uma nova Transformação. Definimos um conjunto de saídas que queremos ser geradas quando este Transform é usado.

Neste exemplo, adicionamos primeiro uma camada AacAudio para a codificação de áudio e duas camadas H264Video para a codificação de vídeo. Nas camadas de vídeo, atribuímos etiquetas para que possam ser usadas nos nomes dos ficheiros de saída. Em seguida, queremos que a saída também inclua miniaturas. No exemplo abaixo especificamos imagens em formato PNG, geradas a 50% da resolução do vídeo de entrada, e em três selos de tempo - {25%, 50%, 75} do comprimento do vídeo de entrada. Por último, especificamos o formato para os ficheiros de saída - um para vídeo + áudio e outro para as miniaturas. Como temos vários H264Layers, temos que usar macros que produzem nomes únicos por camada. Podemos usar `{Label}` um `{Bitrate}` ou macro, o exemplo mostra o primeiro.

Vamos salvar esta transformação num ficheiro. Neste exemplo, damos `customPreset.json`o nome do ficheiro.

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
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
```

## <a name="create-a-new-transform"></a>Criar uma nova transformação  

Neste exemplo, criamos um **Transform** que é baseado no predefinido personalizado que definimos anteriormente. Ao criar um Transform, deve primeiro verificar se já existe um. Se a Transformação existir, reutilize-a. O `show` seguinte comando `customTransformName` devolve a transformação se existir:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

O seguinte comando Azure CLI cria a Transformação com base no predefinido personalizado (definido anteriormente).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Para que os Serviços de Media apliquem a Transform no vídeo ou áudio especificado, é necessário submeter um Trabalho ao abrigo dessa Transformação. Para um exemplo completo que mostra como submeter um trabalho sob uma transformação, consulte [Quickstart: Stream video files - Azure CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Consulte também

[Azure CLI](/cli/azure/ams)
