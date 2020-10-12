---
title: Subclip um vídeo ao codificar com a Azure Media Services REST
description: Este tópico descreve como subclip um vídeo ao codificar com a Azure Media Services usando REST
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: 2a78e8c8c204e5cedeb8bdd2a25cff40a3e5cc44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296554"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Subclip um vídeo ao codificar com os Media Services - REST

Pode aparar ou subclipar um vídeo ao codificar o vídeo usando um [Trabalho](/rest/api/media/jobs). Esta funcionalidade funciona com qualquer [Transform](/rest/api/media/transforms) que seja construído utilizando as predefinições Predefinidas [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições [StandardEncoderPreset.](/rest/api/media/transforms/createorupdate#standardencoderpreset) 

O exemplo REST neste tópico cria um trabalho que apara um vídeo à medida que apresenta um trabalho de codificação. 

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos descritos neste tópico, tem de:

- [Criar uma conta Azure Media Services](./create-account-howto.md).
- [Configure Carteiro para Azure Media Services REST Chamadas API](media-rest-apis-with-postman.md).
    
    Certifique-se de seguir o último passo do tema [Get Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Criar uma Transformação e um Ativo de saída. Pode ver como criar um Transform e uma saída Ativos no [Código de Um ficheiro remoto baseado em URL e transmitir o vídeo -](stream-files-tutorial-with-rest.md) REST tutorial.
- Reveja o tópico do [conceito de codificação.](encoding-concept.md)

## <a name="create-a-subclipping-job"></a>Criar um trabalho de subcrramento

1. Na coleção Do Carteiro que descarregou, selecione **Transforms and jobs**  ->  **Create Job with Sub Clipping**.
    
    O pedido **put** é assim:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Atualize o valor da variável ambiente "transformName" com o seu nome de transformação. 
1. Selecione o **separador 'Corpo'** e atualize o "myOutputAsset" com o nome de Ativo de saída.

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

    Vê a **Resposta** com a informação sobre o trabalho que foi criado e submetido e o estado do trabalho. 

## <a name="next-steps"></a>Passos seguintes

[Como codificar com uma transformação personalizada](custom-preset-rest-howto.md) 
