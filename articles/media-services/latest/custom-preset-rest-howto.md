---
title: Codificar de transformação personalizados com os serviços de multimédia v3 REST - Azure | Documentos da Microsoft
description: Este tópico mostra como utilizar serviços de multimédia do Azure v3 para codificar uma transformação personalizada através de REST.
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
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761801"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Como codificar com uma transformação personalizados - REST

Ao codificar com os serviços de multimédia do Azure, pode começar a utilizar rapidamente com um das predefinições de incorporada recomendadas, com base em práticas recomendadas do setor, como demonstrado na [ficheiros de transmissão em fluxo](stream-files-tutorial-with-rest.md#create-a-transform) tutorial. Também pode criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino.

## <a name="considerations"></a>Considerações

Ao criar configurações predefinidas personalizadas, aplicam-se as seguintes considerações:

* Todos os valores para a altura e largura no conteúdo de AVC tem de ser um múltiplo de 4.
* Em serviços de multimédia do Azure v3, todas as velocidades de transmissão de codificação são em bits por segundo. Isso é diferente de predefinições com nossas APIs v2, que utilizado kilobits por segundo, como a unidade. Por exemplo, se a velocidade de transmissão no v2 foi especificada como 128 (kilobits por segundo), na v3-la seria definido como 128000 (bits por segundo).

## <a name="prerequisites"></a>Pré-requisitos 

- [Criar uma conta de Media Services](create-account-cli-how-to.md). <br/>Lembre-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia. 
- [Configurar o Postman para chamar a API de REST de serviços de multimédia do Azure](media-rest-apis-with-postman.md).<br/>Lembre-se de que siga o último passo no tópico [obter do Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definir uma configuração predefinida personalizada

O exemplo seguinte define o corpo do pedido de uma nova transformação. Vamos definir um conjunto de saídas que Desejamos sejam gerados quando essa transformação é utilizada. 

Neste exemplo, primeiro adicionamos uma camada de AacAudio para a codificação de áudio e de duas camadas de H264Video para a codificação de vídeo. As camadas de vídeo, podemos atribuir etiquetas para que possa ser utilizados em nomes de ficheiros de saída. Em seguida, queremos que a saída para incluir também miniaturas. No exemplo abaixo, podemos especificar imagens em formato PNG, gerado a 50% da resolução de vídeo de entrada e em três carimbos de data / - {25%, 50%, 75} de comprimento do vídeo de entrada. Por último, podemos especificar o formato para os ficheiros de saída – um para vídeo + áudio e outro para as miniaturas. Uma vez que temos várias H264Layers, temos de usar macros que produzem nomes exclusivos por camada. Podemos utilizar um `{Label}` ou `{Bitrate}` macro, o exemplo mostra o primeiro.

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

Neste exemplo, vamos criar um **transformar** que se baseia-se a configuração predefinida personalizada definido anteriormente. Ao criar uma transformação, primeiro deve usar [obter](https://docs.microsoft.com/rest/api/media/transforms/get) para verificar se já existir. Se existir a transformação, reutilizá-lo. 

Coleção do Postman que transferiu, selecione **transforma e tarefas**->**criação ou atualização transformar**.

O **colocar** método de pedido HTTP é semelhante a:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Selecione o **corpo** separador e substitua o corpo json com o código que [definidas anteriormente](#define-a-custom-preset). Para serviços de multimédia para aplicar a transformação ao especificado de vídeo ou áudio, tem de submeter uma tarefa sob essa transformação.

Selecione **Enviar**. 

Para serviços de multimédia para aplicar a transformação ao especificado de vídeo ou áudio, tem de submeter uma tarefa sob essa transformação. Para obter um exemplo completo que mostra como submeter uma tarefa numa transformação, consulte [Tutorial: Stream ficheiros de vídeo - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Passos Seguintes

Consulte [outras operações de REST](https://docs.microsoft.com/rest/api/media/)
