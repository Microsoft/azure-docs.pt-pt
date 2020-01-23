---
title: Subclipe um vídeo ao codificar com REST dos serviços de mídia do Azure
description: Este tópico descreve como subcortar um vídeo ao codificar com os serviços de mídia do Azure usando REST
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
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514328"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Subclipe um vídeo ao codificar com os serviços de mídia-REST

Você pode cortar ou subcortar um vídeo ao codificá-lo usando um [trabalho](https://docs.microsoft.com/rest/api/media/jobs). Essa funcionalidade funciona com qualquer [transformação](https://docs.microsoft.com/rest/api/media/transforms) criada usando as predefinições [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições de [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) . 

O exemplo de REST neste tópico cria um trabalho que corta um vídeo à medida que envia um trabalho de codificação. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos descritos neste tópico, tem de:

- [Crie uma conta dos serviços de mídia do Azure](create-account-cli-how-to.md).
- [Configurar o Postman para chamar a API de REST de serviços de multimédia do Azure](media-rest-apis-with-postman.md).
    
    Certifique-se de seguir a última etapa no tópico [obter token do Azure ad](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Crie uma transformação e um ativo de saída. Você pode ver como criar uma transformação e um ativo de saída no tutorial [codificar um arquivo remoto baseado em URL e transmitir o vídeo REST](stream-files-tutorial-with-rest.md) .
- Examine o tópico [conceito de codificação](encoding-concept.md) .

## <a name="create-a-subclipping-job"></a>Criar um trabalho de subcorte

1. Na coleção de postmaster que você baixou, selecione **transformações e trabalhos** -> **criar trabalho com subcorte**.
    
    A solicitação **Put** é parecida com esta:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Atualize o valor da variável de ambiente "transformName" com o nome da transformação. 
1. Selecione a guia **corpo** e atualize o "myOutputAsset" com o nome do ativo de saída.

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

    Você verá a **resposta** com as informações sobre o trabalho que foi criado e enviado e o status do trabalho. 

## <a name="next-steps"></a>Passos seguintes

[Como codificar com uma transformação personalizada](custom-preset-rest-howto.md) 
