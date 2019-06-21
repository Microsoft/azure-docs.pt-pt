---
title: Um vídeo do subclip ao codificar com a API de REST de serviços de multimédia do Azure
description: Este tópico descreve como um vídeo do subclip ao codificar com os serviços de multimédia do Azure com REST
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: df8c8a4040b4aae4379b4bfe0e9a16337588dd1b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305215"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Um vídeo do subclip quando encoding com Media Services - REST

Pode cortar ou um vídeo do subclip ao codificar usando um [tarefa](https://docs.microsoft.com/rest/api/media/jobs). Esta funcionalidade funciona com qualquer [transformar](https://docs.microsoft.com/rest/api/media/transforms) criada através de um a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) predefinições, ou o [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) configurações predefinidas. 

O exemplo REST neste tópico cria uma tarefa que corta um vídeo que submete um trabalho de codificação. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos descritos neste tópico, tem de:

- [Criar uma conta de Media Services do Azure](create-account-cli-how-to.md).
- [Configurar o Postman para chamar a API de REST de serviços de multimédia do Azure](media-rest-apis-with-postman.md).
    
    Lembre-se de que siga o último passo no tópico [obter do Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Criar uma transformação e uma saída ativos. Pode ver como criar uma transformação e uma saída ativos no [codificar um ficheiro remoto com base na URL e transmitir o vídeo - REST](stream-files-tutorial-with-rest.md) tutorial.
- Reveja os [conceito de codificação](encoding-concept.md) tópico.

## <a name="create-a-subclipping-job"></a>Criar uma tarefa de subdistorção

1. Na coleção do Postman que transferiu, selecione **transforma e tarefas** -> **criar tarefa com o recorte de sub-rotina**.
    
    O **colocar** pedido tem o seguinte aspeto:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Atualize o valor da variável de ambiente de "transformName" com o nome da sua transformação. 
1. Selecione o **corpo** separador e atualizar "myOutputAsset" com a sua saída nome do elemento.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Prima **Enviar**.

    Verá o **resposta** com as informações sobre uma tarefa que foi criada e submetida e o estado da tarefa. 

## <a name="next-steps"></a>Passos Seguintes

[Como codificar com uma transformação personalizados](custom-preset-rest-howto.md) 
