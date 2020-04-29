---
title: Subclipe um vídeo ao codificar com o Azure Media Services REST
description: Este tópico descreve como subclipe um vídeo ao codificar com a Azure Media Services usando o REST
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
ms.openlocfilehash: c39aded55fe36cb130459a4f6f119f872b1adbc4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76514328"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Subclipe um vídeo ao codificar com os Media Services - REST

Pode cortar ou subclipe um vídeo ao codificar-o com um [Job](https://docs.microsoft.com/rest/api/media/jobs). Esta funcionalidade funciona com qualquer [Transform](https://docs.microsoft.com/rest/api/media/transforms) que seja construído utilizando os predefinições [BuiltInStandardEncoderPreset,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou os predefinições [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

O exemplo rest neste tópico cria um trabalho que apara um vídeo ao submeter um trabalho de codificação. 

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos descritos neste tópico, tem de:

- [Criar uma conta Azure Media Services.](create-account-cli-how-to.md)
- [Configure Postman para Azure Media Services REST API chamadas](media-rest-apis-with-postman.md).
    
    Certifique-se de seguir o último passo no tópico [Get Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Criar uma Transformação e um Ativo de saída. Pode ver como criar um Transform e um Output Assets no [Encode um ficheiro remoto baseado em URL e transmitir o vídeo - TUTORIAL REST.](stream-files-tutorial-with-rest.md)
- Reveja o tópico do [conceito de codificação.](encoding-concept.md)

## <a name="create-a-subclipping-job"></a>Criar um trabalho de subclipping

1. Na coleção Postman que descarregou, selecione **Transforms e jobs** -> **Create Job com Sub Clipping**.
    
    O pedido do **PUT** é assim:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Atualize o valor da variável ambiente "transformName" com o seu nome de transformação. 
1. Selecione o separador **Body** e atualize o "myOutputAsset" com o nome do Ativo de saída.

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

    Veja a **Resposta** com a informação sobre o trabalho que foi criado e submetido e o estatuto do trabalho. 

## <a name="next-steps"></a>Passos seguintes

[Como codificar com uma transformação personalizada](custom-preset-rest-howto.md) 
