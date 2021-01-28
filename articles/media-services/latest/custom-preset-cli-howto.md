---
title: Codificar um CLI de transformação personalizado
description: Este tópico mostra como usar o Azure Media Services v3 para codificar uma transformação personalizada usando O Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 91ee605035dbc81c2302aa0350763e68dc73ecb3
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956111"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Como codificar com uma transformação personalizada - Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao codificar com o Azure Media Services, pode começar rapidamente com uma das predefinições recomendadas, com base nas melhores práticas da indústria, como demonstra o quickstart dos [ficheiros streaming.](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) Também pode construir uma predefinição personalizada para direcionar o seu cenário específico ou requisitos do dispositivo.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, aplicam-se as seguintes considerações:

* Todos os valores para altura e largura no conteúdo AVC devem ser múltiplos de 4.
* No Azure Media Services v3, todos os bitrates codificadores estão em bits por segundo. Isto é diferente das predefinições com as nossas APIs v2, que usavam quilobits/segundo como unidade. Por exemplo, se o bitrate em v2 fosse especificado como 128 (quilobits/segundo), em v3 seria definido para 128000 (bits/segundo).

## <a name="prerequisites"></a>Pré-requisitos

[Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social.

Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Comunicação Social.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Defina uma predefinição personalizada

O exemplo a seguir define o corpo de pedido de uma nova Transformação. Definimos um conjunto de saídas que queremos ser geradas quando esta Transforma é usada.

Neste exemplo, primeiro adicionamos uma camada AacAudio para a codificação de áudio e duas camadas H264Video para a codificação de vídeo. Nas camadas de vídeo, atribuímos etiquetas para que possam ser usadas nos nomes dos ficheiros de saída. Em seguida, queremos que a saída também inclua miniaturas. No exemplo abaixo especificamos imagens em formato PNG, geradas a 50% da resolução do vídeo de entrada, e em três vezes osmps - {25%, 50%, 75} do comprimento do vídeo de entrada. Por último, especificamos o formato para os ficheiros de saída - um para vídeo + áudio e outro para as miniaturas. Como temos vários H264Layers, temos de usar macros que produzem nomes únicos por camada. Podemos usar um `{Label}` ou `{Bitrate}` macro, o exemplo mostra o primeiro.

Vamos salvar esta transformação num ficheiro. Neste exemplo, damos o nome ao `customPreset.json` ficheiro.

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

Neste exemplo, criamos uma **Transformação** que se baseia na predefinição personalizada que definimos anteriormente. Ao criar uma Transform, deve primeiro verificar se já existe uma. Se a Transformação existe, reutilizá-la. O seguinte `show` comando devolve a `customTransformName` transformação se existir:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

O seguinte comando Azure CLI cria a Transformação com base na predefinição personalizada (definida anteriormente).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Para que os Serviços de Comunicação Social apliquem a Transformação ao vídeo ou áudio especificado, é necessário submeter um Job ao abrigo dessa Transformação. Para um exemplo completo que mostre como submeter um trabalho sob uma transformação, consulte [Quickstart: Stream video files - Azure CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Ver também

[CLI do Azure](/cli/azure/ams)
