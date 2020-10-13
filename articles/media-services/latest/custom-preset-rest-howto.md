---
title: Transformação personalizada encode usando Media Services v3 REST - Azure / Microsoft Docs
description: Este tópico mostra como usar o Azure Media Services v3 para codificar uma transformação personalizada usando REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 2656bf93cb9c29ded4b9dde49f0caba91c1654b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295636"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Como codificar com uma transformação personalizada - REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao codificar com o Azure Media Services, pode começar rapidamente com uma das predefinições recomendadas, baseada nas melhores práticas da indústria, como demonstra o tutorial de [ficheiros streaming.](stream-files-tutorial-with-rest.md#create-a-transform) Também pode construir uma predefinição personalizada para direcionar o seu cenário específico ou requisitos do dispositivo.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, aplicam-se as seguintes considerações:

* Todos os valores para altura e largura no conteúdo AVC devem ser múltiplos de 4.
* No Azure Media Services v3, todos os bitrates codificadores estão em bits por segundo. Isto é diferente das predefinições com as nossas APIs v2, que usavam quilobits/segundo como unidade. Por exemplo, se o bitrate em v2 fosse especificado como 128 (quilobits/segundo), em v3 seria definido para 128000 (bits/segundo).

## <a name="prerequisites"></a>Pré-requisitos 

- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social. <br/>Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Comunicação Social. 
- [Configure Carteiro para Azure Media Services REST Chamadas API](media-rest-apis-with-postman.md).<br/>Certifique-se de seguir o último passo do tema [Get Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Defina uma predefinição personalizada

O exemplo a seguir define o corpo de pedido de uma nova Transformação. Definimos um conjunto de saídas que queremos ser geradas quando esta Transforma é usada. 

Neste exemplo, primeiro adicionamos uma camada AacAudio para a codificação de áudio e duas camadas H264Video para a codificação de vídeo. Nas camadas de vídeo, atribuímos etiquetas para que possam ser usadas nos nomes dos ficheiros de saída. Em seguida, queremos que a saída também inclua miniaturas. No exemplo abaixo especificamos imagens em formato PNG, geradas a 50% da resolução do vídeo de entrada, e em três vezes osmps - {25%, 50%, 75} do comprimento do vídeo de entrada. Por último, especificamos o formato para os ficheiros de saída - um para vídeo + áudio e outro para as miniaturas. Como temos vários H264Layers, temos de usar macros que produzem nomes únicos por camada. Podemos usar um `{Label}` ou `{Bitrate}` macro, o exemplo mostra o primeiro.

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

## <a name="create-a-new-transform"></a>Criar uma nova transformação  

Neste exemplo, criamos uma **Transformação** que se baseia na predefinição personalizada que definimos anteriormente. Ao criar uma Transform, deve primeiro utilizar [o Get](/rest/api/media/transforms/get) to check if one já existe. Se a Transformação existe, reutilizá-la. 

Na coleção do Carteiro que descarregou, selecione **Transforms and Jobs** -> **Create ou Update Transform**.

O método de pedido **PUT** HTTP é semelhante a:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Selecione o **separador 'Corpo'** e substitua o corpo pelo código json que [definiu anteriormente](#define-a-custom-preset). Para que os Serviços de Comunicação Social apliquem a Transformação ao vídeo ou áudio especificado, é necessário submeter um Job ao abrigo dessa Transformação.

Selecione **Send** (Enviar). 

Para que os Serviços de Comunicação Social apliquem a Transformação ao vídeo ou áudio especificado, é necessário submeter um Job ao abrigo dessa Transformação. Para um exemplo completo que mostre como submeter um trabalho sob uma transformação, consulte [Tutorial: Stream video files - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Passos seguintes

Ver [outras operações REST](/rest/api/media/)
