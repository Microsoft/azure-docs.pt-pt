---
title: Codificar de transformação personalizados com os serviços de multimédia v3 CLI - Azure | Documentos da Microsoft
description: Este tópico mostra como utilizar serviços de multimédia do Azure v3 para codificar uma transformação personalizada com a CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: 7160b7c9cdb82d378870edb1750c9d9016aa2d18
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2019
ms.locfileid: "58006851"
---
# <a name="how-to-encode-with-a-custom-transform-by-using-cli"></a>Como codificar com uma transformação personalizada com a CLI

Ao codificar com os serviços de multimédia do Azure, pode começar a utilizar rapidamente com um das predefinições de incorporada recomendadas, com base em práticas recomendadas do setor, como demonstrado na [ficheiros de transmissão em fluxo](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) início rápido. Também pode criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino.

## <a name="considerations"></a>Considerações

Ao criar configurações predefinidas personalizadas, aplicam-se as seguintes considerações:

* Todos os valores para a altura e largura no conteúdo de AVC tem de ser um múltiplo de 4.
* Em serviços de multimédia do Azure v3, todas as velocidades de transmissão de codificação são em bits por segundo. Isso é diferente de predefinições com nossas APIs v2, que utilizado kilobits por segundo, como a unidade. Por exemplo, se a velocidade de transmissão no v2 foi especificada como 128 (kilobits por segundo), na v3-la seria definido como 128000 (bits por segundo).

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](create-account-cli-how-to.md). <br/>Lembre-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definir uma configuração predefinida personalizada

O exemplo seguinte define o corpo do pedido de uma nova transformação. Vamos definir um conjunto de saídas que Desejamos sejam gerados quando essa transformação é utilizada. 

Neste exemplo, primeiro adicionamos uma camada de AacAudio para a codificação de áudio e de duas camadas de H264Video para a codificação de vídeo. As camadas de vídeo, podemos atribuir etiquetas para que possa ser utilizados em nomes de ficheiros de saída. Em seguida, queremos que a saída para incluir também miniaturas. No exemplo abaixo, podemos especificar imagens em formato PNG, gerado a 50% da resolução de vídeo de entrada e em três carimbos de data / - {25%, 50%, 75} de comprimento do vídeo de entrada. Por último, podemos especificar o formato para os ficheiros de saída – um para vídeo + áudio e outro para as miniaturas. Uma vez que temos várias H264Layers, temos de usar macros que produzem nomes exclusivos por camada. Podemos utilizar um `{Label}` ou `{Bitrate}` macro, o exemplo mostra o primeiro.

Vamos guardar essa transformação num arquivo. Neste exemplo, podemos nomear o arquivo `customPreset.json`. 

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
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
                            "filenamePattern": "Video-{Basename}-{Label}{Extension}",
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

## <a name="create-a-new-transform"></a>Criar uma nova transformação  

Neste exemplo, vamos criar um **transformar** que se baseia-se a configuração predefinida personalizada definido anteriormente. Ao criar uma transformação, deve verificar se um já existe. Se existir a transformação, reutilizá-lo. O seguinte procedimento `show` comando devolve a `customTransformName` transformar se existir:

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

O seguinte comando da CLI cria a transformação com base na configuração predefinida personalizada (definida anteriormente). 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Para serviços de multimédia para aplicar a transformação ao especificado de vídeo ou áudio, tem de submeter uma tarefa sob essa transformação. Para obter um exemplo completo que mostra como submeter uma tarefa numa transformação, consulte [início rápido: Ficheiros de vídeo - CLI de Stream](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
